# Module 2: Advanced Inferencing: Structured Outputs

In Module 1, you deployed a vLLM server and experienced the chat interface. Now ACME Corporation faces a new challenge: their customer support system needs to integrate AI responses with existing backend systems. Free-form text responses are difficult to parse and process programmatically.

ACME's engineering team requires predictable, structured outputs that downstream systems can reliably consume. vLLM Playground's structured output capabilities — **JSON Schema**, **Regex**, and **Grammar** — provide exactly this control.

In this module, you'll learn to constrain LLM outputs to specific formats, ensuring ACME's AI responses are system-ready and consistently parseable.

## Learning Objectives

By the end of this module, you'll be able to:

- ✅ Analyze customer feedback using sentiment classification with constrained outputs
- ✅ Define JSON Schema constraints for structured API-ready responses
- ✅ Apply Regex patterns to enforce specific output formats
- ✅ Create custom Grammar rules for complex output structures
- ✅ Choose the appropriate structured output method for different use cases

---

## Exercise 1: Sentiment Analysis with Constrained Outputs

ACME's customer support team receives thousands of feedback messages daily. Before routing to the appropriate team, they need to automatically classify the sentiment of each message. The classification must be consistent and machine-readable.

You'll configure vLLM to output only valid sentiment labels, ensuring reliable automated processing.

### Prerequisites

- Module 1 completed
- Access to vLLM Playground web UI

### Steps

1. **Open the vLLM Playground web UI:**

    ```
    http://localhost:7860
    ```

2. **For structured outputs to work reliably, we need a more capable model.** Stop the current server if running, then configure:

    - Click **Stop Server** if a server is currently running
    - In the **Model** section, select or enter: `Qwen/Qwen2.5-3B-Instruct`
    - Ensure **Container** mode and **GPU** mode are selected
    - Click **Start Server**

    !!! note "Model Selection"
        Qwen2.5-3B-Instruct is a 3 billion parameter model that provides better instruction-following capabilities for structured output tasks while still being fast on GPU.

3. **Wait for the server to be ready** (green "Server is ready to chat!" notification).

4. **Navigate to the Structured Outputs section** in the toolbar panel.

5. **Click "Enable Structured Outputs".**

6. **Select Choice mode** for simple constrained outputs.

7. **Configure the sentiment choices:**

    ```json
    ["positive", "negative", "neutral"]
    ```

    This constrains the model to output ONLY one of these three values.

8. **Set a system prompt for sentiment analysis:**

    ```
    You are a sentiment classifier. Analyze the customer feedback and respond 
    with exactly one word: positive, negative, or neutral. No other output.
    ```

9. **Test with sample customer feedback:**

    ```
    Feedback: "I absolutely love your product! It exceeded all my expectations 
    and the customer service was fantastic."
    ```

    **Expected output:** `positive`

10. **Test with negative feedback:**

    ```
    Feedback: "Terrible experience. The product arrived broken and nobody 
    responded to my support ticket for a week."
    ```

    **Expected output:** `negative`

11. **Test with neutral feedback:**

    ```
    Feedback: "The product works as described. Delivery was on time. 
    Nothing special but nothing wrong either."
    ```

    **Expected output:** `neutral`

### ✅ Verify

Confirm structured outputs are working:

- [x] Model outputs ONLY one of the three allowed values
- [x] No additional text, explanations, or formatting
- [x] Consistent classification across similar inputs

### Business Value for ACME

With constrained sentiment outputs, ACME can:

- Automatically route negative feedback to priority support queues
- Aggregate sentiment metrics for reporting dashboards
- Trigger alerts when negative sentiment spikes
- Process thousands of messages without manual review

---

## Exercise 2: JSON Schema for Structured Responses

ACME's backend systems expect customer support responses in a specific JSON format. The AI must generate responses that conform exactly to the required schema, enabling direct API integration without post-processing.

You'll define a JSON Schema that constrains the model to output properly structured customer support tickets.

### Steps

1. **Click the Clear button** in the Chat Interface to start a new conversation.

2. **In the Structured Outputs panel, select JSON mode.**

3. **Define a schema for customer support ticket extraction:**

    ```json
    {
      "type": "object",
      "properties": {
        "ticket_type": {
          "type": "string",
          "enum": ["billing", "technical", "general", "complaint", "feature_request"]
        },
        "priority": {
          "type": "string",
          "enum": ["low", "medium", "high", "urgent"]
        },
        "summary": {
          "type": "string",
          "maxLength": 100
        },
        "customer_sentiment": {
          "type": "string",
          "enum": ["positive", "negative", "neutral"]
        },
        "requires_escalation": {
          "type": "boolean"
        }
      },
      "required": ["ticket_type", "priority", "summary", "customer_sentiment", "requires_escalation"]
    }
    ```

