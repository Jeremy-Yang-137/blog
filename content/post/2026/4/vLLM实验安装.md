---
date: '2026-04-03T20:19:29+08:00'
draft: false
title: 'vLLM实验安装'
---

最初想把vLLM安装在自己的电脑上（wsl中），用来自己进行一些简单的大模型推理实验。一是想学习一下vLLM的架构，二是想自己体会一下大模型推理中的资源管理。

## 1. 本地安装

理论上，我的笔记本配置是Ultra7 255HX + RTX5060 8GB，是可以运行8B~12B的量化版模型的。于是：

第一步：安装wsl子系统

第二步：新建一个conda环境

第三步：安装torch、cuda-toolkit等依赖

第四步：下载vLLM源码，然后将vllm编译安装到环境里

```bash
pip install -e .
```

接着，先后遇到了以下问题：

vLLM安装的时候会自动从Github下载Triton，但这个过程总是会中断（可能是网络问题？）。于是我直接从它Github仓库的Release下载了压缩包，解压到本地

```bash
wget --tries=0 --continue https://github.com/triton-lang/triton/archive/refs/tags/v3.6.0.tar.gz
tar -xzf v3.6.0.tar.gz
```

并修改vLLM中相应的CMake文件，让它直接从本地找而不是从Github下载

```cmake
set(TRITON_LOCAL_PATH "/root/tmp/triton-3.6.0")
message (STATUS "[triton_kernels] Use local source: ${TRITON_LOCAL_PATH}")
FetchContent_Declare(
    triton_kernels
    SOURCE_DIR ${TRITON_LOCAL_PATH}
    SOURCE_SUBDIR python/triton_kernels/triton_kernels
)
```

然后又出现了一个问题：编译了四五个文件后，vscode和wsl的连接直接丢失了。可能是由于wsl占用内存过高导致它被系统制裁了。解决方法是：

```bash
export MAX_JOBS=2
```

这样可以限制同时进行的任务，减少内存占用。

另外，安装时可以在最后加上-v参数，能够输出更多详细步骤，可以提供不小的心理安慰：

```bash
pip install -e . -v
```

然而，最后遇到了一个奇怪的问题。编译过程中突然连续报了几百行错误，样子都差不多：

```
ptxas /tmp/tmpxft_0000042a_00000000-6_paged_attention_v2.ptx, line 2559513; error   : 
Feature 'f16 arithemetic and compare instructions' requires .target sm_53 or higher
```

应该跟CUDA版本有关。Kimi刚开始以为是我的显卡太旧，以至于vLLM不支持。排查后发现其实是我的显卡太新了，vLLM识别不到。

所以，我打算转移到Autodl算力云上面编译vLLM。

## 2. Autodl安装

前面都一切顺利。

然而，在编译时又出现了一个新的报错，提示无法写入到/tmp/...之类的。

原因是，编译时会将所有的临时文件都写到/tmp下。而我租的那台主机的存储分配是这样的：
```
存储：
  系 统 盘/               ：57% 18G/30G  （/tmp在这里面）
  数 据 盘/root/autodl-tmp：20% 9.8G/50G
  文件存储/root/autodl-fs ：1% 41M/200G
```
系统盘本来就不大，/tmp很快就满了。

所以方法很简单，让系统将临时文件存到50G的数据盘里就行了：
```bash
export TMPDIR=/root/autodl-tmp
```
我还遇到过另一个报错：
```
<built-in>: fatal error: when writing output to /tmp/tmpxft_00003496_00000000-7_fused_quantize_mx_sm100.cpp1.ii: No such file or directory
  compilation terminated.
```
可能是太多线程同时访问/tmp导致冲突了？总之只要设置一下MAX_JOBS就行了。

然而，最后还是会报各种莫名其妙的错误。由于以下两个理由：
1. 编译vLLM的时间成本巨大，从执行`pip install -e .`到编译开始已经需要4~5分钟，其中有概率出现网络问题；开始编译后需要约半个小时，有概率出现存储容量等问题
2. 了解到，SGLang采用预编译内核，可以节省大量的时间

于是我决定转为使用SGLang做实验。
