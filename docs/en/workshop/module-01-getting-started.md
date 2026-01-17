# Module 1: Getting Started with vLLM Playground

ACME Corporation is kicking off their AI customer support initiative, and you've been tasked with evaluating vLLM as the foundation for their inference infrastructure. vLLM is the industry-leading high-throughput and memory-efficient inference engine, and **vLLM Playground** provides a modern web interface for managing and interacting with vLLM servers.

In this module, you'll verify your environment, deploy your first vLLM server using Podman, and experience the chat interface firsthand.

## Learning Objectives

By the end of this module, you'll be able to:

- ✅ Verify vLLM Playground installation and environment readiness
- ✅ Pull and deploy a vLLM server using Podman containers
- ✅ Navigate the vLLM Playground web interface
- ✅ Interact with an LLM through the chat UI with streaming responses

---

## Exercise 1: Verify Your Environment

ACME needs to ensure the AI infrastructure is properly configured before proceeding. Let's start by verifying that vLLM Playground and all dependencies are correctly installed.

### Prerequisites

- vLLM Playground installed (`pip install vllm-playground`)
- Podman or Docker installed
- GPU with CUDA support (recommended) or CPU

### Steps

1. **Verify vLLM Playground is installed:**

    ```bash
    vllm-playground --help
    ```

    Expected output:
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

2. **Check Podman is available:**

    ```bash
    podman version
    ```

    You should see Podman version 4.0 or later. (Or use `docker version` if using Docker)

3. **Verify GPU availability (if using GPU mode):**

    ```bash
    nvidia-smi
    ```

    You should see your NVIDIA GPU listed with driver and CUDA information.

4. **Check the current status of vLLM Playground:**

    ```bash
    vllm-playground status
    ```

    This shows whether any vLLM instance is currently running.

### ✅ Verify

Confirm all checks passed:

- [x] `vllm-playground --help` displays usage information
- [x] `podman version` shows version 4.0+
- [x] `nvidia-smi` shows GPU information (if using GPU)
- [x] Environment is ready for vLLM deployment

---

## Exercise 2: Deploy Your First vLLM Server

With the environment validated, ACME is ready to deploy their first LLM. You'll pull the necessary container images and start a vLLM server with a capable model for customer support scenarios.

### Prerequisites

- Exercise 1 completed successfully
- GPU detected and available (or CPU mode)

### Steps

1. **Pre-download the GPU container image** (this may take a few minutes):

    ```bash
    vllm-playground pull
    ```

    This downloads the vLLM GPU container image (~10GB). You'll see progress indicators as the layers download.

    !!! tip "CPU Mode"
        If you're in a CPU-only environment, use `vllm-playground pull --cpu` instead.

2. **Start vLLM Playground:**

    ```bash
    vllm-playground
    ```

3. **Access the Web UI** by opening your browser to:

    ```
    http://localhost:7860
    ```

    ![vLLM Playground Home Page](../assets/images/module-01-figure-01.png)

4. **In the vLLM Playground web interface, configure your first model:**

    - Navigate to **Server Configuration** section
    - In the **Model** section, you have several options:
        - **Dropdown list**: Select from pre-configured models like `TinyLlama 1.1B Chat (Fast, No token)` — ideal for quick testing
        - **Custom model name**: Enter any HuggingFace model ID in the text field
        - **Browse Community Recipes**: Explore community-contributed model configurations
        - **HuggingFace Token**: Required only for gated models (Llama 3.1, Llama 3.2, etc.)

    For this exercise, select `TinyLlama 1.1B Chat` from the dropdown — it's fast and doesn't require authentication.

    ![Model Selection](../assets/images/module-01-figure-02.png)

5. **Configure the Run Mode:**

    - **Subprocess**: Runs vLLM directly as a Python subprocess (requires vLLM preinstalled)
    - **Container**: Runs vLLM in an isolated Podman container (recommended)

    Select **Container** mode for better isolation.

    ![Run Mode Selection](../assets/images/module-01-figure-03.png)

6. **Configure the Compute Mode:**

    - **CPU**: For systems without GPU (slower inference)
    - **GPU**: For NVIDIA GPU acceleration (recommended)

    Select **GPU** mode if available.

    !!! warning "GPU Mode Requires Root Privilege"
        Running containers with GPU access requires root privilege. The vLLM container will be started with `sudo` when GPU mode is selected.

    ![Compute Mode Selection](../assets/images/module-01-figure-04.png)

7. **Review the Command Preview** at the bottom — it shows the exact vLLM command that will run.

    ![Command Preview](../assets/images/module-01-figure-05.png)

8. **Click "Start Server"**

9. **Wait for the model to load.** You can monitor progress in the interface or check container logs:

    ```bash
    sudo podman logs -f vllm-service
    ```

    !!! note "Sudo for GPU Access"
        You may need `sudo podman logs -f vllm-service` as the container uses GPU resources.

### ✅ Verify

Look for these indicators that the server is ready:

![Server Ready](../assets/images/module-01-figure-06.png)

- [x] The **Server Logs** panel shows `Application startup complete.`
- [x] A green **"Server is ready to chat!"** toast notification appears
- [x] The **Send** button in the chat interface turns green