4. **Set a system prompt for ticket extraction:**

    ```
    You are a customer support ticket classifier. Extract structured information 
    from customer messages and output a JSON object with ticket_type, priority, 
    summary, customer_sentiment, and requires_escalation fields.
    ```

5. **Test with a sample customer message:**

    ```
    Customer message: "I've been charged twice for my subscription this month! 
    This is unacceptable and I want a refund immediately. I've been a loyal 
    customer for 3 years and this is how you treat me?"
    ```

    **Expected output:**
    ```json
    {
      "ticket_type": "billing",
      "priority": "high",
      "summary": "Customer charged twice for subscription, requesting immediate refund",
      "customer_sentiment": "negative",
      "requires_escalation": true
    }
    ```

6. **Test with a different scenario:**

    ```
    Customer message: "Hey, just wondering if you have any plans to add dark mode 
    to the mobile app? Would be really nice to have. Thanks!"
    ```

    **Expected output:**
    ```json
    {
      "ticket_type": "feature_request",
      "priority": "low",
      "summary": "Request for dark mode in mobile app",
      "customer_sentiment": "positive",
      "requires_escalation": false
    }
    ```

### ✅ Verify

Confirm JSON Schema constraints are enforced:

- [x] Output is valid JSON (no syntax errors)
- [x] All required fields are present
- [x] Enum values match the defined options
- [x] Boolean field is true/false (not string)

!!! tip "Validation"
    The vLLM Playground response panel already validates JSON formatting — if the output displays properly formatted, it's valid JSON.

### Troubleshooting

??? failure "Output contains extra text before/after JSON"
    **Solution:**
    
    1. Ensure JSON Schema mode is properly selected
    2. Check that the schema is valid JSON
    3. Restart the server if mode changes don't take effect

??? failure "Model outputs invalid enum values"
    **Solution:**
    
    1. Verify enum arrays are properly formatted in schema
    2. Ensure model supports guided decoding (check server logs)

---

## Exercise 3: Regex Patterns for Formatted Outputs

ACME's ticketing system requires specific ID formats for tracking. The AI must generate ticket IDs that match the exact pattern expected by downstream systems: `ACME-XXXX-YYYY` where X is a letter and Y is a digit.

You'll use Regex constraints to enforce this precise format.

### Steps

1. **Click the Clear button** to start a new conversation.

2. **In the Structured Outputs panel, select Regex mode.**

3. **Define a pattern for ACME ticket IDs:**

    ```
    ACME-[A-Z]{4}-[0-9]{4}
    ```

    This pattern enforces:
    
    - Literal prefix `ACME-`
    - Exactly 4 uppercase letters
    - A hyphen `-`
    - Exactly 4 digits

4. **Set a system prompt:**

    ```
    Generate a unique ticket ID for the customer support system. 
    Output only the ticket ID, nothing else.
    ```

5. **Test the Regex constraint:**

    ```
    Generate a ticket ID for a new billing inquiry.
    ```

    **Expected output format:** `ACME-BXYZ-1234` (exact letters/numbers will vary)

6. **Verify the format matches:**
    - Starts with `ACME-`
    - Followed by 4 uppercase letters
    - Then a hyphen
    - Ends with 4 digits

7. **Try a more complex Regex for phone number formatting:**

    ```
    \(\d{3}\) \d{3}-\d{4}
    ```

    This enforces US phone format: `(XXX) XXX-XXXX`

8. **Test with prompt:**

    ```
    Format this phone number: 5551234567
    ```

    **Expected output:** `(555) 123-4567`

### ✅ Verify

Confirm Regex patterns are enforced:

- [x] Output matches the exact pattern
- [x] No extra characters or whitespace
- [x] Pattern is consistently applied across multiple generations

### Use Cases for ACME

Regex constraints enable:

- **Ticket IDs**: Consistent format for tracking systems
- **Reference numbers**: Order IDs, invoice numbers, case numbers
- **Formatted data**: Phone numbers, dates, postal codes
- **Codes**: Product SKUs, department codes, status codes

---

## Exercise 4: Grammar for Complex Output Structures

ACME's analytics team needs AI-generated reports in a specific markup format that their reporting tool can parse. The format is more complex than what JSON Schema or Regex can easily express.

You'll define a custom Grammar to enforce a report structure with sections, bullet points, and specific delimiters.

### Steps

1. **Click the Clear button** to start a new conversation.

