# Stage 2 — Memory Agent 🧠

> **Concept:** Short-term memory — the agent remembers your full conversation, not just the last message.

**LLM:** Groq · model `openai/gpt-oss-20b` · key `GROQ_API_KEY` · ⏱ **20–25 minutes** (including setup)

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
Every time chain.predict() is called:

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

### What ConversationBufferMemory stores — and what it injects

After 2 turns, memory **stores** a list of message objects:
```
[1] Student  : What is IndexError?
[2] CodePilot: IndexError: list index out of range.
[3] Student  : Show me a code example.
[4] CodePilot: my_list=[1,2,3]; print(my_list[5]) -> IndexError
```

Before the next reply, it **injects** this transcript into `{chat_history}`:
```
Student: What is IndexError?
CodePilot: IndexError: list index out of range.
Student: Show me a code example.
CodePilot: my_list=[1,2,3]; print(my_list[5]) -> IndexError
```

The agent reads this **entire history** before generating each new reply.
That is how it "remembers" — it re-reads the history every single time.

---

## 3. Setup

1. Get a free key at **https://console.groq.com** → API Keys → Create API Key.
2. In Colab, click the **🔑 key icon** (left sidebar) → **Add new secret** → name `GROQ_API_KEY`
   (exactly this name) → paste the key → turn **Notebook access** ON.
3. Run **Step 1** (API key) and **Step 2** (install packages) before anything else.

If the secret is not found, Step 1 asks for the key in a **hidden input box**. The key stays only
in the running session and is never printed or saved in the notebook.

> 🔐 **Never** paste your key into a code cell, a screenshot, a chat message or GitHub.
> If a key is ever exposed, delete it in the Groq console and create a new one.

---

## 4. Expected Output

LLM answers vary from run to run — the wording below is an example, not exact output.

```
CodePilot AI Studio - Stage 2: Memory Agent

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

Memory contents after 3 turns:
[1] Student  : What is an IndexError in Python?...
[2] CodePilot: An IndexError occurs when you try...
[3] Student  : Can you give me a code example...
[4] CodePilot: Sure! Here is the IndexError example...
[5] Student  : How do I fix the bug you just showed me?...
[6] CodePilot: To fix the IndexError I showed you...

Total messages in memory: 6
```

**Step 5 — Verify** prints `VERIFICATION PASSED` when at least 6 messages (3 turns) are in memory.

---

## 5. Code Walkthrough

### The LLM
```python
llm = ChatGroq(model="openai/gpt-oss-20b", temperature=0.7)
```

### The Memory Object
```python
memory = ConversationBufferMemory(
    memory_key="chat_history",   # MUST match {chat_history} in the prompt
    return_messages=False,       # inject history as a readable text transcript
    human_prefix="Student",      # names used in that transcript
    ai_prefix="CodePilot",
)
```
- `memory_key` must **exactly match** the variable name in the prompt template.
- Memory always *stores* message objects. `return_messages` only controls how the history is
  *loaded into the prompt*: `False` gives a plain transcript, which is right for a text
  `PromptTemplate`. With `True`, the prompt would contain raw Python objects like
  `[HumanMessage(content=...)]`.

### The Prompt Template
```python
prompt_template = PromptTemplate(
    input_variables=["chat_history", "input"],
    template="""...{chat_history}...Student: {input}\nCodePilot:""",
)
```
Every time `chain.predict()` is called:
1. Memory fills in `{chat_history}` automatically
2. Your new message fills in `{input}`
3. The full combined prompt goes to the LLM (`openai/gpt-oss-20b` on Groq)

### The Conversation Chain
```python
chain = ConversationChain(
    llm=llm,
    memory=memory,
    prompt=prompt_template,
    verbose=False,   # set True to see the full prompt!
)
```
After every `chain.predict()`, the Q+A is automatically saved to memory.

> ℹ️ `ConversationBufferMemory` and `ConversationChain` are **legacy** LangChain classes, kept in
> the `langchain-classic` package. They still work and show memory very clearly, so the notebook
> hides their deprecation warnings. Newer LangChain/LangGraph code does the same job with message
> history and checkpointers — the idea (re-inject the history every turn) is identical.

---

## 6. Common Errors and Fixes

### ❌ `ValidationError ... Got unexpected prompt input variables`
**Cause:** `memory_key` in `ConversationBufferMemory` does not match `{variable}` in the prompt
(e.g. `memory_key="history"` but the template uses `{chat_history}`). Older versions showed
`ValueError: Missing keys in input` for the same mistake.
**Fix:** Make sure `memory_key="chat_history"` and `{chat_history}` in the template are identical.

### ❌ Agent does not seem to remember
**Fix:** Set `verbose=True` in `ConversationChain` and run again.
You will see the full prompt with the history injected. Check that the history appears.

### ❌ `ImportError` / `ModuleNotFoundError` for `langchain_classic` or `ConversationChain`
**Fix:** Run Step 2 again:
`!pip install -q langchain-groq langchain-core langchain-classic`

### ⚠️ `ERROR: pip's dependency resolver ... google-colab 1.0.0 requires requests==2.32.4`
**Harmless for this notebook** — it is a version *warning* from Colab's own package, not a failure.
It appears if extra packages (such as `langchain-community`) upgrade `requests`. The Step 2 cell
installs only `langchain-groq langchain-core langchain-classic`, which avoids it.
If you already saw it: *Runtime → Restart session*, then run the cells again.

### ❌ `401` / authentication error
**Fix:** The key is wrong, revoked or has spaces. Re-add `GROQ_API_KEY` in Colab Secrets
(Notebook access ON) and re-run Step 1.

### ❌ `404 model_not_found`
**Fix:** The model name must be exactly `openai/gpt-oss-20b`. Older names such as
`llama-3.1-8b-instant` may no longer be available.

### ❌ `429` rate limit
**Fix:** Wait a minute and run the cell again — the free tier has request limits.

---

## 7. Try It Yourself (Step 6 in the notebook)

### Experiment 1 — Prove memory works
Ask: `"What was the very first question I asked you?"`
It should mention IndexError — that was Turn 1.

### Experiment 2 — See the full prompt (verbose)
A new chain with `verbose=True` and its own empty memory asks `"What is a for loop?"`, then
`"Show me an example of that."`. In Turn 2, look for Turn 1 inside `Conversation so far:`.

### Experiment 3 — Clear the memory
```python
memory.clear()
response = chain.predict(input="How do I fix the bug you showed me?")
print(response)  # The agent no longer knows which bug - it should ask, or answer generically
```

### Experiment 4 (optional) — Watch the prompt grow
Ask a few more questions and print the size of `{chat_history}` after each one.
`ConversationBufferMemory` **never drops messages on its own**, so the prompt keeps growing.
The real limits are the model's **context window**, **cost** and **latency** — which is why real
agents need *context management* (for example, keeping only recent turns or summarising old ones).

---

## 8. Key Vocabulary

| Term | Meaning |
|------|---------|
| Stateless LLM | Forgets everything after each call |
| Short-term memory | Remembers within one running session, lost on restart |
| Persistent memory | Saved to storage (e.g. a database), so it survives restarts |
| `ConversationBufferMemory` | Stores ALL messages; injects them as `{chat_history}` |
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

> Also note: this memory disappears when the runtime restarts. Remembering across restarts needs
> **persistent memory** — like the SQLite incident memory in RootCause Copilot.
