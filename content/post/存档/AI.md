---
date: '2025-12-01T00:00:10+08:00'
draft: true
title: 'AI'
---

# OpenAI API格式

[OpenAI API格式详解-Chat Completions – 知乎](https://zhuanlan.zhihu.com/p/692336625)

# VS Code接入大模型

使用插件：Cline

- Deepseek：选择Deepseek，模型选择deepseek-chat，需提供API Key
- Qwen：选择OpenAI Compatible，Base URL为https://dashscope.aliyuncs.com/compatible-model/v1 ，模型选择qwen-flash，也需提供API Key

注意：在比较大的项目上工作时，token消耗可能较多

# 技巧

关键：提供尽可能多的信息

- 提供操作系统、软件环境等信息，并简要介绍目前代码的思路以及预期效果
- 使用AI画图（豆包seedream）时，详细描述画面的构图、风格、色调、每一处细节，以及画面传达的感觉（可以文艺一点）。说少了只会让AI对不清楚的地方自由发挥，达不到想要的效果。豆包生图网站有不少实例，可以参考。
- 有时候，让AI提供问题相关的网页/论文会比直接向它提问的结果要更准确
- 直接告诉AI该做什么，而非从反面告诉它不该做什么

# 不同AI的区别

### Deepseek
吐字比较慢，生成文本的长度一般比较长，内容一般比较详细。

注意：图片只能识别文字，因此不能拿来做电路题目（正确率只有20%左右）

### 豆包
吐字比较快，生成文本的长度较短且灵活，但有些时候会过于简略，需要不断追问。无法控制是否开启联网搜索。

不知道是怎样识图的，因为电路题目正确率也没有高很多，更多时候需要口述电路。
