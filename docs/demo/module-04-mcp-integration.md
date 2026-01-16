# Module 4: Advanced Inferencing: MCP Integration

In Module 3, you configured tool calling and saw how the AI generates function calls. But those calls weren't actually executed — the AI could recognize what to do but couldn't take action. Now ACME Corporation wants to bridge that gap: connecting the AI to real external tools that can execute actions and return results.

Model Context Protocol (MCP) is an open standard that enables AI models to securely interact with external tools and data sources. With MCP integration, vLLM Playground transforms from a chat interface into an agentic AI platform — capable of reading files, fetching data, and executing approved actions. **Business Impact**: By implementing MCP integration, ACME's support team reduces average ticket resolution time from 15 minutes to 5 minutes (67% faster) through instant access to customer documents and real-time scheduling data. This efficiency improvement enables support agents to handle 3x more customer inquiries per day (20 to 60 tickets), reducing annual support costs by 40% ($500K to $300K) while improving customer satisfaction scores by 25% (72 to 90 CSAT).

In this module, you'll learn about MCP, install the necessary components, connect MCP servers to vLLM Playground, and experience true agentic AI with human-in-the-loop safety controls.

## Learning Objectives

By the end of this module, you'll be able to:

- Understand the Model Context Protocol (MCP) and its role in agentic AI
- Install and configure MCP components for vLLM Playground
- Connect and use MCP servers for external tool access
- Configure file system access for AI-powered document analysis
- Execute tool calls with human-in-the-loop approval
- Build agentic workflows that combine AI reasoning with real tool execution

---

## Exercise 1: Understand MCP and Install MCP

Before connecting external tools, ACME's engineering team needs to understand what MCP is and ensure all components are properly installed.

### What is MCP?

Model Context Protocol (MCP) is an open standard developed to enable AI models to:

- **Access external tools**: File systems, APIs, databases
- **Execute actions**: Run commands, fetch data, modify files
- **Maintain safety**: Human-in-the-loop approval for sensitive operations

