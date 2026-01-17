# 模块 1：vLLM Playground 快速入门

ACME 公司正在启动他们的 AI 客户支持计划，你被委派评估 vLLM 作为其推理基础设施的基础。vLLM 是业界领先的高吞吐量和内存高效推理引擎，而 **vLLM Playground** 提供了一个现代化的 Web 界面来管理和与 vLLM 服务器交互。

在本模块中，你将验证环境、使用 Podman 部署你的第一个 vLLM 服务器，并亲身体验聊天界面。

## 学习目标

完成本模块后，你将能够：

- ✅ 验证 vLLM Playground 安装和环境就绪状态
- ✅ 使用 Podman 容器拉取和部署 vLLM 服务器
- ✅ 浏览 vLLM Playground Web 界面
- ✅ 通过聊天 UI 与 LLM 交互，体验流式响应

---

## 练习 1：验证你的环境

ACME 需要确保 AI 基础设施在继续之前已正确配置。让我们首先验证 vLLM Playground 和所有依赖项是否正确安装。

### 前提条件

- 已安装 vLLM Playground（`pip install vllm-playground`）
- 已安装 Podman 或 Docker
- 支持 CUDA 的 GPU（推荐）或 CPU

### 步骤

1. **验证 vLLM Playground 已安装：**

    ```bash
    vllm-playground --help
    ```

    预期输出：
    ```
    usage: vllm-playground [-h] [--port PORT] [--host HOST] {pull,stop,status} ...

    vLLM Playground - A modern web interface for vLLM

    positional arguments:
      {pull,stop,status}
        pull              Pre-download container images
        stop              Stop running vLLM Playground instance
        status            Check status of vLLM Playground

    optional arguments:
      -h, --help          show this help message and exit
      --port PORT         Port to run on (default: 7860)
      --host HOST         Host to bind to (default: 0.0.0.0)
    ```

2. **检查 Podman 是否可用：**

    ```bash
    podman version
    ```

    你应该看到 Podman 版本 4.0 或更高版本。（如果使用 Docker，则使用 `docker version`）

3. **验证 GPU 可用性（如果使用 GPU 模式）：**

    ```bash
    nvidia-smi
    ```

    你应该看到你的 NVIDIA GPU 列表以及驱动程序和 CUDA 信息。

4. **检查 vLLM Playground 的当前状态：**

    ```bash
    vllm-playground status
    ```

    这显示是否有任何 vLLM 实例正在运行。

### ✅ 验证

确认所有检查通过：

- [x] `vllm-playground --help` 显示使用信息
- [x] `podman version` 显示版本 4.0+
- [x] `nvidia-smi` 显示 GPU 信息（如果使用 GPU）
- [x] 环境已准备好进行 vLLM 部署

---

## 练习 2：部署你的第一个 vLLM 服务器

环境验证完成后，ACME 准备部署他们的第一个 LLM。你将拉取必要的容器镜像并启动一个带有适合客户支持场景的模型的 vLLM 服务器。

### 前提条件

- 练习 1 成功完成
- 检测到并可用 GPU（或 CPU 模式）

### 步骤

1. **预下载 GPU 容器镜像**（这可能需要几分钟）：

    ```bash
    vllm-playground pull
    ```

    这将下载 vLLM GPU 容器镜像（约 10GB）。你将看到下载层的进度指示器。

    !!! tip "CPU 模式"
        如果你在仅 CPU 的环境中，请改用 `vllm-playground pull --cpu`。

2. **启动 vLLM Playground：**

    ```bash
    vllm-playground
    ```

3. **访问 Web UI**，在浏览器中打开：

    ```
    http://localhost:7860
    ```

    ![vLLM Playground 主页](../assets/images/module-01-figure-01.png)

