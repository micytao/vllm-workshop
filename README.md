# vLLM Workshop
[![GitHub Pages](https://img.shields.io/badge/docs-GitHub%20Pages-blue)](https://micytao.github.io/vllm-workshop)

📚 **Hands-on learning powered by [vLLM Playground](https://github.com/micytao/vllm-playground)**

A comprehensive workshop for learning vLLM — the high-performance LLM inference engine — through practical, hands-on exercises.

## 🌐 View the Workshop

**[https://micytao.github.io/vllm-workshop](https://micytao.github.io/vllm-workshop)**

## 📚 What You'll Learn

| Module | Topic | Key Skills |
|--------|-------|------------|
| **Module 1** | Getting Started | Deploy vLLM servers, use chat interface |
| **Module 2** | Structured Outputs | JSON Schema, Regex, Grammar constraints |
| **Module 3** | Tool Calling | Function calling with LLMs |
| **Module 4** | MCP Integration | Agentic AI with human-in-the-loop |
| **Module 5** | Performance Testing | Benchmarking with GuideLLM |

## 🚀 Quick Start

### Prerequisites

- Python 3.10+
- GPU with CUDA support (recommended) or CPU
- Podman or Docker

### Install vLLM Playground

```bash
pip install vllm-playground
vllm-playground pull
vllm-playground
```

Then open http://localhost:7860 and follow the workshop modules!

## 🛠️ Local Development

To run the documentation site locally:

```bash
# Install MkDocs
pip install mkdocs-material mkdocs-glightbox

# Serve locally
mkdocs serve

# Open http://localhost:8000
```

## 📖 Workshop Structure

```
docs/
├── index.md                    # Welcome page
├── overview.md                 # ACME Corporation narrative
├── details.md                  # Requirements & timing
├── workshop/
│   ├── module-01-getting-started.md
│   ├── module-02-structured-outputs.md
│   ├── module-03-tool-calling.md
│   ├── module-04-mcp-integration.md
│   └── module-05-benchmarking.md
└── conclusion.md               # Summary & next steps
```

## 🔗 Related Projects

- **[vLLM Playground](https://github.com/micytao/vllm-playground)** - The tool used in this workshop
- **[vLLM](https://github.com/vllm-project/vllm)** - High-throughput LLM serving engine
- **[GuideLLM](https://github.com/neuralmagic/guidellm)** - Performance benchmarking

## 🤝 Contributing

Contributions welcome! Please feel free to submit issues and pull requests.

## 📝 License

Apache 2.0 License

---

Built with ❤️ for the vLLM community
