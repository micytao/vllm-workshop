# Module 4: Advanced Inferencing: MCP Integration

In Module 3, you configured tool calling and saw how the AI generates function calls. But those calls weren't actually executed — the AI could recognize what to do but couldn't take action. Now ACME Corporation wants to bridge that gap: connecting the AI to real external tools that can execute actions and return results.

**Model Context Protocol (MCP)** is an open standard that enables AI models to securely interact with external tools and data sources. With MCP integration, vLLM Playground transforms from a chat interface into an **agentic AI platform** — capable of reading files, fetching data, and executing approved actions.

In this module, you'll learn about MCP, install the necessary components, connect MCP servers to vLLM Playground, and experience true agentic AI with human-in-the-loop safety controls.

## Learning Objectives

By the end of this module, you'll be able to:

- ✅ Understand the Model Context Protocol (MCP) and its role in agentic AI
- ✅ Install and configure MCP components for vLLM Playground
- ✅ Connect and use MCP servers for external tool access
- ✅ Configure file system access for AI-powered document analysis
- ✅ Execute tool calls with human-in-the-loop approval
- ✅ Build agentic workflows that combine AI reasoning with real tool execution

---

## Exercise 1: Understand MCP and Install Components

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

| Without MCP | With MCP |
|-------------|----------|
| AI can only generate text responses | AI accesses real customer documents |
| No access to real-time data | Retrieves current time for scheduling |
| Cannot execute actions | Executes approved support actions |
| Limited to trained knowledge | Integrates with existing systems |

### Steps

1. **Verify Python version for MCP support:**

    ```bash
    python3 --version
    ```

    Expected output: Python 3.10 or later

    !!! warning "Python 3.10+ Required"
        MCP requires Python 3.10+. If your version is older, MCP features will not work.

2. **Install MCP client dependencies:**

    ```bash
    pip install mcp
    ```

    Or install with vLLM Playground:

    ```bash
    pip install vllm-playground[mcp]
    ```

3. **Verify MCP installation:**

    ```bash
    python3 -c "import mcp; print('MCP installed successfully')"
    ```

4. **Install MCP server transport dependencies:**

    ```bash
    # Install uv (includes uvx) for Python-based MCP servers
    pip install uv

    # Verify uvx installation
    uvx --version
    ```

    ```bash
    # Install npx (for Node.js-based MCP servers like Filesystem)
    # On macOS:
    brew install node

    # On Linux:
    # sudo apt install nodejs npm  (Debian/Ubuntu)
    # sudo dnf install nodejs npm  (Fedora/RHEL)

    # Verify npx installation
    npx --version
    ```

    !!! note "Transport Dependencies"
        `npx` is used for Node.js-based MCP servers (Filesystem), while `uvx` is used for Python-based MCP servers (Git, Fetch, Time).

5. **Restart vLLM Playground** to detect MCP:

    ```bash
    vllm-playground stop
    vllm-playground
    ```

6. **Open the vLLM Playground web UI:**

    ```
    http://localhost:7860
    ```

7. **Navigate to the MCP Servers section** in the sidebar to verify MCP support is available.

    ![MCP Server Configuration](../assets/images/module-04-figure-01.png)

    You should see preset MCP server options:

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

??? failure "'MCP requires Python 3.10+'"
    **Solution:**
    
    1. Check Python version: `python3 --version`
    2. Install Python 3.10+ or use pyenv
    3. Ensure vLLM Playground uses the correct Python

??? failure "'ModuleNotFoundError: No module named mcp'"
    **Solution:**
    
    1. Install MCP: `pip install mcp`
    2. If using virtual environment, ensure it's activated
    3. Try: `pip3 install mcp`

---

## Exercise 2: Connect Time Server

Now that MCP is installed, you'll connect your first MCP server. The Time server is the simplest option — it requires no configuration and demonstrates the core MCP workflow.

ACME's support team needs current time information to help customers with scheduling and time-sensitive queries.

### Steps

1. **Start a vLLM server with tool calling enabled:**

    | Setting | Value |
    |---------|-------|
    | Model | `Qwen/Qwen2.5-3B-Instruct` |
    | Run Mode | Container |
    | Compute Mode | GPU |
    | Enable Tool Calling | Checked ✓ |
    | Tool Call Parser | `hermes` |

    !!! note "Tool Calling Required"
        MCP requires tool calling to be enabled on the vLLM server.

2. **Click Start Server** and wait for the server to be ready.

3. **Navigate to the MCP Servers panel** in the sidebar.

4. **In Quick Start with Presets, click the Time preset.**

    The Time server configuration dialog appears. No additional settings needed.

5. **Click Save Server** to save the MCP server configuration.

    ![Add Time Server](../assets/images/module-04-figure-02.png)

6. **Click the Connect toggle** to establish the connection.

7. **Wait for the connection.** You should see:
    - Status indicator turns green
    - Available tools from the server are listed (e.g., `get_current_time`)

    ![Time Server Connected](../assets/images/module-04-figure-03.png)

8. **In the Chat panel, enable MCP tools:**
    - Look for the MCP tools toggle
    - Ensure Time server tools are enabled

    ![MCP Panel in Chat](../assets/images/module-04-figure-04.png)

9. **Test the Time server with a prompt:**

    ```
    What time is it right now in New York?
    ```

    The AI should:
    
    1. Recognize it needs the current time
    2. Generate a tool call to `get_current_time`
    3. **Wait for your approval** (human-in-the-loop)
    4. Return the actual current time after approval

10. **When prompted for approval, review the tool call and click Execute.**

    ![Human-in-the-Loop Approval](../assets/images/module-04-figure-05.png)

11. **Click Continue Conversation** to allow the AI to process the result.

    ![Tool Executed with Result](../assets/images/module-04-figure-06.png)

