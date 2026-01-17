# vLLM 实验

<div class="vllm-playground-callout">
🚀 <strong>由 <a href="https://github.com/micytao/vllm-playground">vLLM Playground</a> 提供支持</strong> — vLLM 的现代化 Web 界面
</div>

欢迎参加 **vLLM 实验**！这是一个动手实践的学习体验，将引导您部署和使用 vLLM —— 业界领先的高性能 LLM 推理引擎。

## 您将学到什么

在本实验中，您将：

- ✅ 使用 Podman 通过容器管理部署 vLLM 服务器
- ✅ 使用具有流式响应的现代聊天界面与 LLM 交互
- ✅ 使用 JSON Schema、Regex 和 Grammar 实现结构化输出
- ✅ 配置和使用各种开源 LLM（Qwen、Llama、Mistral 等）的工具调用
- ✅ 设置 MCP（模型上下文协议）服务器以实现智能体功能
- ✅ 使用 GuideLLM 运行性能基准测试

## 适用人群

本实验专为希望建立具有工具调用和 MCP 集成的完整 AI 推理环境的**开发人员**、**AI 工程师**、**平台工程师**和**架构师**设计。

**经验水平**：所有级别 — 初级、中级和高级

## 前提条件

在开始本实验之前，您应该具备：

- Linux 命令行的基本知识
- 对 [vLLM](https://github.com/vllm-project/vllm) 的基本了解，这是一个高吞吐量和内存高效的推理引擎
- 熟悉容器（Podman 或 Docker）
- 对 AI 推理概念的基本理解

## 环境设置

### 硬件要求

| 组件 | 最低配置 | 推荐配置 |
|------|----------|----------|
| **GPU** | 8GB 显存的 NVIDIA GPU | 16GB+ 显存的 NVIDIA GPU |
| **内存** | 16GB | 32GB+ |
| **存储** | 50GB 可用空间 | 100GB+ 可用空间 |

!!! tip "支持 CPU 模式"
    没有 GPU？您仍然可以使用 CPU 模式完成大部分练习，但推理速度会较慢。

### 软件要求

- Python 3.10 或更高版本
- Podman 4.0+ 或 Docker
- NVIDIA GPU 驱动程序和 CUDA（用于 GPU 模式）
- 现代 Web 浏览器（Chrome、Firefox、Safari、Edge）

### 安装 vLLM Playground

```bash
# 从 PyPI 安装
pip install vllm-playground

# 预下载容器镜像（GPU 版本约 10GB）
vllm-playground pull

# 启动 playground
vllm-playground
```

打开 [http://localhost:7860](http://localhost:7860)，您就可以开始了！

## 开始学习！

点击导航中的 **[概述](overview.md)** 开始您的 vLLM 学习之旅！

---

<div style="text-align: center; margin-top: 2rem;">
⭐ <strong>喜欢这个实验？</strong> 在 GitHub 上给 <a href="https://github.com/micytao/vllm-playground">vLLM Playground</a> 点个星！
</div>