You can also verify the container is running:

```bash
sudo podman ps
```

- [x] Container `vllm-service` is running
- [x] Status shows "healthy" or "Up"

### Troubleshooting

??? failure "Container fails to start with 'out of memory'"
    **Solution:**
    
    1. Check GPU memory: `nvidia-smi`
    2. Try a smaller model or adjust GPU memory utilization in settings
    3. Use `--gpu-memory-utilization 0.8` for 80% GPU memory usage

??? failure "'Model not found' error"
    **Solution:**
    
    1. Verify model ID is correct (check HuggingFace)
    2. For gated models (Llama, Gemma), ensure you have access and set `HF_TOKEN`
    3. Try a public model like `TinyLlama/TinyLlama-1.1B-Chat-v1.0`

??? failure "Port 7860 already in use"
    **Solution:**
    ```bash
    vllm-playground stop
    vllm-playground --port 8080
    ```

---

## Exercise 3: Interact with Your LLM

Now that the vLLM server is running, you'll explore the chat interface and have your first conversation with the model. This simulates how ACME's customer support system would interact with users.

### Steps

1. **In the vLLM Playground web UI, navigate to the Chat section.**

    You'll see a ChatGPT-style interface with:
    
    - Message input area at the bottom
    - Conversation history in the main panel
    - Model settings in the sidebar

    ![Chat Interface](../assets/images/module-01-figure-07.png)

2. **Send your first message to test the model:**

    ```
    Hello! Can you introduce yourself and explain what you can help me with?
    ```

    Observe the streaming response — text appears word by word as the model generates it.

3. **Test a customer support scenario for ACME:**

    ```
    I'm a customer support agent. A customer is asking about their order status. 
    How should I professionally respond if their order is delayed by 2 days?
    ```

    Notice how the model provides helpful guidance for the support scenario.

4. **Observe the Response Metrics panel** on the right side of the chat interface:

    | Metric | Description |
    |--------|-------------|
    | **Prompt Tokens** | Number of tokens in your input |
    | **Completion Tokens** | Number of tokens generated by the model |
    | **Total Tokens** | Combined input and output tokens |
    | **Time Taken** | Total response generation time |
    | **Tokens/sec** | Generation throughput rate |

    These metrics highlight the high throughput that vLLM delivers.

    ![Response Metrics](../assets/images/module-01-figure-08.png)

5. **Explore the chat settings:**

    - **Temperature**: Controls randomness (lower = more deterministic)
    - **Max Tokens**: Limits response length
    - **System Prompt**: Sets the model's persona/behavior

    Try setting a system prompt:

    ```
    You are a helpful customer support assistant for ACME Corporation. 
    Be professional, empathetic, and solution-oriented.
    ```

    !!! tip "Templates"
        Click the **Templates** dropdown to access preset system prompt templates for common use cases.

    ![System Prompt](../assets/images/module-01-figure-09.png)

6. **Send another message with the system prompt active:**

    ```
    A customer is frustrated because their product arrived damaged. What should I say?
    ```

    Notice how the response now aligns with the customer support persona.

### ✅ Verify

Confirm you can interact with the model:

- [x] Messages send successfully
- [x] Responses stream in real-time (word by word)
- [x] System prompt affects model behavior
- [x] Different temperature settings change response style

### Troubleshooting

??? failure "Chat messages don't get responses"
    **Solution:**
    
    1. Verify server is running: Check the status indicator in the UI
    2. Check container logs: `podman logs vllm-service`
    3. Stop the Server and Start again

??? failure "Responses are very slow"
    **Solution:**
    
    1. Check GPU utilization: `nvidia-smi`
    2. Verify model loaded to GPU (not CPU)
    3. Consider a smaller model for faster responses

---

## Learning Outcomes

By completing this module, you should now understand:

- ✅ How vLLM Playground provides a web interface for managing vLLM servers
- ✅ The container-based architecture that enables easy deployment via Podman
- ✅ How streaming responses work for real-time user interaction
- ✅ The role of system prompts and temperature in controlling model behavior
- ✅ Basic troubleshooting for vLLM server issues

## Module Summary

You've successfully completed the Getting Started module for vLLM Playground.

**What you accomplished:**

- Verified vLLM Playground installation and GPU availability
- Pulled container images and deployed a vLLM server
- Explored the modern chat interface with streaming responses
- Tested customer support scenarios relevant to ACME's use case

**Key takeaways:**

- vLLM Playground simplifies LLM deployment through container management
- The chat interface provides a familiar experience similar to ChatGPT
- System prompts are essential for tailoring model behavior to specific use cases
- Streaming responses enable real-time interaction for better user experience

---

**Next:** [Module 2: Structured Outputs](module-02-structured-outputs.md) — Learn how to constrain model responses to specific formats using JSON Schema, Regex, and Grammar.

## References

- [vLLM Playground GitHub Repository](https://github.com/micytao/vllm-playground) — Installation and configuration
- [vLLM Project](https://github.com/vllm-project/vllm) — High-performance inference engine
- [TinyLlama Model](https://huggingface.co/TinyLlama/TinyLlama-1.1B-Chat-v1.0) — Lightweight model for testing