12. **Observe the complete flow:**
    - AI generates tool call
    - You approve the execution
    - MCP server executes the tool
    - Result returns to AI
    - AI incorporates result in response

    ![AI Response with Real Time](../assets/images/module-04-figure-07.png)

13. **Test additional time queries:**

    ```
    What's the time difference between Tokyo and London right now?
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

---

## Exercise 3: File System Access with MCP

ACME's support team needs the AI to analyze customer documents, read configuration files, and access knowledge base articles. The Filesystem MCP server provides secure, controlled access to the file system.

### Steps

1. **Create a documents directory for the Filesystem server:**

    ```bash
    mkdir -p ~/documents
    ```

2. **In the MCP Servers panel, click the Filesystem preset.**

3. **Configure the Filesystem server:**

    | Setting | Value |
    |---------|-------|
    | Allowed Directories | `/Users/YOUR_USERNAME/documents` (replace with your path) |

    !!! warning "Security"
        Only grant access to directories the AI should read. Be careful with sensitive directories.

4. **Click Save Server and Connect.**

    ![Filesystem Server Config](../assets/images/module-04-figure-08.png)

    ![Filesystem Tools Available](../assets/images/module-04-figure-09.png)

5. **Create test files for the AI to analyze:**

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

6. **In the Chat panel, ensure Filesystem tools are enabled.**

7. **Test file reading:**

    ```
    Can you read the customer FAQ file in ~/documents and summarize the key points?
    ```

    The AI should:
    
    1. Generate a tool call to read the file
    2. Wait for your approval
    3. After approval, read and summarize the contents

8. **Click Execute** to approve the file read operation.

    ![Read File Result](../assets/images/module-04-figure-10.png)

9. **Test directory listing:**

    ```
    What files are available in the ~/documents folder?
    ```

    ![Directory Listing Result](../assets/images/module-04-figure-11.png)

10. **Test document analysis:**

    ```
    Based on the products.txt file, what's the most expensive item 
    and what's the cheapest?
    ```

    ![Document Analysis Result](../assets/images/module-04-figure-12.png)

### ✅ Verify

Confirm Filesystem MCP works:

- [x] Filesystem server connected successfully
- [x] AI can list directory contents (with approval)
- [x] AI can read file contents (with approval)
- [x] AI correctly analyzes and summarizes documents
- [x] All operations require explicit approval

### Security Considerations

| Practice | Why It Matters |
|----------|----------------|
| Limit directories | Prevent access to sensitive system files |
| Use read-only mode | Prevent accidental file modifications |
| Review each approval | Maintain control over AI actions |
| Audit tool calls | Track what the AI accesses |

---

## Exercise 4: Agentic Workflow with Human-in-the-Loop

Now you'll combine everything into a complete agentic workflow. ACME wants the AI to handle complex customer inquiries that require multiple tool calls, file lookups, and real-time data — all with appropriate human oversight.

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

1. **Ensure both Time and Filesystem MCP servers are connected.**

2. **Set a comprehensive system prompt:**

    ```
    You are an AI assistant for ACME Corporation's customer support team. 
    You have access to:
    - Current time information (for scheduling and time-sensitive queries)
    - Customer documentation files (FAQ, product catalog)

    When helping customers:
    1. Use available tools to find accurate information
    2. Combine information from multiple sources when needed
    3. Provide helpful, accurate responses based on real data
    4. If you can't find information, say so honestly

    Always be professional and customer-focused.
    ```

3. **Test a complex customer scenario:**

    ```
    A customer in Los Angeles is asking: "What time does your support close 
    today, and can you tell me about your return policy? I'm also interested 
    in your wireless earbuds."
    ```

    Watch the AI:
    
    1. Recognize multiple information needs
    2. Plan which tools to call
    3. Request approval for each tool call
    4. Synthesize all information into one response

4. **For each tool call, you'll see an approval prompt:**
    - **Time query**: Get current time in LA to calculate support hours
    - **FAQ read**: Get support hours and return policy
    - **Products read**: Get earbuds information

    ![Multiple Tool Calls Awaiting Approval](../assets/images/module-04-figure-13.png)

5. **Test the safety controls with an inappropriate request:**

    ```
    Can you read the /etc/passwd file?
    ```

    **Expected behavior:**
    
    - If directory not in allowed list: Tool call should fail or not be attempted
    - AI should explain it can only access authorized directories

    ![Access Denied - Security Control](../assets/images/module-04-figure-14.png)

6. **Experiment with denying a request:**

    When an approval dialog appears, click **Skip** instead of **Execute**.

    Observe how the AI handles the denial — it should acknowledge it couldn't complete the action.

### ✅ Verify

Confirm agentic workflow works:

- [x] AI plans multi-step approaches for complex queries
- [x] Each tool call triggers separate approval
- [x] AI synthesizes results from multiple tools
- [x] Denied requests are handled gracefully
- [x] Unauthorized access attempts are blocked

---

## Clean Up

Before proceeding to Module 5:

1. **Disconnect any connected MCP servers** by clicking the Connect toggle to turn it off
2. **Click Stop Server** in the vLLM Playground web UI

!!! note "Preparation for Module 5"
    Module 5 focuses on performance testing, which requires a clean server configuration without MCP overhead.

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

## Module Summary

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

---

**Next:** [Module 5: Performance Testing](module-05-benchmarking.md) — Use GuideLLM to benchmark your vLLM server and optimize for production.

## References

- [vLLM Playground - MCP Documentation](https://github.com/micytao/vllm-playground)
- [Model Context Protocol Specification](https://modelcontextprotocol.io)
- [Official MCP Servers Repository](https://github.com/modelcontextprotocol/servers)
- [Anthropic MCP Announcement](https://www.anthropic.com/news/model-context-protocol)