4. **在 vLLM Playground Web 界面中，配置你的第一个模型：**

    - 导航到 **Server Configuration** 部分
    - 在 **Model** 部分，你有几个选项：
        - **下拉列表**：从预配置的模型中选择，如 `TinyLlama 1.1B Chat (Fast, No token)` — 非常适合快速测试
        - **自定义模型名称**：在文本字段中输入任何 HuggingFace 模型 ID
        - **浏览社区配方**：探索社区贡献的模型配置
        - **HuggingFace Token**：仅受限模型需要（Llama 3.1、Llama 3.2 等）

    对于本练习，从下拉列表中选择 `TinyLlama 1.1B Chat` — 它快速且不需要身份验证。

    ![模型选择](../assets/images/module-01-figure-02.png)

5. **配置运行模式：**

    - **Subprocess**：直接作为 Python 子进程运行 vLLM（需要预安装 vLLM）
    - **Container**：在隔离的 Podman 容器中运行 vLLM（推荐）

    选择 **Container** 模式以获得更好的隔离。

    ![运行模式选择](../assets/images/module-01-figure-03.png)

6. **配置计算模式：**

    - **CPU**：用于没有 GPU 的系统（推理较慢）
    - **GPU**：用于 NVIDIA GPU 加速（推荐）

    如果可用，选择 **GPU** 模式。

    !!! warning "GPU 模式需要 Root 权限"
        运行具有 GPU 访问权限的容器需要 root 权限。选择 GPU 模式时，vLLM 容器将使用 `sudo` 启动。

    ![计算模式选择](../assets/images/module-01-figure-04.png)

7. **查看底部的命令预览** — 它显示将运行的确切 vLLM 命令。

    ![命令预览](../assets/images/module-01-figure-05.png)

8. **点击 "Start Server"**

9. **等待模型加载。** 你可以在界面中监控进度或检查容器日志：

    ```bash
    sudo podman logs -f vllm-service
    ```

    !!! note "GPU 访问需要 Sudo"
        你可能需要 `sudo podman logs -f vllm-service`，因为容器使用 GPU 资源。

### ✅ 验证

查找这些表明服务器已就绪的指标：

![服务器就绪](../assets/images/module-01-figure-06.png)

- [x] **Server Logs** 面板显示 `Application startup complete.`
- [x] 出现绿色的 **"Server is ready to chat!"** 提示通知
- [x] 聊天界面中的 **Send** 按钮变为绿色

你也可以验证容器正在运行：

```bash
sudo podman ps
```

- [x] 容器 `vllm-service` 正在运行
- [x] 状态显示 "healthy" 或 "Up"

### 故障排除

??? failure "容器启动失败，显示 'out of memory'"
    **解决方案：**
    
    1. 检查 GPU 内存：`nvidia-smi`
    2. 尝试更小的模型或在设置中调整 GPU 内存利用率
    3. 使用 `--gpu-memory-utilization 0.8` 设置 80% GPU 内存使用率

??? failure "'Model not found' 错误"
    **解决方案：**
    
    1. 验证模型 ID 是否正确（检查 HuggingFace）
    2. 对于受限模型（Llama、Gemma），确保你有访问权限并设置 `HF_TOKEN`
    3. 尝试公共模型，如 `TinyLlama/TinyLlama-1.1B-Chat-v1.0`

??? failure "端口 7860 已被占用"
    **解决方案：**
    ```bash
    vllm-playground stop
    vllm-playground --port 8080
    ```

---

## 练习 3：与你的 LLM 交互

现在 vLLM 服务器正在运行，你将探索聊天界面并与模型进行第一次对话。这模拟了 ACME 的客户支持系统将如何与用户交互。

### 步骤

1. **在 vLLM Playground Web UI 中，导航到 Chat 部分。**

    你会看到一个 ChatGPT 风格的界面，包含：
    
    - 底部的消息输入区域
    - 主面板中的对话历史
    - 侧边栏中的模型设置

    ![聊天界面](../assets/images/module-01-figure-07.png)

2. **发送你的第一条消息来测试模型：**

    ```
    Hello! Can you introduce yourself and explain what you can help me with?
    ```

    观察流式响应 — 文本在模型生成时逐字出现。