2. **In the Structured Outputs panel, select Grammar mode.**

3. **Grammar uses GBNF (GGML BNF) format.** Define a simple report structure:

    ```
    root ::= report
    report ::= header sections footer
    header ::= "## REPORT START ##\n"
    sections ::= section+
    section ::= section-title bullet-list "\n"
    section-title ::= "### " [A-Za-z ]+ " ###\n"
    bullet-list ::= bullet+
    bullet ::= "- " [A-Za-z0-9 ,.]+ "\n"
    footer ::= "## REPORT END ##"
    ```

    This grammar enforces:
    
    - Report wrapped in START/END markers
    - One or more sections with titles
    - Bullet lists within each section

4. **Set a system prompt:**

    ```
    Generate a brief customer support summary report. 
    Follow the exact format structure provided.
    ```

5. **Test with a prompt:**

    ```
    Summarize today's customer support metrics: 150 tickets resolved, 
    23 escalated, average response time 4.2 minutes, customer satisfaction 94%.
    ```

    **Expected output format:**
    ```
    ## REPORT START ##
    ### Daily Metrics ###
    - Total tickets resolved, 150
    - Escalated tickets, 23
    - Average response time, 4.2 minutes
    ### Customer Satisfaction ###
    - Overall satisfaction score, 94 percent
    ## REPORT END ##
    ```

### ✅ Verify

Confirm Grammar constraints work:

- [x] Output follows the defined structure exactly
- [x] Required delimiters (##, ###, -) are present
- [x] No content outside the grammar rules

### When to Use Each Method

| Use Case | Best Method |
|----------|-------------|
| Simple choices (yes/no, categories) | Choice mode |
| Structured data (APIs, databases) | JSON Schema |
| Specific formats (IDs, codes) | Regex |
| Complex markup, reports, custom formats | Grammar |

### Reset Structured Outputs

Before moving on, disable Structured Outputs:

1. In the **Structured Outputs** panel, uncheck **Enable Structured Outputs**
2. Click the **Clear** button to start fresh

!!! note "Combining Methods"
    In practice, these features can complement each other — you might use JSON Schema for API responses in one conversation and Regex for ID generation in another. Choose the right constraint for each use case.

---

## Troubleshooting

??? failure "Structured outputs not enforced (model ignores constraints)"
    **Solution:**
    
    1. Verify the server was started with structured output support
    2. Check server logs for guided decoding errors
    3. Some models may not support all constraint types

??? failure "'Guided decoding not supported' error"
    **Solution:**
    
    1. Ensure you're using a compatible model
    2. Check vLLM version supports guided decoding
    3. Try a different model that supports constrained generation

??? failure "Performance slower with structured outputs"
    **Solution:**
    
    1. This is expected — constraint checking adds overhead
    2. Use simpler constraints when possible
    3. For high throughput, consider JSON Schema over Grammar

---

## Learning Outcomes

By completing this module, you should now understand:

- ✅ How structured outputs enable reliable AI integration with backend systems
- ✅ The difference between Choice, JSON Schema, Regex, and Grammar modes
- ✅ When to use each structured output method based on requirements
- ✅ How to define JSON Schemas for API-ready responses
- ✅ How Regex patterns enforce specific output formats
- ✅ How Grammar rules handle complex structured outputs

## Module Summary

**What you accomplished:**

- Implemented sentiment classification with constrained choice outputs
- Defined JSON Schemas for customer support ticket extraction
- Applied Regex patterns for ticket ID and phone number formatting
- Created Grammar rules for structured report generation

**Key takeaways:**

- Structured outputs transform unpredictable AI text into reliable, parseable data
- JSON Schema is ideal for API integration and database storage
- Regex excels at enforcing specific ID and code formats
- Grammar handles complex markup and custom structures
- Choose the simplest constraint method that meets your requirements

**Business impact for ACME:**

- Customer feedback automatically categorized and routed
- Support tickets generated in backend-compatible JSON format
- Consistent ticket IDs for tracking and analytics
- Structured reports for management dashboards

---

**Next:** [Module 3: Tool Calling](module-03-tool-calling.md) — Enable the AI to execute functions, retrieve data, and perform actions.

## References

- [vLLM Playground - Structured Outputs](https://github.com/micytao/vllm-playground)
- [vLLM Guided Decoding](https://docs.vllm.ai/en/latest/serving/openai_compatible_server.html#guided-decoding)
- [Understanding JSON Schema](https://json-schema.org/understanding-json-schema/)
- [GBNF Grammar Reference](https://github.com/ggerganov/llama.cpp/blob/master/grammars/README.md)