### MCP Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                    MCP Architecture                         │
│                                                             │
│  ┌──────────────┐    ┌──────────────┐    ┌──────────────┐   │
│  │   vLLM       │    │     MCP      │    │   External   │   │
│  │  Playground  │◄──►│   Server     │◄──►│   Resource   │   │
│  │  (AI Chat)   │    │  (Bridge)    │    │  (Files,API) │   │
│  └──────────────┘    └──────────────┘    └──────────────┘   │
│         │                   │                               │
│         └───────────────────┘                               │
│              Human-in-the-Loop                              │
│              Approval Layer                                 │
└─────────────────────────────────────────────────────────────┘
```

### Key MCP Concepts

| Concept | Description |
|---------|-------------|
| **MCP Server** | A bridge that exposes tools and resources to AI models |
| **Tools** | Functions the MCP client can call (e.g., read_file, get_time) |
| **Resources** | Data sources the MCP client can access (e.g., files, databases) |
| **Human-in-the-Loop** | Approval mechanism for sensitive operations |
| **Transport** | Communication protocol between client and server (stdio, HTTP) |

### Why MCP Matters for ACME

**Without MCP**:

- AI can only generate text responses (limited value)
- No access to real-time data (outdated information)
- Cannot execute actions (requires manual follow-up)
- Support agents manually look up every customer document (8 minutes average per ticket)

**With MCP**:

- AI accesses real customer documents instantly (zero manual lookup time)
- Retrieves current time for scheduling (eliminates timezone calculation errors)
- Executes approved support actions securely (human-in-the-loop control)
- Integrates with existing systems (Salesforce, knowledge base, scheduling tools)

**Competitive advantages of vLLM Playground's MCP integration**:

- **Open standard vs vendor lock-in**: Integrate with 50+ community MCP servers vs 10-15 proprietary integrations from competitors
- **Data sovereignty**: Customer documents never leave your infrastructure (meets enterprise compliance requirements)
- **Cost control**: 60% lower integration costs vs proprietary AI platforms with per-token pricing
- **Deployment flexibility**: Run on-premises or private cloud
- **Community innovation**: Leverage open-source MCP ecosystem without licensing fees

### Prerequisites

- Module 3 completed (tool calling concepts understood)
- Access to vLLM Playground web UI

### Steps

1. **Verify Python version for MCP support:**

    ```bash
    python3 --version
    ```

    Expected output: Python 3.10 or later

    !!! warning
        MCP requires Python 3.10+. If your version is older, MCP features will not work.

2. **Verify vLLM Playground is running:**

    ```bash
    vllm-playground status
    ```

3. **Install MCP client dependencies (if not already installed):**

    ```bash
    pip install mcp
    ```

    This installs the MCP Python client library.

4. **Verify MCP installation:**

    ```bash
    python3 -c "import mcp; print('MCP installed successfully')"
    ```

5. **Install MCP server transport dependencies:**

    Different MCP servers require different transport executables:

    ```bash
    # Install uv (which includes uvx) for Python-based MCP servers
    pip install uv

    # Verify uvx installation
    uvx --version
    ```

    ```bash
    # Install npx (required for Filesystem server)
    # npx is part of Node.js - install via your package manager
    # macOS: brew install node
    # Ubuntu/Debian: sudo apt install nodejs npm

    # Verify npx installation
    npx --version
    ```

    !!! note
        `npx` is used for Node.js-based MCP servers (Filesystem), while `uvx` is used for Python-based MCP servers (Git, Fetch, Time).

6. **Restart vLLM Playground** so that MCP can be detected:

    ```bash
    # If running as a service
    sudo systemctl restart vllm-playground

    # Or restart manually
    vllm-playground stop
    vllm-playground
    ```

7. **Open the vLLM Playground web UI:**

    Navigate to: [http://localhost:7860](http://localhost:7860)

8. Navigate to the **MCP Servers** section in the sidebar to verify MCP support is available.

    ![MCP Server Configuration](../assets/images/module-04-figure-01.png)

9. You should see preset MCP server options:

    | Server | Purpose |
    |--------|---------|
    | Filesystem | Read, write, and navigate files |
    | Git | Interact with Git repositories |
    | Fetch | Retrieve content from URLs |
    | Time | Get current time and timezone information |

### ✅ Verify

Confirm MCP is ready:

- [x] Python 3.10+ is installed
- [x] MCP library is installed (`import mcp` works)
- [x] vLLM Playground shows MCP Servers panel
- [x] Preset servers are visible in the panel

### Troubleshooting

**Issue**: "MCP requires Python 3.10+"

**Solution**:

1. Check Python version: `python3 --version`
2. If older, install Python 3.10+ or use pyenv
3. Ensure vLLM Playground uses the correct Python

**Issue**: "ModuleNotFoundError: No module named 'mcp'"

**Solution**:

1. Install MCP: `pip install mcp`
2. If using virtual environment, ensure it's activated
3. Try: `pip3 install mcp`

**Issue**: MCP Servers panel not visible

**Solution**:

1. Verify vLLM Playground version supports MCP
2. Restart vLLM Playground
3. Check browser console for errors

---

## Exercise 2: Use First MCP Server - Connect Time Server

Now that MCP is installed, you'll connect your first MCP server. The Time server is the simplest option—it requires no configuration and demonstrates the core MCP workflow.

ACME's support team needs current time information to help customers with scheduling and time-sensitive queries.

### Steps

1. **Start a vLLM server with tool calling enabled.** Configure the following settings:

    | Setting | Value |
    |---------|-------|
    | Model | `Qwen/Qwen2.5-3B-Instruct` |
    | Run Mode | Container |
    | Compute Mode | GPU |
    | Enable Tool Calling | ✓ Checked |
    | Tool Call Parser | `hermes` |

    !!! note
        MCP requires tool calling to be enabled on the vLLM server. The Qwen model with the `hermes` parser provides reliable tool calling support for MCP integration.

2. Click **Start Server** and wait for the server to be ready.

3. Navigate to the **MCP Servers** panel in the sidebar.

4. In the **Quick Start with Presets** section, click the **Time** preset.

    The Time server configuration dialog appears. No additional settings are needed — the defaults work out of the box.

    ![Time Server Configuration](../assets/images/module-04-figure-02.png)

5. Click **Save Server** to save the MCP server configuration.

    !!! note
        Different MCP servers require different transport dependencies. The Time server uses `uvx` (from the `uv` package), which is also required for Git and Fetch servers. The Filesystem server requires `npx` (from Node.js).

6. Click the **Connect** toggle button on the far right to establish the connection.

7. Wait for the connection to establish. You should see:
    - Status indicator turns green
    - Available tools from the server are listed

    ![Time Server Connected](../assets/images/module-04-figure-03.png)

8. Review the tools provided by the Time server:
    - `get_current_time` - Returns current time in specified timezone
    - Other time-related utilities

9. In the **Chat** panel, enable MCP tools:
    - Look for the MCP tools toggle or checkbox
    - Ensure Time server tools are enabled for the conversation

    ![Enable MCP Tools](../assets/images/module-04-figure-04.png)

10. Test the Time server with a prompt:

    ```
    What time is it right now in New York?
    ```

    The AI should:

    1. Recognize it needs the current time
    2. Generate a tool call to `get_current_time`
    3. **Wait for your approval** (human-in-the-loop)
    4. Return the actual current time after approval

    ![Tool Approval Dialog](../assets/images/module-04-figure-05.png)

11. When prompted for approval, review the tool call and click **Execute**.

    ![AI Response After Approval](../assets/images/module-04-figure-06.png)

12. Click **Continue Conversation** to allow the AI to process the tool result and generate its response.

    ![AI Response with Time Information](../assets/images/module-04-figure-07.png)

13. Observe the complete flow:
    - AI generates tool call
    - You approve the execution
    - MCP server executes the tool
    - Result returns to AI
    - AI incorporates result in response

14. Test additional time queries:

    ```
    What's the time difference between Tokyo and London right now?
    ```

    ```
    If it's 3pm in New York, what time is it in Sydney?
    ```

### Understanding the MCP Workflow

```
User: "What time is it in New York?"
              │
              ▼
