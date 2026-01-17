# Module 5: Performance Testing

Throughout this workshop, you've deployed vLLM servers, configured advanced inferencing features, and built agentic workflows. Now ACME Corporation faces the final question before production deployment: **Can this infrastructure handle real-world load?**

Before launching their AI-powered customer support system, ACME needs to validate that their vLLM deployment can handle expected traffic volumes with acceptable latency. **GuideLLM** is a performance benchmarking tool designed specifically for LLM inference servers, providing insights into throughput, latency, and resource utilization.

In this final module, you'll benchmark your vLLM server and learn to optimize configuration for production workloads.

## Learning Objectives

By the end of this module, you'll be able to:

- ✅ Understand LLM inference performance metrics and their business impact
- ✅ Install and configure GuideLLM for vLLM benchmarking
- ✅ Run load tests with different request patterns
- ✅ Analyze throughput, latency, and token generation metrics
- ✅ Optimize vLLM server configuration based on benchmark results

---

## Exercise 1: Introduction to GuideLLM and Installation

Before running benchmarks, ACME's engineering team needs to understand what metrics matter for their customer support use case and set up the benchmarking tools.

### Understanding LLM Performance Metrics

| Metric | Description | Business Impact |
|--------|-------------|-----------------|
| **Throughput** | Requests processed per second | How many customers can be served simultaneously |
| **Latency (TTFT)** | Time to First Token — how quickly the response starts | User-perceived responsiveness |
| **Latency (E2E)** | End-to-End — total time for complete response | Total customer wait time |
| **Tokens/second** | Rate of token generation | How fast responses stream to users |
| **GPU Utilization** | Percentage of GPU compute used | Infrastructure efficiency and cost |
| **Memory Usage** | GPU memory consumption | Capacity for concurrent requests |

### What is GuideLLM?

GuideLLM is a benchmarking tool that:

- Generates realistic LLM workloads
- Measures inference performance metrics
- Supports sweep testing (varying request rates)
- Provides detailed analysis and reports
- Integrates with vLLM's OpenAI-compatible API

### Steps

1. **Install GuideLLM:**

    ```bash
    pip install guidellm
    ```

    Or install with vLLM Playground:

    ```bash
    pip install vllm-playground[benchmark]
    ```

2. **Verify the installation:**

    ```bash
    guidellm --help
    ```

    Expected output:
    ```
    Usage: guidellm [OPTIONS] COMMAND [ARGS]...

    GuideLLM CLI for benchmarking, preprocessing, and testing language models.

    Options:
      --version  Show the version and exit.
      --help     Show this message and exit.

    Commands:
      benchmark    Run a benchmark or load a previously saved benchmark report.
      config       Show configuration settings.
      mock-server  Start a mock OpenAI/vLLM-compatible server for testing.
      preprocess   Tools for preprocessing datasets for use in benchmarks.
    ```

3. **Restart vLLM Playground** to detect GuideLLM:

    ```bash
    vllm-playground stop
    vllm-playground
    ```

4. **Start a vLLM server** with the Qwen model:

    | Setting | Value |
    |---------|-------|
    | Model | `Qwen/Qwen2.5-3B-Instruct` |
    | Run Mode | Container |
    | Compute Mode | GPU |

    Click **Start Server** and wait for the server to be ready.

    !!! note "Clean Configuration"
        For performance benchmarking, use a simple configuration without tool calling or MCP to get accurate baseline metrics.

5. **Check your vLLM server endpoint:**

    ```bash
    curl http://localhost:8000/v1/models
    ```

    This confirms the OpenAI-compatible API is accessible.

    ![API Models Response](../assets/images/module-05-figure-01.png)

