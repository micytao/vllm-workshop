# Module 3: Advanced Inferencing: Tool Calling

In Module 2, you learned to constrain AI outputs to specific formats. Now ACME Corporation wants to take their AI customer support to the next level: enabling the AI to not just respond, but to **take actions** — looking up order status, checking inventory, or scheduling callbacks.

**Tool calling** (also known as function calling) allows the AI to recognize when it needs external data or actions, and generate structured function calls that your systems can execute. In this module, you'll configure tool calling and define custom tools for ACME's customer support scenarios.

!!! note "Tool Calling Pattern"
    This module focuses on the tool calling *pattern* — how the AI generates function calls. Actual tool execution with human-in-the-loop approval is covered in Module 4 (MCP Integration).

## Learning Objectives

By the end of this module, you'll be able to:

- ✅ Enable tool calling in vLLM Playground server configuration
- ✅ Understand tool calling parsers for different model families
- ✅ Define custom tools with proper function schemas
- ✅ Interpret AI-generated function calls and arguments
- ✅ Choose the appropriate model and parser for tool calling use cases

---

## Exercise 1: Enable Tool Calling

ACME's engineering team needs to configure the vLLM server to support tool calling. This requires enabling the feature and selecting the appropriate parser for the model being used.

### Understanding Tool Calling

Tool calling enables AI models to recognize when they need external data or capabilities, and generate structured requests (function calls) that your application can interpret and execute.

!!! warning "Important: AI Doesn't Execute Tools"
    **Tool calling does NOT mean LLM executes tools.** When tool calling is enabled, the AI model generates a JSON-formatted function call with arguments — but the LLM itself does not execute any code or call external APIs. Your application (vLLM Playground in this case) is responsible for parsing the tool call, executing the actual function, and returning results to the model. This design ensures security and gives you full control.

### Understanding Tool Calling Parsers

Different model families use different formats for tool calling. vLLM supports several parsers:

| Parser | Models | Format |
|--------|--------|--------|
| `llama3_json` | Llama 3.x, Llama 3.1, Llama 3.2 | JSON-based function calls |
| `mistral` | Mistral, Mixtral | Mistral's native tool format |
| `hermes` | Hermes, Qwen (with Hermes prompt) | Hermes-style function calling |
| `auto` (Auto-detect) | Various | Attempts to detect model type |

### Steps

1. **Open the vLLM Playground web UI:**

    ```
    http://localhost:7860
    ```

2. **If a server is running, stop it first:**

    Click **Stop Server** in the Server Configuration panel.

3. **In the Server Configuration panel, configure tool calling:**

    - Check **Enable Tool Calling**
    - Select a tool calling parser (or leave as "Auto-detect")

    ![Enable Tool Calling](../assets/images/module-03-figure-01.png)

4. **For this exercise, configure:**

    | Setting | Value |
    |---------|-------|
    | Model | `Qwen/Qwen2.5-3B-Instruct` (or similar Qwen model) |
    | Enable Tool Calling | ✓ Checked |
    | Tool Call Parser | `hermes` (or Auto-detect) |

    !!! note "Open Models"
        Qwen models are open and don't require HuggingFace authentication. If using gated models like Llama, ensure you have HuggingFace access configured.

5. **Click Start Server** and wait for the model to load.

6. **Monitor the server logs** in the Server Logs panel for tool calling confirmation.

### ✅ Verify

Confirm tool calling is enabled:

- [x] Server started without errors
- [x] "Enable Tool Calling" shows as active in the UI
- [x] Server logs confirm tool calling parser loaded

### Troubleshooting

??? failure "'Tool calling not supported for this model'"
    **Solution:**
    
    1. Use a model that supports function calling (Llama 3.x, Mistral, Qwen)
    2. Check the model's documentation for tool calling support
    3. Try a different parser setting

??? failure "Server fails to start with tool calling enabled"
    **Solution:**
    
    1. Check GPU memory — tool calling may require additional resources
    2. Try a smaller model
    3. Review server logs for specific errors

---

## Exercise 2: Define Custom Tools

With tool calling enabled, ACME needs to define the tools (functions) that the AI can invoke. Each tool has a name, description, and parameter schema that tells the AI when and how to use it.

### Understanding Tool Definitions

A tool definition includes:

- **name**: Function identifier (e.g., `get_order_status`)
- **description**: When to use this tool (helps AI decide)
- **parameters**: JSON Schema defining expected arguments

### Steps

1. **In vLLM Playground, navigate to the Tools panel** (🔧 icon in the toolbar).

    ![Tool Calling Panel](../assets/images/module-03-figure-02.png)

2. **Click Add Tool** to define your first customer support function.

3. **Create an order status lookup tool:**

    ![Add New Tool Dialog](../assets/images/module-03-figure-03.png)

    ```json
    {
      "type": "function",
      "function": {
        "name": "get_order_status",
        "description": "Look up the current status of a customer order. Use this when a customer asks about their order, shipping, or delivery.",
        "parameters": {
          "type": "object",
          "properties": {
            "order_id": {
              "type": "string",
              "description": "The order ID to look up (e.g., ORD-12345)"
            }
          },
          "required": ["order_id"]
        }
      }
    }
    ```