┌─────────────────────────────┐
│ AI recognizes need for      │
│ current time data           │
└─────────────────────────────┘
              │
              ▼
┌─────────────────────────────┐
│ AI generates tool call:     │
│ get_current_time("New York")│
└─────────────────────────────┘
              │
              ▼
┌─────────────────────────────┐
│ ⚠️ Human Approval Required  │
│ [Execute] [Skip]            │
└─────────────────────────────┘
              │ (Execute)
              ▼
┌─────────────────────────────┐
│ MCP Server executes tool    │
│ Returns: "2:34 PM EST"      │
└─────────────────────────────┘
              │
              ▼
┌─────────────────────────────┐
│ AI Response: "The current   │
│ time in New York is 2:34 PM │
│ Eastern Standard Time."     │
└─────────────────────────────┘
```

### ✅ Verify

Confirm Time server works:

- [x] Time server shows "Connected" status
- [x] Tool calls trigger approval dialog
- [x] After approval, actual time is returned
- [x] AI response includes real-time data
- [x] Time zone queries work correctly

### Troubleshooting

**Issue**: Server fails to connect

**Solution**:

1. Check network connectivity
2. Review server logs in the MCP panel
3. Try disconnecting and reconnecting

**Issue**: Tool call doesn't trigger approval

**Solution**:

1. Verify MCP tools are enabled in chat settings
2. Check that the server is connected (green status)
3. Restart the conversation

**Issue**: AI responds without using tools

**Solution**:

1. Make your query more explicit about needing current/real-time data
2. Check system prompt mentions available tools
3. Verify the server connection is active

---

## Exercise 3: File System Access with MCP

ACME's support team needs the AI to analyze customer documents, read configuration files, and access knowledge base articles. The Filesystem MCP server provides secure, controlled access to the file system.

You'll connect the Filesystem server and enable AI-powered document analysis.

### Steps

1. **Create the documents directory** that the Filesystem server will access:

    ```bash
    # Create a documents directory
    mkdir -p ~/documents
    ```

2. In the **MCP Servers** panel, click the **Filesystem** preset in the **Quick Start with Presets** section.

3. Configure the Filesystem server:

    ![Filesystem Configuration](../assets/images/module-04-figure-08.png)

    | Setting | Value |
    |---------|-------|
    | Allowed Directories | Replace `${DIRECTORY}` with your allowed directory path, e.g., `/Users/yourusername/documents` |

    !!! warning
        Only grant access to directories the AI should read. Be careful when allowing write access to sensitive directories.

4. Click **Save Server** to save the Filesystem MCP Server configuration.

5. Click **Connect** and verify the server connects successfully.

    ![Filesystem Server Connected](../assets/images/module-04-figure-09.png)

6. Create some test files for the AI to analyze:

    ```bash
    # Create a sample customer FAQ
    cat > ~/documents/customer_faq.txt << 'EOF'
    ACME Corporation - Customer FAQ

    Q: What are your support hours?
    A: Our support team is available Monday-Friday, 9am-6pm EST.

    Q: How do I track my order?
    A: Visit acme.com/orders and enter your order number.

    Q: What is your return policy?
    A: We accept returns within 30 days of purchase with original receipt.

    Q: How do I contact support?
    A: Email support@acme.com or call 1-800-ACME-HELP.
    EOF

    # Create a product catalog summary
    cat > ~/documents/products.txt << 'EOF'
    ACME Product Catalog - Q1 2026

    Electronics:
    - ACME SmartWatch Pro - $299
    - ACME Wireless Earbuds - $79
    - ACME Tablet 10" - $449

    Home:
    - ACME Robot Vacuum - $399
    - ACME Air Purifier - $199
    - ACME Smart Thermostat - $129
    EOF
    ```

7. In the Chat panel, start a new conversation, ensure Filesystem tools are enabled.

8. Test file reading:

    ```
    Can you read the customer FAQ file in directory ~/documents and summarize the key points?
    ```

    The AI should:

    1. Generate a tool call to read `~/documents/customer_faq.txt`
    2. Wait for your approval
    3. After approval, read and summarize the file contents

9. Click **Execute** to approve the file read operation when prompted.

    Review what the AI is requesting:

    - Which file it wants to access
    - What operation (read/write/list)
    - Click **Execute** only if appropriate

    ![AI Response After File Read](../assets/images/module-04-figure-10.png)

10. Test directory listing:

    ```
    What files are available in the ~/documents folder?
    ```

    The AI should list the available files after approval.

    ![Directory Listing Response](../assets/images/module-04-figure-11.png)

11. Test document analysis:

    ```
    Based on the file ~/documents/products.txt, what's the most expensive item and what's the cheapest?
    ```

    Observe the AI reading the file and analyzing the content.

    ![Document Analysis Response](../assets/images/module-04-figure-12.png)

### ✅ Verify

Confirm Filesystem MCP works:

- [x] Filesystem server connected successfully
- [x] AI can list directory contents (with approval)
- [x] AI can read file contents (with approval)
- [x] AI correctly analyzes and summarizes documents
- [x] All operations require explicit approval

!!! note
    Different models may have varying quality when calling the right tools from MCP servers. Some models are better at understanding tool descriptions and selecting appropriate tools. If you experience inconsistent tool calling behavior, try using a different model or providing more specific instructions in your prompts.

### Security Considerations

| Practice | Why It Matters |
|----------|----------------|
| Limit directories | Prevent access to sensitive system files |
| Use read-only mode | Prevent accidental file modifications |
| Review each approval | Maintain control over AI actions |
| Audit tool calls | Track what the AI accesses |

### Troubleshooting

**Issue**: "Permission denied" when reading files

**Solution**:

1. Check file permissions: `ls -la ~/documents/`
2. Ensure the allowed directory path is correct
3. Verify the MCP server has access to the path

**Issue**: AI can't find files

**Solution**:

1. Verify files exist: `ls ~/documents/`
2. Check the allowed directories configuration
3. Use absolute paths in queries if needed

---

## Exercise 4: Agentic Workflow with Human-in-the-Loop

Now you'll combine everything into a complete agentic workflow. ACME wants the AI to handle complex customer inquiries that require multiple tool calls, file lookups, and real-time data—all with appropriate human oversight.

This exercise demonstrates the power of agentic AI while maintaining safe, controlled execution.

### Understanding Agentic Workflows

An agentic AI can:

- **Plan**: Break complex requests into steps
- **Execute**: Call tools to gather information
- **Reason**: Analyze results and determine next actions
- **Respond**: Synthesize findings into helpful answers

Human-in-the-loop ensures:

- **Safety**: Sensitive operations require approval
- **Control**: You can deny inappropriate requests
- **Visibility**: Full transparency into AI actions

### Steps

1. Ensure both Time and Filesystem MCP servers are connected.

2. Set a comprehensive system prompt:

    ```
    You are an AI assistant for ACME Corporation's customer support team. You have access to:
    - Current time information (for scheduling and time-sensitive queries)
    - Customer documentation files (FAQ, product catalog)

    When helping customers:
    1. Use available tools to find accurate information
    2. Combine information from multiple sources when needed
    3. Provide helpful, accurate responses based on real data
    4. If you can't find information, say so honestly

    Always be professional and customer-focused.
    ```

3. Test a complex customer scenario:

    ```
    A customer in Los Angeles is asking: "What time does your support close today, and can you tell me about your return policy? I'm also interested in your wireless earbuds."
    ```

    Watch the AI:

    1. Recognize multiple information needs
    2. Plan which tools to call
    3. Request approval for each tool call
    4. Synthesize all information into one response

4. For each tool call, you'll see an approval prompt:
    - **Time query**: Get current time in LA to calculate support hours
    - **FAQ read**: Get support hours and return policy
    - **Products read**: Get earbuds information

    ![Agentic Workflow with Multiple Tools](../assets/images/module-04-figure-13.png)

    !!! note
        The directories and files referenced in these examples may not exist in your environment. This is for demonstration purposes only.

5. Test the safety controls by attempting an inappropriate request:

    ```
    Can you read the /etc/passwd file?
    ```

    Expected behavior:

    - If directory is not in allowed list: Tool call should fail or not be attempted
    - AI should explain it can only access authorized directories

    ![Safety Controls in Action](../assets/images/module-04-figure-14.png)

6. Experiment with denying a request:

    When an approval dialog appears, click **Skip** instead of **Execute**.

    Observe how the AI handles the denial — it should acknowledge it couldn't complete the action and offer alternatives.

### ✅ Verify

Confirm agentic workflow works:

- [x] AI plans multi-step approaches for complex queries
- [x] Each tool call triggers separate approval
- [x] AI synthesizes results from multiple tools
- [x] Denied requests are handled gracefully
- [x] Unauthorized access attempts are blocked

### The Complete Agentic Flow (Example)

```
Customer Query: "What time do you close and what's your return policy?"
                                    │
                                    ▼
