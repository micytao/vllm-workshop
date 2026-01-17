# Workshop Overview
🎬 *Your Mission, Should You Choose to Accept It*

## Your Role and Mission

You're a **senior AI engineer at ACME Corporation**, and you've been assigned to evaluate and implement an AI Inferencing system that can serve multiple Large Language Models (LLMs) efficiently.

ACME needs to modernize their customer support infrastructure to handle increasing demand while maintaining response quality. Your manager has explained:

> "We need a flexible AI inference platform that can serve multiple models, integrate with external tools, and scale to meet our enterprise requirements."

**Your assignment**: Evaluate vLLM as the foundation for ACME's AI Inferencing service, demonstrating its capabilities for model serving, tool calling, and agentic workflows.

**The objective**: Prove that vLLM can deliver high-performance LLM inference with advanced features like structured outputs, tool calling, and MCP integration.

!!! info "About vLLM Playground"
    Throughout this workshop, you'll use **[vLLM Playground](https://github.com/micytao/vllm-playground)** — a modern web interface that makes vLLM accessible and visual. It handles container management, configuration, and provides an intuitive chat interface for exploring vLLM's capabilities.

## Success Criteria for Your Mission

By the end of this workshop, you'll have practical AI inference skills to address ACME's customer support challenges:

| Skill | Business Outcome |
|-------|------------------|
| Deploy and manage vLLM servers using Podman containers | Establish reliable model serving infrastructure |
| Interact with LLMs through a modern chat interface | Demonstrate real-time customer interaction capabilities |
| Configure structured outputs for consistent AI responses | Ensure predictable, parseable data for downstream systems |
| Implement tool calling for dynamic functionality | Enable AI to execute actions and retrieve information |
| Set up MCP servers for agentic capabilities | Extend AI with external tool access and human-in-the-loop approval |
| Run performance benchmarks to validate throughput | Prove the system can handle production workloads |

**Technical outcome**: You'll have hands-on experience with vLLM that directly applies to ACME's AI infrastructure requirements.

**Business benefit**: A proven AI inference platform that enables intelligent customer support with enterprise-grade reliability.

## Target Audience

This workshop is designed for:

- 🧑‍💻 **AI Engineers** building inference infrastructure
- 👨‍💻 **Developers** integrating LLMs into applications
- 🏗️ **Platform Engineers** evaluating AI serving solutions
- 📐 **Architects** designing enterprise AI systems

## What You Need to Succeed

You should have:

- ✅ Basic Linux experience — you've used a terminal before
- ✅ Understanding of containers — you know how to run Podman/Docker containers
- ✅ Basic AI/ML concepts — you understand what LLMs and inference mean
- ✅ A machine with GPU support (or patience for CPU mode)

## ACME Corporation's AI Challenges

**The situation**: ACME Corporation needs to build an AI-powered customer support system that can handle diverse customer inquiries efficiently.

**Project timeline**: Evaluate vLLM and demonstrate its capabilities for ACME's enterprise AI requirements.

### Current Challenges

| Challenge | Impact |
|-----------|--------|
| **Inconsistent AI responses** | Different models produce varying output formats → complicates integration with existing systems |
| **Limited tool integration** | Current AI solutions can't execute actions or access external data → reduces automation potential |
| **Manual model management** | Deploying and updating models requires significant effort → slows down iteration cycles |
| **Scalability concerns** | Uncertain if current approach can handle production traffic → risk to customer experience |

**The opportunity**: vLLM provides a unified platform for model serving with advanced features that can address these challenges, and you've been selected to evaluate its feasibility for ACME's use case.

## Your Vision of Success

If vLLM proves effective for ACME's use case, here are the potential improvements:

### Immediate Improvements (Short-term)

- ⚡ **Faster model deployment**: Deploy new models in minutes via containers → accelerate experimentation and iteration
- 📋 **Consistent AI outputs**: Structured outputs ensure predictable response formats → simplify integration with downstream systems

### Strategic Benefits (Longer-term)

- 🤖 **Agentic capabilities**: MCP integration enables AI to use external tools → expand automation possibilities
- 👤 **Human-in-the-loop approval**: Safe tool execution with manual approval → maintain control over AI actions
- 📊 **Performance visibility**: Built-in benchmarking validates throughput → confident capacity planning

**Success metric**: A demonstrable AI inference platform that can serve multiple models with tool calling, structured outputs, and agentic capabilities suitable for ACME's customer support requirements.

## Common Questions

??? question "Can we use different models for different use cases?"
    Yes! vLLM supports various models including Llama, Mistral, and Qwen with model-specific optimizations.

??? question "What about controlling AI outputs for our systems?"
    Module 2 covers structured outputs using JSON Schema, Regex, and Grammar to constrain responses.

??? question "How does tool calling work with customer support scenarios?"
    Module 3 demonstrates defining custom tools that AI can invoke to retrieve customer data or execute support actions.

??? question "Can we extend AI with access to our internal systems?"
    Module 4 shows MCP integration for connecting AI to external tools with human-in-the-loop approval.

??? question "How do we validate the system can handle production workloads?"
    Module 5 covers performance benchmarking with GuideLLM to measure throughput, latency, and optimize server configuration.

---

**Ready to begin?** Continue to [Before You Begin](details.md) to see the module breakdown and timing.