4. **Add a second tool for customer information:**

    ```json
    {
      "type": "function",
      "function": {
        "name": "get_customer_info",
        "description": "Retrieve customer account information. Use this when you need to verify customer identity or look up account details.",
        "parameters": {
          "type": "object",
          "properties": {
            "customer_email": {
              "type": "string",
              "description": "Customer's email address"
            },
            "customer_id": {
              "type": "string",
              "description": "Customer's account ID (optional if email provided)"
            }
          },
          "required": ["customer_email"]
        }
      }
    }
    ```

5. **Add a third tool for scheduling callbacks:**

    ```json
    {
      "type": "function",
      "function": {
        "name": "schedule_callback",
        "description": "Schedule a callback from a support agent. Use this when the customer requests to speak with a human or needs escalated support.",
        "parameters": {
          "type": "object",
          "properties": {
            "customer_phone": {
              "type": "string",
              "description": "Customer's phone number for callback"
            },
            "preferred_time": {
              "type": "string",
              "description": "Preferred callback time (e.g., 'morning', 'afternoon', '2pm EST')"
            },
            "issue_summary": {
              "type": "string",
              "description": "Brief summary of the customer's issue"
            }
          },
          "required": ["customer_phone", "issue_summary"]
        }
      }
    }
    ```

6. **Add a product search tool:**

    ```json
    {
      "type": "function",
      "function": {
        "name": "search_products",
        "description": "Search the product catalog. Use this when a customer asks about products, availability, or pricing.",
        "parameters": {
          "type": "object",
          "properties": {
            "query": {
              "type": "string",
              "description": "Search query (product name, category, or keywords)"
            },
            "category": {
              "type": "string",
              "description": "Product category filter (optional)",
              "enum": ["electronics", "clothing", "home", "sports", "all"]
            },
            "max_results": {
              "type": "integer",
              "description": "Maximum number of results to return"
            }
          },
          "required": ["query"]
        }
      }
    }
    ```

7. **Your tools panel should now show 4 defined tools.**

    ![Tools Panel with 4 Tools](../assets/images/module-03-figure-04.png)

### ✅ Verify

Confirm tools are properly defined:

- [x] All 4 tools appear in the Tools panel
- [x] Each tool has name, description, and parameters
- [x] No JSON syntax errors (panel accepts the definitions)

### Best Practices for Tool Definitions

| Practice | Why It Matters |
|----------|----------------|
| Clear descriptions | Helps AI decide when to use the tool |
| Specific parameter names | Reduces ambiguity in extracted values |
| Required vs optional | Guides AI on minimum needed information |
| Enum constraints | Limits values to valid options |
| Default values | Provides sensible fallbacks |

---

## Exercise 3: Test Tool Calling Workflow

Now you'll see tool calling in action. The AI will analyze customer messages and generate appropriate function calls based on the tools you defined.

### Steps

1. **Ensure your tools from Exercise 2 are loaded** and the server is running with tool calling enabled.

2. **In the Chat panel, set a system prompt:**

    ```
    You are a helpful customer support assistant for ACME Corporation. 
    You have access to tools to help customers with their orders, account 
    information, scheduling callbacks, and product searches. Use the 
    appropriate tool when a customer needs specific information or actions.
    ```

3. **In the Tools panel, set Tool Choice to "Auto (recommended)".**

    This allows the AI to automatically decide when to use tools.

4. **Test order status inquiry:**

    ```
    Hi, I placed an order last week and haven't received any updates. 
    My order number is ORD-78432. Can you check the status?
    ```

    **Expected AI behavior — generates a tool call:**
    ```json
    {
      "name": "get_order_status",
      "arguments": {
        "order_id": "ORD-78432"
      }
    }
    ```

    !!! note "Streaming Disabled"
        Streaming is disabled for tool calling due to a bug in vLLM v0.11.0. This has been resolved in later versions.

    ![Order Status Tool Call](../assets/images/module-03-figure-05.png)

5. **Observe the response format:**
    - The AI recognizes the need for external data
    - Instead of making up information, it generates a function call
    - The arguments are extracted from the customer's message

6. **Test customer lookup:**

    ```
    I need to update my shipping address. My email is john.smith@email.com
    ```

    **Expected tool call:**
    ```json
    {
      "name": "get_customer_info",
      "arguments": {
        "customer_email": "john.smith@email.com"
      }
    }
    ```

    ![Multiple Tool Calls](../assets/images/module-03-figure-06.png)