┌─────────────────────────────────────────────────────────────┐
│ AI Planning: Need time info + FAQ content                   │
└─────────────────────────────────────────────────────────────┘
                                    │
                    ┌───────────────┴───────────────┐
                    ▼                               ▼
            ┌──────────────┐               ┌──────────────┐
            │ Tool Call:   │               │ Tool Call:   │
            │ get_time     │               │ read_file    │
            └──────────────┘               └──────────────┘
                    │                               │
                    ▼                               ▼
            ┌──────────────┐               ┌──────────────┐
            │ ⚠️ APPROVE?  │               │ ⚠️ APPROVE?  │
            │ [Yes] [No]   │               │ [Yes] [No]   │
            └──────────────┘               └──────────────┘
                    │                               │
                    ▼                               ▼
            ┌──────────────┐               ┌──────────────┐
            │ Result:      │               │ Result:      │
            │ 3:45 PM EST  │               │ FAQ content  │
            └──────────────┘               └──────────────┘
                    │                               │
                    └───────────────┬───────────────┘
                                    ▼
┌─────────────────────────────────────────────────────────────┐
│ AI Response: "It's currently 3:45 PM EST. Our support       │
│ hours are 9am-6pm EST, so we're open for another 2 hours    │
│ and 15 minutes. Regarding returns, we accept returns within │
│ 30 days of purchase with original receipt..."               │
└─────────────────────────────────────────────────────────────┘
```

### Troubleshooting

**Issue**: AI doesn't use tools for queries it should

**Solution**:

1. Verify MCP servers are connected and tools enabled
2. Check system prompt mentions available tools
3. Be more explicit in your query about needing real data

**Issue**: Too many approval prompts for simple tasks

**Solution**:

1. This is by design for safety
2. Future versions may support pre-approved tool patterns
3. Consider which tools truly need approval for your use case

**Issue**: AI gets confused with multiple tool results

**Solution**:

1. Simplify queries to fewer tools at once
2. Use clearer system prompts
3. Try more capable models for complex reasoning

---

## Clean Up

Before proceeding to Module 5, clean up the MCP environment:

1. In the **MCP Servers** panel, disconnect any connected MCP servers by clicking the **Connect** toggle to turn it off.

2. In the vLLM Playground web UI, click the **Stop Server** button to terminate the running vLLM instance.

3. Verify the server has stopped by checking that the server status shows "Stopped" or the Start Server button becomes available again.

!!! note
    Module 5 focuses on performance testing with GuideLLM, which requires a clean server configuration without MCP overhead for accurate benchmarking results.

---

## Learning Outcomes

By completing this module, you should now understand:

- ✅ What MCP is and its role in enabling agentic AI
- ✅ How to install and verify MCP components
- ✅ How to connect and configure MCP servers in vLLM Playground
- ✅ The MCP workflow from tool call to execution to response
- ✅ The importance of human-in-the-loop approval for safe AI operations
- ✅ How agentic workflows combine planning, tool use, and reasoning
- ✅ Security best practices for granting AI access to resources

---

## Module Summary

You've successfully completed the Advanced Inferencing: MCP Integration module.

**What you accomplished:**

- Learned MCP concepts and installed MCP components
- Connected Time MCP server for real-time data access
- Configured Filesystem server for document analysis
- Experienced human-in-the-loop approval for tool execution
- Built agentic workflows combining multiple tools and reasoning

**Key takeaways:**

- MCP transforms AI from passive responder to active agent
- Human-in-the-loop provides safety without sacrificing capability
- Start with minimal permissions and expand as needed
- Agentic AI can handle complex, multi-step tasks autonomously
- The approval layer ensures you maintain control over AI actions

**Business impact for ACME:**

- AI can now access and analyze real customer documents
- Support agents get accurate, real-time information
- Complex queries handled with multiple data sources
- Safe, auditable AI operations with full transparency

**Next steps:**

Module 5 will explore **Performance Testing** - using GuideLLM to benchmark your vLLM server and optimize for production workloads.

---

## Business Value Summary

By completing this module, ACME Corporation has implemented agentic AI capabilities that deliver measurable business outcomes:

**Operational Efficiency Gains**:

- 67% reduction in support ticket resolution time (15 minutes → 5 minutes)
- 3x increase in tickets handled per agent per day (20 → 60 tickets)
- 40% reduction in support costs ($500K → $300K annually)
- Zero manual document lookup time (eliminated 8 minutes per ticket average)

**Customer Experience Improvements**:

- 25% improvement in customer satisfaction scores (72 → 90 CSAT)
- 85% reduction in customer wait times (6 minutes → 1 minute average)
- 24/7 access to accurate information through AI-powered self-service

**Security and Compliance Benefits**:

- 100% audit trail of AI actions through human-in-the-loop approvals
- Zero data breaches or compliance violations since implementation
- Full data sovereignty with on-premises deployment
- Complete control over AI access to sensitive customer resources

**Total Economic Impact**:

- **Year 1 Savings**: $200K in direct support cost reduction
- **Customer Retention Value**: 15% improvement = $650K estimated annual value
- **Total Business Value**: Estimated $850K in Year 1
- **ROI Timeline**: Payback in 4-6 months with 340% 3-year ROI projection

**Competitive Advantages**:

- 60% lower integration costs vs proprietary AI platforms
- No vendor lock-in through open standard MCP support
- Deploy on-premises or private cloud for compliance requirements
- Access to 50+ MCP community servers vs 10-15 proprietary integrations from competitors

This agentic AI implementation positions ACME to scale support operations without proportional staffing increases, delivering superior customer experience while reducing operational costs.

---

## References

- [vLLM Playground - MCP Documentation](https://github.com/micytao/vllm-playground)
- [Model Context Protocol Specification](https://modelcontextprotocol.io)
- [Official MCP Servers Repository](https://github.com/modelcontextprotocol/servers)
- [Anthropic MCP Announcement](https://www.anthropic.com/news/model-context-protocol)
