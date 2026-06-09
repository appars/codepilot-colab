# 🤖 CodePilot AI Studio — Google Colab Edition

> **Agentic AI in Software Engineering | Module 4**
> **Instructor: Prof. Apparsamy Perumal**

---

## What Is This?

CodePilot AI Studio is a hands-on teaching project that builds a real
AI coding assistant — **stage by stage** — using Google Colab.

**No installation. No admin rights. Just a browser and a free API key.**

Every stage adds one new capability to the agent:

| Stage | Concept | In Class / Homework |
|-------|---------|---------------------|
| Stage 1 | Hello Groq — talk to an LLM | ✅ In Class |
| Stage 2 | Memory — agent remembers your conversation | ✅ In Class |
| Stage 3 | Tools — intent routing to specialist skills | ✅ In Class |
| Stage 4 | RAG — long-term memory with ChromaDB | 📚 Homework |
| Stage 5 | Reflection — agent improves its own output | 📚 Homework |
| Stage 6 | LangGraph — stateful graph workflow | 📚 Homework |

---

## ⚡ Before Class — MUST DO (5 minutes)

You need a **free Groq API key** to run these notebooks.
Do this **before class** on your home WiFi.

### Step 1 — Create a free Groq account

1. Open your browser and go to: **https://console.groq.com**
2. Click **"Sign Up"** (top right)
3. Sign up with your **Google account** OR your email
4. No credit card required — it is completely free

![Groq signup page](https://console.groq.com)

### Step 2 — Create your API Key

1. After logging in, click **"API Keys"** in the left sidebar
2. Click the **"Create API Key"** button
3. Give it any name (e.g. `codepilot-class`)
4. Click **"Submit"**
5. **COPY the key immediately** — it looks like this:
   ```
   gsk_xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
   ```
6. Save it somewhere safe (Notepad, Notes app, etc.)

> ⚠️ **Important:** The key is shown only ONCE. If you lose it, create a new one.

### Step 3 — Keep your key ready for class

You will paste this key into the first cell of each notebook.
**Each student must have their own key** — do not share keys.

---

## 🚀 How to Open Notebooks in Google Colab

### Method 1 — From GitHub (Recommended)

1. Go to this repository on GitHub
2. Click on any `.ipynb` file (e.g. `stage1_hello_groq/Stage1_Hello_Groq.ipynb`)
3. You will see an **"Open in Colab"** button at the top — click it
4. The notebook opens directly in Google Colab — ready to run!

### Method 2 — Upload manually

1. Download this repository as a ZIP file from GitHub
2. Unzip it on your computer
3. Go to **https://colab.research.google.com**
4. Click **File → Upload Notebook**
5. Select the `.ipynb` file you want to open

### How to run a notebook

Once the notebook is open in Colab:

1. **Paste your Groq API key** in Cell 1 (the first code cell)
2. Press **Ctrl + F9** to run all cells at once
   OR click the ▶ button on each cell one by one
3. Read the output below each cell
4. Try the experiments in the last cells

> ⏱ **First run takes ~1 minute** to install packages. After that, it is instant.

---

## 📁 Repository Structure

```
codepilot-colab/
│
├── README.md                          ← this file (start here)
│
├── stage1_hello_groq/
│   ├── Stage1_Hello_Groq.ipynb        ← Colab notebook
│   └── Stage1_README.md               ← detailed concept guide
│
├── stage2_memory_agent/
│   ├── Stage2_Memory_Agent.ipynb
│   └── Stage2_README.md
│
├── stage3_tool_agent/
│   ├── Stage3_Tool_Agent.ipynb
│   └── Stage3_README.md
│
├── stage4_rag_explain/
│   ├── Stage4_RAG_Explain.ipynb
│   └── Stage4_README.md
│
├── stage5_reflection/
│   ├── Stage5_Reflection.ipynb
│   └── Stage5_README.md
│
└── stage6_langgraph/
    ├── Stage6_LangGraph.ipynb
    └── Stage6_README.md
```

---

## 🛠️ Tech Stack

| Tool | Purpose | Free? |
|------|---------|-------|
| Google Colab | Browser-based Python environment | ✅ Free |
| Groq API | Fast LLM inference (Llama 3) | ✅ Free |
| LangChain | AI agent framework | ✅ Open source |
| ChromaDB | Vector database for RAG (Stage 4) | ✅ Open source |
| HuggingFace | Text embeddings for RAG (Stage 4) | ✅ Open source |
| LangGraph | Graph-based agent workflow (Stage 6) | ✅ Open source |

---

## ❓ Troubleshooting

### "I lost my Groq API key"
Go to https://console.groq.com → API Keys → Create a new one. Takes 30 seconds.

### "The notebook says ModuleNotFoundError"
Run the Setup cell again (Cell 2). Sometimes Colab resets between sessions.

### "My response is very slow"
Groq should be fast (1-3 seconds). If slow, check your internet connection.
Make sure you are using the Groq key, not Ollama.

### "AuthenticationError or Invalid API Key"
Your API key is wrong or has a typo.
Go to https://console.groq.com and create a fresh key.
Copy it carefully — no extra spaces.

### "The notebook session expired"
Colab sessions expire after ~12 hours of inactivity.
Just re-open the notebook and run from Cell 1 again.
Your Groq key works in the new session too.

---

## 📊 What You Will Build

By Stage 6 you will have built a complete AI coding assistant that can:

- Talk to a fast cloud LLM (Groq + Llama 3)
- Remember your full conversation across multiple turns
- Detect what you want (debug / explain / review) automatically
- Retrieve relevant past knowledge before answering (RAG)
- Critique and improve its own output (reflection)
- Execute a stateful workflow with loops and branching (LangGraph)

---

## 🎓 Credits

Built for **Agentic AI Software Development — Module 4**
Department of Computer Science & Engineering | Semester 5

Tech: [Groq](https://groq.com) · [LangChain](https://langchain.com) ·
[LangGraph](https://langchain-ai.github.io/langgraph/) ·
[ChromaDB](https://www.trychroma.com) · [HuggingFace](https://huggingface.co)