7. **Test callback scheduling:**

    ```
    This is frustrating! I've been trying to resolve this billing issue for days. 
    Can someone call me back? My number is 555-123-4567, preferably in the afternoon.
    ```

    **Expected tool call:**
    ```json
    {
      "name": "schedule_callback",
      "arguments": {
        "customer_phone": "555-123-4567",
        "issue_summary": "Billing issue",
        "preferred_time": "afternoon"
      }
    }
    ```

    ![Schedule Callback Tool Call](../assets/images/module-03-figure-07.png)

8. **Test product search:**

    ```
    Do you have any wireless headphones under $100?
    ```

    **Expected tool call:**
    ```json
    {
      "name": "search_products",
      "arguments": {
        "query": "wireless headphones",
        "category": "electronics",
        "max_results": 5
      }
    }
    ```

    ![Search Products Tool Call](../assets/images/module-03-figure-08.png)

9. **Test a message that doesn't need tools:**

    ```
    Thanks for your help today!
    ```

    **Expected:** Normal text response (no tool call) — the AI should respond conversationally.

10. **Test multiple potential tools:**

    ```
    I want to check on order ORD-99001 and also see if you have the new laptop model in stock.
    ```

    Observe: The AI may generate multiple tool calls or prioritize one.

    ![Multiple Tool Calls for Order and Search](../assets/images/module-03-figure-09.png)

### ✅ Verify

Confirm tool calling workflow works:

- [x] AI generates tool calls for appropriate requests
- [x] Arguments are correctly extracted from customer messages
- [x] Tool names match the defined functions
- [x] AI responds normally when tools aren't needed
- [x] JSON format is valid and parseable

### Understanding the Workflow

```
┌─────────────────────────────────────────────────────────────┐
│                    Tool Calling Workflow                    │
│                                                             │
│  1. Customer Message                                        │
│     "Check order ORD-12345"                                 │
│              ↓                                              │
│  2. AI Analysis                                             │
│     Recognizes need for get_order_status tool               │
│              ↓                                              │
│  3. Tool Call Generated                                     │
│     {"name": "get_order_status", "arguments": {...}}        │
│              ↓                                              │
│  4. [Module 4] Tool Execution    (MCP Server)               │
│     Actual function runs, returns data                      │
│              ↓                                              │
│  5. [Module 4] AI Response                                  │
│     AI incorporates result into customer response           │
└─────────────────────────────────────────────────────────────┘
```

In this module, we completed steps 1-3. Module 4 (MCP Integration) covers steps 4-5 with actual tool execution.

### Troubleshooting

??? failure "AI responds with text instead of tool calls"
    **Solution:**
    
    1. Verify tool calling is enabled in server config
    2. Check that tools are properly loaded
    3. Ensure system prompt mentions available tools
    4. Try rephrasing the customer message more explicitly

??? failure "AI generates wrong tool or arguments"
    **Solution:**
    
    1. Improve tool descriptions to be more specific
    2. Add examples in the description
    3. Check parameter names are clear and unambiguous
    4. Try a different bigger model (Llama 4 has improved tool calling)

??? failure "Invalid JSON in tool call"
    **Solution:**
    
    1. Check tool call parser matches model
    2. Try "Auto-detect" parser setting
    3. Some models may need specific prompt formatting

---

## Clean Up

Before proceeding to Module 4, stop the current vLLM server:

1. Click **Stop Server** in the vLLM Playground web UI
2. Verify the server has stopped

!!! note "Preparation for Module 4"
    Module 4 requires restarting after installing MCP dependencies.

---

## Learning Outcomes

By completing this module, you should now understand:

- ✅ How tool calling enables AI to request external data and actions
- ✅ The role of tool parsers for different model families
- ✅ How to define tools with proper schemas and descriptions
- ✅ The workflow from customer message to generated function call
- ✅ Best practices for tool definitions that guide AI behavior
- ✅ The difference between tool call generation and tool execution

## Module Summary

**What you accomplished:**

- Enabled tool calling in vLLM Playground server configuration
- Understood tool calling parsers for Llama, Mistral, and Hermes models
- Defined 4 customer support tools with proper schemas
- Tested tool calling workflow with various customer scenarios
- Observed AI-generated function calls with extracted arguments

**Key takeaways:**

- Tool calling transforms AI from passive responder to active assistant
- Good tool descriptions are critical for AI decision-making
- The AI generates structured calls; your systems handle execution
- Different models have different tool calling capabilities
- Tool calling is the foundation for agentic AI workflows

**Business impact for ACME:**

- AI can now recognize when it needs customer data
- Structured tool calls integrate with existing backend APIs
- Reduces need for customers to provide information multiple ways
- Foundation for automated support workflows

---

**Next:** [Module 4: MCP Integration](module-04-mcp-integration.md) — Connect the AI to actual external tools with human-in-the-loop approval.

## References

- [vLLM Playground - Tool Calling](https://github.com/micytao/vllm-playground)
- [vLLM Tool Calling](https://docs.vllm.ai/en/latest/serving/openai_compatible_server.html#tool-calling)
- [OpenAI Function Calling Guide](https://platform.openai.com/docs/guides/function-calling) (compatible format)
