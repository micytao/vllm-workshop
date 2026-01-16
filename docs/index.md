# vLLM Workshop

<div class="vllm-playground-callout">
🚀 <strong>Powered by <a href="https://github.com/micytao/vllm-playground">vLLM Playground</a></strong> — A modern web interface for vLLM
</div>

Welcome to the **vLLM Workshop**! This hands-on learning experience will guide you through deploying and using vLLM — the industry-leading high-performance LLM inference engine.

## What You'll Learn

In this workshop, you will:

- ✅ Deploy vLLM servers via container management using Podman
- ✅ Use a modern chat UI with streaming responses to interact with LLMs
- ✅ Implement structured outputs using JSON Schema, Regex, and Grammar
- ✅ Configure and use tool calling with various Open Source LLMs (Qwen, Llama, Mistral, etc.)
- ✅ Set up MCP (Model Context Protocol) servers for agentic capabilities
- ✅ Run performance benchmarks with GuideLLM

## Who This Is For

This workshop is designed for **Developers**, **AI Engineers**, **Platform Engineers**, and **Architects** who want to set up a complete AI inference environment with tool calling and MCP integration.

**Experience level**: All levels — Beginner, Intermediate, and Advanced

## Prerequisites

Before starting this workshop, you should have:

- Basic knowledge of Linux command line
- Basic understanding of [vLLM](https://github.com/vllm-project/vllm), a high-throughput and memory-efficient inference engine
- Familiarity with containers (Podman or Docker)
- Basic understanding of AI inferencing concepts

## Environment Setup

### Hardware Requirements

| Component | Minimum | Recommended |
|-----------|---------|-------------|
| **GPU** | NVIDIA GPU with 8GB VRAM | NVIDIA GPU with 16GB+ VRAM |
| **RAM** | 16GB | 32GB+ |
| **Storage** | 50GB free | 100GB+ free |

!!! tip "CPU Mode Available"
    No GPU? You can still complete most exercises using CPU mode, though inference will be slower.

### Software Requirements

- Python 3.10 or later
- Podman 4.0+ or Docker
- NVIDIA GPU drivers and CUDA (for GPU mode)
- Modern web browser (Chrome, Firefox, Safari, Edge)

### Install vLLM Playground

```bash
# Install from PyPI
pip install vllm-playground

# Pre-download container image (~10GB for GPU)
vllm-playground pull

# Start the playground
vllm-playground
```

Open [http://localhost:7860](http://localhost:7860) and you're ready to begin!

## Let's Get Started!

Click on **[Workshop Overview](overview.md)** in the navigation to begin your learning journey with vLLM!

---

<div style="text-align: center; margin-top: 2rem;">
⭐ <strong>Like this workshop?</strong> Star <a href="https://github.com/micytao/vllm-playground">vLLM Playground</a> on GitHub!
</div>
