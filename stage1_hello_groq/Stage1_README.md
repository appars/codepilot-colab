# Stage 1 — Hello Groq 🦙

> **Concept:** Talking to a Large Language Model (LLM) using a free cloud API.
> No installation. No local model. Just Python + your API key.

---

## 1. What You Will Learn

By the end of Stage 1 you will understand:

- What an **LLM** (Large Language Model) is
- What **Groq** is — a free, fast cloud service that runs AI models
- What **Llama 3** is — the open-source AI model we will use
- What **LangChain** is — the Python framework connecting your code to the LLM
- How to send a **prompt** and receive a **response**

---

## 2. The Concept — How It Works

### What happens when you call an LLM?

```
Your Python code
      │
      ▼
  LangChain              ← the connector (like a USB cable)
      │
      ▼
  Groq API               ← the fast cloud engine
  (console.groq.com)
      │
      ▼
  Llama 3 (8B)           ← the AI brain
      │
      ▼
  Response               ← back to your Python code
```

### Real-world analogy

Think of it like ordering food at a restaurant:
- **You** = the customer (your Python code)
- **LangChain** = the waiter (takes your order to the kitchen)
- **Groq** = the fast kitchen (processes the order instantly)
- **Llama 3** = the chef (actually makes the food / generates the answer)
- **Response** = your food arriving at the table

### Why Groq?

Groq uses special hardware called an **LPU (Language Processing Unit)**
that runs AI models 10x faster than regular computers.
A response that takes 30 seconds on a laptop takes 1-3 seconds on Groq.
And it is **completely free** for students.

---

## 3. Getting Your Groq API Key (do this first!)

### Step 1 — Create account
1. Go to **https://console.groq.com**
2. Click **Sign Up** (top right corner)
3. Sign up with Google or email — no credit card needed

### Step 2 — Create API Key
1. In the left sidebar, click **"API Keys"**
2. Click **"Create API Key"**
3. Name it anything (e.g. `my-codepilot-key`)
4. Click **"Submit"**
5. **Copy the key** — it looks like: `gsk_abc123...xyz789`

### Step 3 — Save it
Paste it in Notepad or Notes app on your phone.
You will paste it into Cell 1 of the notebook.

> ⚠️ The key is shown only ONCE. Save it immediately!

---

## 4. How to Open and Run the Notebook

### Open in Colab from GitHub
1. Go to the GitHub repository
2. Click `stage1_hello_groq/Stage1_Hello_Groq.ipynb`
3. Click the **"Open in Colab"** badge or button
4. The notebook opens in your browser

### Run the notebook
1. **Cell 1:** Paste your Groq API key and run
2. **Cell 2:** Run setup (installs packages — ~30 seconds)
3. **Cell 3:** Read the concept explanation
4. **Cell 4:** Run the main code — see Llama 3 respond!
5. **Cell 5:** Try the experiments

---

## 5. Expected Output

When Stage 1 runs correctly you will see:

```
============================================================
CodePilot AI Studio — Stage 1: Hello Groq
============================================================
Model  : llama3-8b-8192
Prompt : You are a helpful Python coding assistant...
------------------------------------------------------------
Llama 3 says:

A "bug" in software refers to an error, flaw, or unexpected
behaviour in a program that causes it to produce incorrect
or unintended results.

A common Python bug is the IndexError:
    my_list = [1, 2, 3]
    print(my_list[5])  # IndexError: list index out of range

This crashes because index 5 does not exist in a 3-item list.

============================================================
Stage 1 complete! Groq + LangChain is working.
```

---

## 6. Code Walkthrough — Every Line Explained

```python
# Import ChatGroq from LangChain
# ChatGroq is the class that connects Python to the Groq API
from langchain_groq import ChatGroq

# Set the model name
# llama3-8b-8192 means: Llama 3, 8 billion parameters, 8192 token context
MODEL_NAME = "llama3-8b-8192"

# Create the LLM object
# This prepares the connection — it does NOT send anything yet
# temperature=0.7 means: balanced between creative and precise
#   0.0 = very predictable, robotic
#   1.0 = very creative, unpredictable
llm = ChatGroq(model=MODEL_NAME, temperature=0.7)

# Write a prompt
# A prompt is the instruction you give to the AI
# Good prompts tell the AI: what role to play, what to do, constraints
prompt = "Explain what a bug is. Give a Python example. Under 100 words."

# Send the prompt and get a response
# .invoke() sends the prompt to Groq and waits for the full response
# This is where the actual AI call happens — one line!
response = llm.invoke(prompt)

# Print the response
# response.content is the text the AI generated
print(response.content)
```

---

## 7. Common Errors and Fixes

### ❌ `AuthenticationError` or `Invalid API Key`
**Cause:** Wrong API key or extra spaces when pasting.
**Fix:** Go to https://console.groq.com → Create a new key → copy carefully.

### ❌ `ModuleNotFoundError: No module named 'langchain_groq'`
**Cause:** Setup cell not run yet.
**Fix:** Run Cell 2 (the setup cell) first.

### ❌ `RateLimitError`
**Cause:** Too many requests in one minute (unlikely with personal key).
**Fix:** Wait 60 seconds and try again.

### ❌ `Output is empty or very short`
**Cause:** Temperature too low or prompt too restrictive.
**Fix:** Try temperature=0.7 and make the prompt more open-ended.

---

## 8. Try It Yourself — 3 Experiments

### Experiment 1 — Change the prompt
Find this line in the notebook:
```python
prompt = "Explain what a bug is..."
```
Change it to:
```python
prompt = "Write a Python function that checks if a number is even or odd. Add comments."
```
Run the cell. Does Llama 3 write good code?

### Experiment 2 — Change the temperature
Find `temperature=0.7` and change to `temperature=0.0`.
Run the same prompt twice. Is the output identical both times?
Now try `temperature=1.0`. Is it more creative?

### Experiment 3 — Ask something non-coding
Change the prompt to: `"What is the capital of France?"`
Does the AI answer correctly? Does LangChain care what you ask?

---

## 9. Key Vocabulary

| Term | Meaning |
|------|---------|
| LLM | Large Language Model — the AI brain (Llama 3, GPT-4, etc.) |
| Groq | Cloud service that runs LLMs super fast using LPU hardware |
| Llama 3 | Open-source AI model by Meta — free to use |
| LangChain | Python framework for building AI applications |
| API Key | Your personal password to access the Groq service |
| Prompt | The instruction or question you send to the AI |
| Temperature | Controls creativity: 0 = precise, 1 = creative |
| `.invoke()` | LangChain method: send prompt → get response |
| `response.content` | The text the AI generated |

---

## 10. What Comes Next

**Problem with Stage 1:** Every call is completely independent.
The AI has no memory of what you said before.
If you ask "fix that bug" — it says "which bug?" because it forgot.

**Stage 2 solves this** by adding short-term conversation memory.

➡️ Open `stage2_memory_agent/Stage2_Memory_Agent.ipynb`
