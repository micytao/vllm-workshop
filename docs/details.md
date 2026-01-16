# Before You Begin

## Choose Your Path

This content is available in two formats:

| Path | Description | Best For |
|------|-------------|----------|
| **[Workshop](workshop/module-01-getting-started.md)** | Hands-on, self-paced learning with detailed step-by-step instructions | Individual learners working through the material on their own |
| **[Demo](demo/module-01-getting-started.md)** | Presentation-ready format with ACME Corporation business scenario | Instructors, presenters, or those following along with a live demo |

Both paths cover the same 5 modules but with different contexts:

- **Workshop**: Focus on learning vLLM Playground features at your own pace
- **Demo**: Business-focused narrative showing how ACME Corporation transforms their customer support with AI

---

## Timing and Schedule

### Full Workshop (90 minutes)

| Module | Topic | Duration |
|--------|-------|----------|
| **Module 1** | Getting Started with vLLM Playground | 18 minutes |
| **Module 2** | Advanced Inferencing: Structured Outputs | 18 minutes |
| **Module 3** | Advanced Inferencing: Tool Calling | 18 minutes |
| **Module 4** | Advanced Inferencing: MCP Integration | 18 minutes |
| **Module 5** | Performance Testing | 18 minutes |

### Quick Start Workshop (36 minutes)

If you're short on time, focus on these modules:

| Module | Topic | Duration |
|--------|-------|----------|
| **Module 1** | Getting Started with vLLM Playground | 18 minutes |
| **Module 5** | Performance Testing | 18 minutes |

## Technical Requirements

### Software Versions

| Component | Version |
|-----------|---------|
| vLLM Playground | v0.1.1+ |
| vLLM | v0.11.0+ |
| Podman | 4.0+ (or Docker) |
| Python | 3.10+ (required for MCP) |
| NVIDIA GPU | CUDA support |
| Web browser | Chrome, Firefox, Safari, Edge |

### Environment Access

After installing vLLM Playground, you will have access to:

| Resource | URL |
|----------|-----|
| vLLM Playground Web UI | [http://localhost:7860](http://localhost:7860) |
| vLLM API Endpoint | [http://localhost:8000](http://localhost:8000) |

## Environment Setup

### Quick Setup

```bash
# 1. Install vLLM Playground
pip install vllm-playground

# 2. Pull the GPU container image (~10GB)
vllm-playground pull

# 3. Start the playground
vllm-playground

# 4. Open http://localhost:7860 in your browser
```

### What's Included

| Component | Status |
|-----------|--------|
| vLLM Playground CLI | ✅ Installed via pip |
| Podman/Docker | ⚠️ Required (install separately) |
| NVIDIA GPU drivers | ⚠️ Required for GPU mode |
| Python 3.10+ | ⚠️ Required for MCP support |

### Optional Packages

The following packages can be installed for additional functionality:

| Package | Install Command | Purpose |
|---------|-----------------|---------|
| MCP Client | `pip install mcp` or `pip install vllm-playground[mcp]` | Required for Module 4 |
| GuideLLM | `pip install guidellm` or `pip install vllm-playground[benchmark]` | Required for Module 5 |

!!! tip "Pre-install Everything"
    To install all optional dependencies at once:
    ```bash
    pip install vllm-playground[mcp,benchmark]
    ```

### Setup Validation

Run these commands to verify your environment:

```bash
# Verify vLLM Playground installation
vllm-playground --help

# Verify Podman (or use 'docker version')
podman version

# Verify GPU availability (if using GPU)
nvidia-smi

# Check Python version (for MCP)
python3 --version

# Verify MCP installation (optional - for Module 4)
python3 -c "import mcp; print('MCP installed successfully')"

# Verify GuideLLM installation (optional - for Module 5)
guidellm --help
```

## Troubleshooting Guide

### Common Setup Issues

| Problem | Solution |
|---------|----------|
| `vllm-playground: command not found` | Verify the installation path is in your PATH, or reinstall with `pip install vllm-playground` |
| `Permission denied` when running Podman | Ensure rootless Podman is configured, or use `sudo` |
| `NVIDIA driver not found` or GPU not detected | Install NVIDIA drivers and verify with `nvidia-smi` |
| Container image pull fails | Check network connectivity and container registry access |
| Port 7860 already in use | Run `vllm-playground stop` or use `--port` to specify a different port |

### During Workshop Support

```bash
# Check vLLM Playground logs
vllm-playground status

# View container logs
podman logs vllm-service

# Restart if needed
vllm-playground stop
vllm-playground
```

## Follow-up Resources

### After the Workshop

- 📦 [vLLM Playground GitHub Repository](https://github.com/micytao/vllm-playground) — Source code and documentation
- 📚 [vLLM Project](https://github.com/vllm-project/vllm) — The underlying high-performance inference engine
- 📊 [GuideLLM Documentation](https://github.com/vllm-project/guidellm) — Performance benchmarking tool
- 🔗 [Model Context Protocol](https://modelcontextprotocol.io) — MCP specification and servers

### Additional Learning Paths

| Level | Focus Area |
|-------|------------|
| **Intermediate** | Explore different model architectures and their tool calling capabilities |
| **Advanced** | Deploy vLLM Playground on OpenShift/Kubernetes for enterprise scale |
| **Production** | Implement custom MCP servers for your specific use cases |

## Authors and Contributors

**Primary Author**: Michael Yang  
**Last Updated**: January 2026  
**Workshop Version**: 1.0

**Feedback and Questions**:

- Workshop issues: [GitHub Issues](https://github.com/micytao/vllm-workshop/issues)
- vLLM Playground issues: [GitHub Issues](https://github.com/micytao/vllm-playground/issues)

---

## Ready to Start?

Choose your learning path:

- 📚 **Workshop Path**: [Module 1: Getting Started](workshop/module-01-getting-started.md) — Self-paced, hands-on learning
- 🎬 **Demo Path**: [Module 1: Getting Started](demo/module-01-getting-started.md) — Presentation-ready with ACME business scenario
