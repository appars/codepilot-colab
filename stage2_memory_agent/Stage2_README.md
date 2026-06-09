# Stage 2 — Memory Agent 🧠

> **Concept:** Short-term memory — the agent remembers your full conversation, not just the last message.

---

## 1. What You Will Learn

- Why a stateless LLM is like a **person with amnesia** — forgets after every reply
- What **short-term memory** means for an AI agent
- How `ConversationBufferMemory` stores and replays chat history
- How a `PromptTemplate` injects memory into every LLM call
- How `ConversationChain` wires LLM + memory + prompt together

---

## 2. The Concept

### The Problem (Stage 1)

```
Turn 1: "What is IndexError?"
Agent : "IndexError means index out of range..."

Turn 2: "Show me an example"
Agent : "Example of WHAT?"  ← FORGOT Turn 1!
```

### The Solution (Stage 2)

```
Turn 1: "What is IndexError?"
Agent : "IndexError means index out of range..."
         ↓ saves to memory

Turn 2: "Show me an example"
         ↓ reads memory (sees Turn 1)
Agent : "Sure! Here is an IndexError example..."  ← REMEMBERS!
```

### How Memory Works Under the Hood

```
Every time the agent replies:

User input ──────────────────────────────────┐
                                             ↓
Memory loads history ──► PromptTemplate ──► LLM ──► Response
                          fills in:                      │
                          {chat_history}                 │
                          {input}                        ↓
                                             Memory saves Q+A
```

> **Analogy:** A doctor who reads your complete medical file before every appointment.
> Not one who forgets you the moment you leave the room.

### What ConversationBufferMemory stores

After 2 turns, memory contains:
```
[
  HumanMessage: "What is IndexError?",
  AIMessage:    "IndexError means index out of range...",
  HumanMessage: "Show me an example",
  AIMessage:    "Here is an example: my_list[5] raises IndexError..."
]
```

The agent reads this **entire list** before generating each new reply.
That is how it "remembers" — it re-reads the history every single time.

---

## 3. Setup

Make sure you have your Groq API key from Stage 1.
Run Cell 1 (API key) and Cell 2 (install packages) before anything else.

---

## 4. Expected Output

```
CodePilot AI Studio — Stage 2: Memory Agent

Student (Turn 1): What is an IndexError in Python?
CodePilot: An IndexError occurs when you try to access a list
           element using an index that does not exist...

Student (Turn 2): Can you give me a code example of that error?
CodePilot: Sure! Here is the IndexError example we discussed:
           my_list = [1, 2, 3]
           print(my_list[5])  # IndexError!

Student (Turn 3): How do I fix the bug you just showed me?
CodePilot: To fix the IndexError I showed you, check the list
           length before accessing it...

What is in memory after 3 turns:
[1] Student  : What is an IndexError in Python?...
[2] CodePilot: An IndexError occurs when you try...
[3] Student  : Can you give me a code example...
[4] CodePilot: Sure! Here is the IndexError example...
[5] Student  : How do I fix the bug you just showed me?...
[6] CodePilot: To fix the IndexError I showed you...

Total messages: 6
```

---

## 5. Code Walkthrough

### The Memory Object
```python
memory = ConversationBufferMemory(
    memory_key="chat_history",   # MUST match {chat_history} in prompt
    return_messages=True          # store as objects, not raw text
)
```
`memory_key` must **exactly match** the variable name in the prompt template.

### The Prompt Template
```python
prompt_template = PromptTemplate(
    input_variables=["chat_history", "input"],
    template="""...{chat_history}...{input}..."""
)
```
Every time `chain.predict()` is called:
1. Memory fills in `{chat_history}` automatically
2. Your new message fills in `{input}`
3. The full combined prompt goes to Llama 3

### The Conversation Chain
```python
chain = ConversationChain(
    llm=llm,
    memory=memory,
    prompt=prompt_template,
    verbose=False   # set True to see the full prompt!
)
```
After every `chain.predict()`, the Q+A is automatically saved to memory.

---

## 6. Common Errors and Fixes

### ❌ `ValueError: Missing keys in input`
**Cause:** `memory_key` in `ConversationBufferMemory` does not match `{variable}` in the prompt.
**Fix:** Make sure `memory_key="chat_history"` and `{chat_history}` in the template are identical.

### ❌ Agent does not seem to remember
**Fix:** Set `verbose=True` in `ConversationChain` and run again.
You will see the full prompt with history injected. Check if history appears.

### ❌ `ImportError` for ConversationChain
**Fix:** Run `!pip install -q langchain langchain-groq langchain-community` again.

---

## 7. Try It Yourself

### Experiment 1 — See the full prompt
Change `verbose=False` to `verbose=True` in ConversationChain.
Run again. You will see the exact prompt sent to Llama 3 with memory inside.

### Experiment 2 — Clear the memory
After 3 turns, add:
```python
memory.clear()
response = chain.predict(input="What were we just talking about?")
print(response)  # Should say it does not know — memory is gone!
```

### Experiment 3 — Test memory limits
Keep asking questions in a loop. After many turns, ask:
`"What was my very first question?"`
At what point does the agent start forgetting old messages?

---

## 8. Key Vocabulary

| Term | Meaning |
|------|---------|
| Stateless LLM | Forgets everything after each call |
| Short-term memory | Remembers within one session, lost on restart |
| `ConversationBufferMemory` | Stores ALL messages in a list |
| `PromptTemplate` | Reusable prompt with fill-in variables |
| `ConversationChain` | Glue connecting LLM + memory + prompt |
| `chain.predict()` | Send message → get reply → auto-save to memory |

---

## 9. What Comes Next

**Problem with Stage 2:** Memory handles conversation history.
But the agent uses one generic prompt for everything.
Ask it to debug → vague answer. Ask it to explain → vague answer.

**Stage 3 fixes this** with specialised skill prompts and intent detection.

➡️ Open `stage3_tool_agent/Stage3_Tool_Agent.ipynb`
