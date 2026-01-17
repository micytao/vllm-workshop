# 开始之前

## 选择你的路径

本内容提供两种格式：

| 路径 | 描述 | 最适合 |
|------|------|--------|
| **[实验](workshop/module-01-getting-started.md)** | 动手实践、自主学习，包含详细的分步说明 | 独自学习材料的个人学习者 |
| **[演示](demo/module-01-getting-started.md)** | 演示就绪格式，包含 ACME 公司业务场景 | 讲师、演示者或跟随现场演示的人员 |

两条路径涵盖相同的 5 个模块，但背景不同：

- **实验**：专注于按自己的节奏学习 vLLM Playground 功能
- **演示**：以业务为中心的叙述，展示 ACME 公司如何用 AI 改造客户支持

---

## 时间和日程

### 📚 实验路径（90 分钟）

自主学习，动手实践，包含详细的分步说明。

| 模块 | 主题 | 时长 |
|------|------|------|
| **模块 1** | vLLM Playground 快速入门 | 18 分钟 |
| **模块 2** | 高级推理：结构化输出 | 18 分钟 |
| **模块 3** | 高级推理：工具调用 | 18 分钟 |
| **模块 4** | 高级推理：MCP 集成 | 18 分钟 |
| **模块 5** | 性能测试 | 18 分钟 |

### 🎬 演示路径（45 分钟）

演示就绪格式，适用于现场演示。

| 模块 | 主题 | 时长 |
|------|------|------|
| **模块 1** | vLLM Playground 快速入门 | 8 分钟 |
| **模块 2** | 高级推理：结构化输出 | 10 分钟 |
| **模块 3** | 高级推理：工具调用 | 10 分钟 |
| **模块 4** | 高级推理：MCP 集成 | 10 分钟 |
| **模块 5** | 性能测试 | 7 分钟 |

### 快速入门（20-36 分钟）

如果时间有限，请专注于以下模块：

| 路径 | 模块 | 时长 |
|------|------|------|
| **实验** | 模块 1 + 模块 5 | 36 分钟 |
| **演示** | 模块 1 + 模块 5 | 15 分钟 |

## 技术要求

### 软件版本

| 组件 | 版本 |
|------|------|
| vLLM Playground | v0.1.1+ |
| vLLM | v0.11.0+ |
| Podman | 4.0+（或 Docker） |
| Python | 3.10+（MCP 需要） |
| NVIDIA GPU | CUDA 支持 |
| Web 浏览器 | Chrome、Firefox、Safari、Edge |

### 环境访问

安装 vLLM Playground 后，你将可以访问：

| 资源 | URL |
|------|-----|
| vLLM Playground Web UI | [http://localhost:7860](http://localhost:7860) |
| vLLM API 端点 | [http://localhost:8000](http://localhost:8000) |

## 环境设置

### 快速设置

```bash
# 1. 安装 vLLM Playground
pip install vllm-playground

# 2. 拉取 GPU 容器镜像（约 10GB）
vllm-playground pull

# 3. 启动 playground
vllm-playground

# 4. 在浏览器中打开 http://localhost:7860
```

### 包含内容

| 组件 | 状态 |
|------|------|
| vLLM Playground CLI | ✅ 通过 pip 安装 |
| Podman/Docker | ⚠️ 需要（单独安装） |
| NVIDIA GPU 驱动 | ⚠️ GPU 模式需要 |
| Python 3.10+ | ⚠️ MCP 支持需要 |

### 可选包

以下包可安装以获得额外功能：

| 包 | 安装命令 | 用途 |
|----|----------|------|
| MCP Client | `pip install mcp` 或 `pip install vllm-playground[mcp]` | 模块 4 需要 |
| GuideLLM | `pip install guidellm` 或 `pip install vllm-playground[benchmark]` | 模块 5 需要 |

!!! tip "预安装所有内容"
    一次性安装所有可选依赖：
    ```bash
    pip install vllm-playground[mcp,benchmark]
    ```

### 设置验证

运行以下命令验证你的环境：

```bash
# 验证 vLLM Playground 安装
vllm-playground --help

# 验证 Podman（或使用 'docker version'）
podman version

# 验证 GPU 可用性（如果使用 GPU）
nvidia-smi

# 检查 Python 版本（用于 MCP）
python3 --version

# 验证 MCP 安装（可选 - 用于模块 4）
python3 -c "import mcp; print('MCP installed successfully')"

# 验证 GuideLLM 安装（可选 - 用于模块 5）
guidellm --help
```

## 故障排除指南

### 常见设置问题

| 问题 | 解决方案 |
|------|----------|
| `vllm-playground: command not found` | 验证安装路径是否在 PATH 中，或使用 `pip install vllm-playground` 重新安装 |
| 运行 Podman 时 `Permission denied` | 确保配置了 rootless Podman，或使用 `sudo` |
| `NVIDIA driver not found` 或 GPU 未检测到 | 安装 NVIDIA 驱动并使用 `nvidia-smi` 验证 |
| 容器镜像拉取失败 | 检查网络连接和容器仓库访问 |
| 端口 7860 已被占用 | 运行 `vllm-playground stop` 或使用 `--port` 指定不同端口 |

### 实验期间支持

```bash
# 检查 vLLM Playground 日志
vllm-playground status

# 查看容器日志
podman logs vllm-service

# 如需重启
vllm-playground stop
vllm-playground
```

## 后续资源

### 实验后

- 📦 [vLLM Playground GitHub 仓库](https://github.com/micytao/vllm-playground) — 源代码和文档
- 📚 [vLLM 项目](https://github.com/vllm-project/vllm) — 底层高性能推理引擎
- 📊 [GuideLLM 文档](https://github.com/vllm-project/guidellm) — 性能基准测试工具
- 🔗 [模型上下文协议](https://modelcontextprotocol.io) — MCP 规范和服务器

### 额外学习路径

| 级别 | 重点领域 |
|------|----------|
| **中级** | 探索不同的模型架构及其工具调用能力 |
| **高级** | 在 OpenShift/Kubernetes 上部署 vLLM Playground 以实现企业规模 |
| **生产** | 为你的特定用例实现自定义 MCP 服务器 |

## 作者和贡献者

**主要作者**：Michael Yang  
**最后更新**：2026 年 1 月  
**实验版本**：1.0

**反馈和问题**：

- 实验问题：[GitHub Issues](https://github.com/micytao/vllm-workshop/issues)
- vLLM Playground 问题：[GitHub Issues](https://github.com/micytao/vllm-playground/issues)

---

## 准备开始？

选择你的学习路径：

- 📚 **实验路径**：[模块 1：快速入门](workshop/module-01-getting-started.md) — 自主学习，动手实践
- 🎬 **演示路径**：[模块 1：快速入门](demo/module-01-getting-started.md) — 演示就绪，包含 ACME 业务场景
