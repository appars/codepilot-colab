# 🤖 CodePilot AI Studio — Google Colab Edition

> **Agentic AI in Software Engineering | Module 4**
> **Instructor: Prof. Apparsamy Perumal | Semester 5**

---

## What Is This?

CodePilot AI Studio builds a real AI coding assistant **stage by stage** in Google Colab.

**Zero installation. No admin rights. Works on any browser with a free Groq API key.**

| Stage | Concept | Session |
|-------|---------|---------|
| Stage 1 | Hello Groq — talk to Llama 3 via API | ✅ In Class |
| Stage 2 | Memory — agent remembers your conversation | ✅ In Class |
| Stage 3 | Tools — intent routing to specialist skills | ✅ In Class |
| Stage 4 | RAG — long-term memory with ChromaDB | 📚 Homework |
| Stage 5 | Reflection — agent improves its own output | 📚 Homework |
| Stage 6 | LangGraph — stateful graph workflow | 📚 Homework |

---

## ⚡ Before Class — MUST DO (5 minutes)

You need a **free Groq API key**. Do this **before class day**.

### Step 1 — Create a free Groq account
1. Go to: **https://console.groq.com**
2. Click **Sign Up** (top right)
3. Sign up with your Google account or email — **no credit card needed**

### Step 2 — Create your API Key
1. After logging in, click **"API Keys"** in the left sidebar
2. Click **"Create API Key"**
3. Name it anything (e.g. `codepilot`)
4. Click **Submit**
5. **Copy the key immediately** — looks like: `gsk_xxxxxxxxxxxxxxxxxxxx`
6. Save it in Notepad or your phone Notes

> ⚠️ The key is shown only ONCE. If you lose it, just create a new one — takes 30 seconds.

### Step 3 — Store key in Colab Secrets (saves you typing it every notebook!)

> 💡 **Colab Secrets stores your key ONCE and all 6 notebooks find it automatically.**

1. Go to **https://colab.research.google.com**
2. Open any notebook
3. Click the **🔑 key icon** in the left sidebar
4. Click **"Add new secret"**
5. **Name:** `GROQ_API_KEY` ← must be exactly this
6. **Value:** paste your key `gsk_xxxx...`
7. Toggle **"Notebook access"** to **ON**
8. Done! Every notebook will now find your key automatically

> Each student must use their **own key** — never share keys.

---

## 🚀 How to Open and Run a Notebook

### Step 1 — Open from GitHub
1. Go to this repository on **GitHub**
2. Click on any `.ipynb` file (e.g. `stage1_hello_groq/Stage1_Hello_Groq.ipynb`)
3. Click the **orange "Open in Colab" badge** at the top of the file
4. The notebook opens directly in Google Colab

### Step 2 — Run the notebook
1. **Cell 1 (API Key):** Run it — if you set up Colab Secrets, it loads automatically. If not, paste your key manually between the quotes.
2. **Cell 2 (Setup):** Run it — installs packages (~30 seconds first time)
3. **Press Ctrl+F9** to run all remaining cells at once
4. Read the output below each cell
5. Try the experiments at the bottom

> ⏱ **First run: ~1 minute** (package install). All runs after that: instant.

---

## 🔑 Two Ways to Add Your API Key

### Option A — Colab Secrets (Recommended — set up once, works everywhere)

| Step | What to do |
|------|-----------|
| 1 | Click the 🔑 key icon in the Colab left sidebar |
| 2 | Click "Add new secret" |
| 3 | Name: `GROQ_API_KEY` |
| 4 | Value: paste your `gsk_xxxx...` key |
| 5 | Toggle "Notebook access" ON |
| Done! | All 6 notebooks load your key automatically |

### Option B — Paste manually (quick fallback)

If Secrets is not set up, find this line in Cell 1:
```python
GROQ_API_KEY = "paste-your-groq-key-here"
```
Replace `paste-your-groq-key-here` with your actual key. Works immediately.

---

## 📁 Repository Structure

```
codepilot-colab/
│
├── README.md                          ← this file (start here)
│
├── stage1_hello_groq/
│   ├── Stage1_Hello_Groq.ipynb        ← Colab notebook (click Open in Colab)
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

## 🛠️ Tech Stack — All Free

| Tool | Purpose |
|------|---------|
| Google Colab | Browser-based Python — no install needed |
| Groq API | Fast LLM cloud service (free tier) |
| Llama 3.1 8B | Open-source AI model by Meta |
| LangChain | Python AI agent framework |
| ChromaDB | Vector database for RAG (Stage 4) |
| HuggingFace Embeddings | Text-to-vector conversion (Stage 4) |
| LangGraph | Graph-based workflow (Stage 6) |

---

## ❓ Troubleshooting

### "Key not found in Secrets / AuthenticationError"
Your GROQ_API_KEY is wrong or Secrets not set up.
Go to https://console.groq.com → create a fresh key → add to Secrets OR paste manually.

### "Notebook access is OFF in Secrets"
Click the 🔑 icon → find GROQ_API_KEY → toggle Notebook access to ON → re-run Cell 1.

### "ModuleNotFoundError"
Run Cell 2 (the pip install cell) again. Sometimes Colab resets between sessions.

### "Session expired"
Colab sessions expire after ~12 hours. Re-open the notebook and run from Cell 1.
Your Groq key in Secrets is still there — no need to add it again.

### "I lost my Groq API key"
Go to https://console.groq.com → API Keys → Create a new one → update Secrets value.

---

## 📊 What You Will Build

By Stage 6 you will have built a complete AI coding assistant that can:
- Talk to a fast cloud LLM (Groq + Llama 3.1)
- Remember your full conversation across multiple turns
- Detect what you want (debug / explain / review) automatically
- Retrieve relevant knowledge before answering (RAG)
- Critique and improve its own output (reflection)
- Execute a stateful workflow with loops and branching (LangGraph)

---

## 🎓 Credits

Built for **Agentic AI Software Development — Module 4**
Department of Computer Science & Engineering | Semester 5
**Prof. Apparsamy Perumal**

Tech: [Groq](https://groq.com) · [LangChain](https://langchain.com) ·
[LangGraph](https://langchain-ai.github.io/langgraph/) ·
[ChromaDB](https://www.trychroma.com) · [HuggingFace](https://huggingface.co)