6. **Understand GuideLLM benchmark options:**

    | Option | Purpose |
    |--------|---------|
    | `--target` | vLLM server URL (default: http://localhost:8000) |
    | `--model` | Model to benchmark (from /v1/models) |
    | `--rate` | Request rate (requests/sec) or "sweep" |
    | `--max-seconds` | Maximum benchmark duration |
    | `--max-requests` | Maximum number of requests |
    | `--data` | Dataset: "emulated" or path to custom data |
    | `--output-path` | Path to save results |

### ✅ Verify

Confirm GuideLLM is ready:

- [x] `guidellm --help` shows available commands
- [x] vLLM server is running and accessible
- [x] `/v1/models` endpoint returns model information

### Troubleshooting

??? failure "'guidellm: command not found'"
    **Solution:**
    
    1. Ensure `pip install` completed successfully
    2. Check if installed in a virtual environment
    3. Try: `python -m guidellm --help`

??? failure "'Connection refused' on /v1/models"
    **Solution:**
    
    1. Verify vLLM server is running: `vllm-playground status`
    2. Check the correct port (default: 8000 for API, 7860 for UI)
    3. Review server logs: `podman logs vllm-service`

---

## Exercise 2: Run Benchmark and Analyze Performance Metrics

Now you'll run your first benchmark and learn to interpret the results. ACME needs to understand their baseline performance before optimizing.

### Steps

1. **In the vLLM Playground web UI, navigate to the GuideLLM panel** in the sidebar.

2. **Select GuideLLM (Advanced)** for Benchmark method.

    !!! note "GuideLLM Required"
        The GuideLLM (Advanced) option is only available after GuideLLM is installed. Without it, you can use the built-in benchmark for basic testing.

3. **Configure the benchmark settings:**

    | Setting | Value |
    |---------|-------|
    | Total Requests | `100` |
    | Request Rate (req/s) | `5` |
    | Prompt Tokens | `100` |
    | Output Tokens | `100` |

4. **Click Run Benchmark** to start.

    ![GuideLLM Panel](../assets/images/module-05-figure-02.png)

    This runs 100 requests at 5 requests/second using 100 prompt tokens and 100 output tokens.

5. **Wait for the benchmark to complete.** You'll see progress indicators and then results.

    ![GuideLLM Advanced with Raw Output](../assets/images/module-05-figure-03.png)

6. **Review the benchmark results.** The GuideLLM panel provides three output formats:

    | Output Format | Description |
    |---------------|-------------|
    | **Raw Output** | Complete console output with real-time progress |
    | **JSON** | Structured output for programmatic analysis |
    | **Benchmark Summary Table** | Formatted table with key metrics |

7. **Understand the Benchmark Summary Table:**

    **Performance Metrics:**

    | Metric | Mean | Median | Min | Max |
    |--------|------|--------|-----|-----|
    | Requests/Second | 4.33 | 4.84 | 0.00 | 59.20 |

    **Token Statistics:**

    | Metric | Value |
    |--------|-------|
    | Output Tokens/Second (Mean) | 448.40 |

    **Request Latency Percentiles:**

    | Percentile | Latency (s) | Latency (ms) |
    |------------|-------------|--------------|
    | P50 | 3.417 | 3416.56 |
    | P75 | 3.498 | 3497.91 |
    | P90 | 3.512 | 3511.61 |
    | P95 | 3.518 | 3517.99 |
    | P99 | 3.559 | 3558.93 |

    ![Benchmark Summary Table](../assets/images/module-05-figure-04.png)

8. **Understand each metric:**

    | Metric | Interpretation |
    |--------|----------------|
    | **Requests/Second (Mean)** | Average throughput — at 4.33 req/s, handles ~260 requests per minute |
    | **Output Tokens/Second** | 448 tokens/s indicates the generation speed |
    | **P50 Latency** | Median latency — 50% of requests complete within 3.4 seconds |
    | **P90 Latency** | 90% of requests complete within 3.5 seconds |
    | **P99 Latency** | 99% of requests complete within 3.6 seconds (tail latency) |

### Analyzing Results for ACME's Use Case

ACME's customer support system requirements:

| Requirement | Target | Why |
|-------------|--------|-----|
| TTFT | < 500ms | Users expect quick response start |
| E2E (typical) | < 3s | Reasonable wait for support answers |
| Throughput | > 10 req/s | Handle 100+ concurrent users with queuing |

Compare your benchmark results against these targets.

### ✅ Verify

Confirm benchmarking works:

- [x] Baseline benchmark completed successfully
- [x] All key metrics (throughput, latency) are captured
- [x] Results displayed in readable format
- [x] Can compare against requirements

### Troubleshooting

??? failure "Benchmark requests failing"
    **Solution:**
    
    1. Check server logs: `podman logs vllm-service`
    2. Verify model is fully loaded
    3. Reduce request rate and retry

??? failure "Very slow throughput"
    **Solution:**
    
    1. Verify GPU is being utilized: `nvidia-smi`
    2. Check model fits in GPU memory
    3. Consider a smaller model for testing

??? failure "Out of memory errors"
    **Solution:**
    
    1. Reduce concurrent requests
    2. Lower `--gpu-memory-utilization` setting
    3. Use a smaller model

---

## Exercise 3: Optimize Server Configuration (Try It Yourself)

Now that you understand how to run benchmarks, try optimizing the vLLM server configuration on your own to improve performance.

### Key Optimization Parameters

The vLLM Playground UI provides several configuration options:

| UI Parameter | vLLM Flag | Effect | Trade-off |
|--------------|-----------|--------|-----------|
| **GPU Memory Utilization** | `--gpu-memory-utilization` | Higher values (0.9-0.95) allow more concurrent requests | Too high may cause OOM errors |
| **Max Model Length** | `--max-model-len` | Maximum context length for requests | Lower values free memory for batching |
| **Tensor Parallel Size** | `--tensor-parallel-size` | Distribute model across multiple GPUs | Requires multiple GPUs |

### Walkthrough: How to Optimize

1. **Record your baseline** — Note the key metrics from Exercise 2

2. **Stop the current server** — Click **Stop Server**

3. **Adjust configuration** — Try changing one parameter at a time:
    - Increase **GPU Memory Utilization** from 0.9 to 0.95
    - Or decrease **Max Model Length** if your use case allows shorter contexts

4. **Restart the server** — Click **Start Server**

5. **Re-run the benchmark** — Use the same settings as Exercise 2

6. **Compare results** — Did throughput improve? Did latency change?

### Optimization Strategies by Use Case

| Use Case | Recommended Approach |
|----------|----------------------|
| **High throughput** (many concurrent users) | Increase GPU memory utilization, accept slightly higher latency |
| **Low latency** (real-time chat) | Keep moderate memory utilization (0.85), prioritize fast response |
| **Long contexts** (document analysis) | Higher max-model-len, fewer concurrent requests |

### Your Turn!

Try the following on your own:

1. Stop the vLLM server
2. Change one configuration parameter (e.g., GPU Memory Utilization to 0.95)
3. Restart the server and run another benchmark
4. Compare the results with your baseline

!!! tip "Keep Notes"
    Document what changes you made and how they affected performance. This helps understand trade-offs for your specific use case.

### ✅ Verify

- [x] You understand the key optimization parameters
- [x] You know how to modify server configuration in the UI
- [x] You can run comparative benchmarks to measure improvements

---

## Troubleshooting

??? failure "GuideLLM crashes during benchmark"
    **Solution:**
    
    1. Check available system memory
    2. Reduce `--max-requests` or `--rate`
    3. Update to latest GuideLLM version

??? failure "Results vary significantly between runs"
    **Solution:**
    
    1. GPU thermal throttling — allow cooling between benchmarks
    2. Other processes competing for resources
    3. Run longer benchmarks for statistical stability

??? failure "Can't achieve target performance"
    **Solution:**
    
    1. Current model may be too large for hardware
    2. Consider model quantization (INT8, INT4)
    3. Evaluate smaller, faster models
    4. Scale horizontally with multiple instances

---

## Learning Outcomes

By completing this module, you should now understand:

- ✅ Key LLM inference metrics and their business implications
- ✅ How to install and use GuideLLM for benchmarking
- ✅ How to interpret throughput, latency, and token generation metrics
- ✅ The trade-offs between throughput and latency optimization
- ✅ How to tune vLLM configuration for different workload patterns
- ✅ How to validate production readiness against requirements

## Module Summary

**What you accomplished:**

- Installed and configured GuideLLM benchmarking tool
- Ran baseline benchmarks against your vLLM server
- Analyzed throughput, latency, and token generation metrics
- Learned optimization strategies for different use cases
- Validated against ACME's production requirements

**Key takeaways:**

- Performance testing is essential before production deployment
- TTFT (Time to First Token) is critical for user-perceived responsiveness
- Throughput vs latency is a fundamental trade-off in LLM serving
- GPU memory utilization directly impacts concurrent request capacity
- Regular benchmarking helps catch performance regressions

**Business impact for ACME:**

- Validated AI infrastructure can handle expected customer load
- Identified optimal configuration for customer support use case
- Established baseline metrics for ongoing monitoring
- Confident production deployment with known performance characteristics

---

**Congratulations!** You've completed all modules. Continue to the [Conclusion](../conclusion.md) for next steps.

## References

- [vLLM Playground - Benchmarking](https://github.com/micytao/vllm-playground)
- [GuideLLM Developer Blog](https://developers.redhat.com/articles/2025/06/20/guidellm-evaluate-llm-deployments-real-world-inference)
- [GuideLLM GitHub Repository](https://github.com/neuralmagic/guidellm)
