---
date: '2026-04-04T14:06:34+08:00'
draft: false
title: 'SGLang实验安装'
---

尝试过在wsl中安装vLLM失败后，便转向安装SGlang。

第一步：在wsl中新建一个conda环境

第二部：下载SGLang源码

第三步：将sglang安装到环境里

```bash
pip install -e "python[all]"
```

第一次运行需要下载很多依赖项，不过过程一切顺利。

第四步：

启动服务器。最初计划是使用Qwen3.5-4B：

```bash
python -m sglang.launch_server \
  --model-path Qwen/Qwen3.5-4B \
  --port 8000 \
  --tp-size 1 \
  --mem-fraction-static 0.85 \
  --context-length 32768 \
  --reasoning-parser qwen3
```

要把整个模型文件（8G左右）下载到本地，需要一点时间。

（模型文件下载到了~./cache/huggingface/hub里面）

下载完开始运行，然后就喜闻乐见地爆显存了。解决办法是换成Qwen3.5-2B，并调整一下参数（AI给的）

```bash
python -m sglang.launch_server \
  --model-path Qwen/Qwen3.5-2B \
  --tp-size 1 \
  --mem-fraction-static 0.70 \
  --context-length 4096 \
  --chunked-prefill-size 2048 \
  --max-running-requests 1 \
  --cuda-graph-max-bs 4 \
  --dtype bfloat16
```

最后遇到的一个问题，还是跟那块RTX5060（万恶之源）有关。以下是两种报错：
```
Exception: Capture cuda graph failed: FlashInfer requires GPUs with sm75 or higher
nvcc fatal   : Unsupported gpu architecture 'compute_120'
```
根本原因是，RTX5060太新了，要使用CUDA13.1+版本；而不知为何我的wsl里装的是CUDA12.4版本，导致各个软件都认不出新的硬件。

解决方法是：从NVIDIA官网安装cuda-toolkit13.1：
```bash
wget https://developer.download.nvidia.com/compute/cuda/13.1.0/local_installers/cuda_13.1.0_590.44.01_linux.run
sudo sh cuda_13.1.0_590.44.01_linux.run
```

并在.bashrc末尾设置一下环境变量就好了。

## 最终的执行指令

先设置一下HF Mirror（不知为何使用本地模型也要联网验证）：

```bash
export HF_ENDPOINT=https://hf-mirror.com
```

**1. 开启服务器（跟上面那个一样）：**

```bash
python -m sglang.launch_server \
  --model-path Qwen/Qwen3.5-2B \
  --tp-size 1 \
  --mem-fraction-static 0.70 \
  --context-length 4096 \
  --chunked-prefill-size 2048 \
  --max-running-requests 1 \
  --cuda-graph-max-bs 4 \
  --dtype bfloat16
```

其中附加参数的意思：
```
--tp-size 1                   张量并行大小为 1，表示不使用张量并行（单 GPU 运行）
--mem-fraction-static 0.70    静态内存分配比例为 70%，预留 30% 内存用于动态分配
--context-length 4096         模型支持的最大上下文长度为 4096 tokens
--chunked-prefill-size 2048   分块预填充大小为 2048，将长输入分块处理以降低延迟
--max-running-requests 1      最大并发运行请求数为 1，同时只处理一个请求（单用户模式）
--cuda-graph-max-bs 4         CUDA Graph 最大批处理大小为 4，用于优化小批量推理性能
--dtype bfloat16              模型权重和计算的数据类型为 bfloat16
```

更多服务器参数：[https://docs.sglang.com.cn/advanced_features/server_arguments.html](https://docs.sglang.com.cn/advanced_features/server_arguments.html)

执行后，会输出一大堆内容。最后能够看到这段令人愉悦的消息：

```
[2026-04-04 23:16:25] INFO:     Started server process [10986]
[2026-04-04 23:16:25] INFO:     Waiting for application startup.
[2026-04-04 23:16:25] INFO:     Application startup complete.
[2026-04-04 23:16:25] INFO:     Uvicorn running on http://127.0.0.1:30000 (Press CTRL+C to quit)
[2026-04-04 23:16:26] INFO:     127.0.0.1:45298 - "GET /model_info HTTP/1.1" 200 OK
[2026-04-04 23:16:38] Prefill batch, #new-seq: 1, #new-token: 82, #cached-token: 0, full token usage: 0.00, mamba usage: 0.15, #running-req: 0, #queue-req: 0, cuda graph: False, input throughput (token/s): 0.00
[2026-04-04 23:16:38] INFO:     127.0.0.1:45302 - "POST /v1/chat/completions HTTP/1.1" 200 OK
[2026-04-04 23:16:38] The server is fired up and ready to roll!
```

VS Code 右下角会弹窗提示“Your application running on port 30000 is available.”

**2. 执行推理**

可以直接在另一个控制台发送请求：
```bash
curl http://127.0.0.1:30000/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{
    "model": "default",
    "messages": [
      {"role": "user", "content": "who are you?"}
    ],
    "temperature": 0.7,
    "max_tokens": 512
  }'
```

也可以通过python程序，使用OpenAI API：
```python
from openai import OpenAI

client = OpenAI(
    base_url="http://127.0.0.1:30000/v1",
    api_key="None"  # SGLang 不需要真实 API key
)

response = client.chat.completions.create(
    model="None",  # 模型名称，如果启动sglang时指定了模型，就会无视该字段
    messages=[
        {"role": "user", "content": "who are you?"}
    ],
    temperature=0.7,
    max_tokens=512
)

print(response.choices[0].message.content)
```

## 参考

启动服务/发送请求的更多方法：[https://docs.sglang.com.cn/basic_usage/send_request.html](https://docs.sglang.com.cn/basic_usage/send_request.html)