3. **为 ACME 测试一个客户支持场景：**

    ```
    I'm a customer support agent. A customer is asking about their order status. 
    How should I professionally respond if their order is delayed by 2 days?
    ```

    注意模型如何为支持场景提供有用的指导。

4. **观察聊天界面右侧的响应指标面板：**

    | 指标 | 描述 |
    |------|------|
    | **Prompt Tokens** | 你输入的 token 数量 |
    | **Completion Tokens** | 模型生成的 token 数量 |
    | **Total Tokens** | 输入和输出 token 的总和 |
    | **Time Taken** | 总响应生成时间 |
    | **Tokens/sec** | 生成吞吐率 |

    这些指标突出显示了 vLLM 提供的高吞吐量。

    ![响应指标](../assets/images/module-01-figure-08.png)

5. **探索聊天设置：**

    - **Temperature**：控制随机性（越低 = 越确定性）
    - **Max Tokens**：限制响应长度
    - **System Prompt**：设置模型的角色/行为

    尝试设置系统提示：

    ```
    You are a helpful customer support assistant for ACME Corporation. 
    Be professional, empathetic, and solution-oriented.
    ```

    !!! tip "模板"
        点击 **Templates** 下拉菜单访问常见用例的预设系统提示模板。

    ![系统提示](../assets/images/module-01-figure-09.png)

6. **在系统提示激活的情况下发送另一条消息：**

    ```
    A customer is frustrated because their product arrived damaged. What should I say?
    ```

    注意响应现在如何与客户支持角色保持一致。

### ✅ 验证

确认你可以与模型交互：

- [x] 消息发送成功
- [x] 响应实时流式传输（逐字）
- [x] 系统提示影响模型行为
- [x] 不同的温度设置改变响应风格

### 故障排除

??? failure "聊天消息没有得到响应"
    **解决方案：**
    
    1. 验证服务器正在运行：检查 UI 中的状态指示器
    2. 检查容器日志：`podman logs vllm-service`
    3. 停止服务器并重新启动

??? failure "响应非常慢"
    **解决方案：**
    
    1. 检查 GPU 利用率：`nvidia-smi`
    2. 验证模型加载到 GPU（而非 CPU）
    3. 考虑使用更小的模型以获得更快的响应

---

## 学习成果

通过完成本模块，你现在应该了解：

- ✅ vLLM Playground 如何提供管理 vLLM 服务器的 Web 界面
- ✅ 通过 Podman 实现轻松部署的基于容器的架构
- ✅ 流式响应如何实现实时用户交互
- ✅ 系统提示和温度在控制模型行为中的作用
- ✅ vLLM 服务器问题的基本故障排除

## 模块总结

你已成功完成 vLLM Playground 的快速入门模块。

**你完成的内容：**

- 验证了 vLLM Playground 安装和 GPU 可用性
- 拉取了容器镜像并部署了 vLLM 服务器
- 探索了具有流式响应的现代聊天界面
- 测试了与 ACME 用例相关的客户支持场景

**关键要点：**

- vLLM Playground 通过容器管理简化了 LLM 部署
- 聊天界面提供了类似 ChatGPT 的熟悉体验
- 系统提示对于根据特定用例定制模型行为至关重要
- 流式响应实现实时交互以获得更好的用户体验

---

**下一步：** [模块 2：结构化输出](module-02-structured-outputs.md) — 学习如何使用 JSON Schema、Regex 和 Grammar 将模型响应约束为特定格式。

## 参考资料

- [vLLM Playground GitHub 仓库](https://github.com/micytao/vllm-playground) — 安装和配置
- [vLLM 项目](https://github.com/vllm-project/vllm) — 高性能推理引擎
- [TinyLlama 模型](https://huggingface.co/TinyLlama/TinyLlama-1.1B-Chat-v1.0) — 用于测试的轻量级模型
