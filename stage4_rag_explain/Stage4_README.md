# Stage 4 — RAG Explain 📚

> **Concept:** Long-term memory using RAG (Retrieval Augmented Generation) with ChromaDB and vector embeddings.

---

## 1. What You Will Learn

- What **RAG** is and why it is one of the most important AI patterns
- What **vector embeddings** are — text converted to numbers encoding meaning
- How **ChromaDB** stores and searches knowledge by meaning, not keywords
- How the agent **retrieves context** before answering — making answers better
- The difference between **semantic search** and keyword search

---

## 2. The Concept

### Without RAG vs With RAG

```
WITHOUT RAG:                    WITH RAG:
User asks about code            Knowledge Base (ChromaDB)
        |                               |
Llama 3 answers from            User asks -> find relevant chunks
training only                           |
(may miss specifics)            Llama 3 + context -> better answer!
```

> **Analogy:** A student who reads the right textbook chapter BEFORE answering
> the exam question — not one who answers purely from memory.

### What are vector embeddings?

An embedding converts text into numbers that encode meaning:

```
"IndexError"         -> [0.23, -0.45, 0.87, ...]  (384 numbers)
"list out of bounds" -> [0.21, -0.41, 0.89, ...]  (very similar!)
"banana recipe"      -> [0.65,  0.32, -0.12, ...] (very different)
```

ChromaDB finds documents with SIMILAR numbers — even if exact words differ!
This is semantic search: search by meaning, not by exact word match.

### The RAG Pipeline (4 steps)

```
Step 1: Embed query      "explain my code" -> [0.34, -0.21, ...]
Step 2: Search ChromaDB  find chunks with similar vectors
Step 3: Retrieve context "IndexError: list index out of range..."
Step 4: Augment + Generate  prompt = context + code -> Llama 3 -> better answer
```

---

## 3. Setup (slightly longer)

Stage 4 installs extra packages:
- `chromadb` — the vector database
- `sentence-transformers` — downloads embedding model (~90MB)

**First run takes about 2 minutes. Be patient!**
After the first run, the model is cached and loads instantly.

---

## 4. Expected Output

```
Loading embedding model (downloads ~90MB first time)...
Embedding model loaded!

Vector size: 384 numbers per text
Similarity: 'IndexError' vs 'list out of bounds' : 0.847 (HIGH)
Similarity: 'IndexError' vs 'banana recipe'      : 0.123 (LOW)

Building vector database...
Vector database ready with 8 chunks!

Searching knowledge base...
Retrieved 2 relevant chunks:
  [1] ZeroDivisionError in Python: Raised when you divide by zero...
  [2] Python Functions Best Practice: handle edge cases explicitly...

RAG-enhanced explanation:
1. WHAT IT DOES: Calculates the average of a list of numbers
2. HOW IT WORKS: Loops through, adds up, divides by count
3. POTENTIAL ISSUES: Will crash with ZeroDivisionError on empty list
   (confirmed by knowledge base: ZeroDivisionError occurs when dividing by 0)
4. BEGINNER TIP: Always validate inputs before processing
```

---

## 5. Code Walkthrough

### Creating embeddings
```python
from langchain_huggingface import HuggingFaceEmbeddings
embeddings = HuggingFaceEmbeddings(
    model_name="all-MiniLM-L6-v2",      # small, fast, good quality
    model_kwargs={"device": "cpu"},      # CPU is fine for classroom
    encode_kwargs={"normalize_embeddings": True}  # consistent scores
)
```

### Building the knowledge base
```python
docs = [Document(page_content=text) for text in PYTHON_KNOWLEDGE]
vectorstore = Chroma.from_documents(
    documents=chunks,
    embedding=embeddings,
    collection_name="codepilot_knowledge"
)
```

### Creating the retriever
```python
retriever = vectorstore.as_retriever(
    search_type="similarity",
    search_kwargs={"k": 2}   # return top 2 most similar chunks
)
```

### The RAG function (4 steps in code)
```python
def rag_explain(code):
    # 1. Find relevant knowledge
    relevant_docs = retriever.invoke(f"Python concepts for: {code[:100]}")
    # 2. Combine into context
    context = "\n\n".join([doc.page_content for doc in relevant_docs])
    # 3. Build augmented prompt
    prompt = RAG_PROMPT.format(context=context, code=code)
    # 4. Generate with context
    return llm.invoke(prompt).content
```

---

## 6. Common Errors and Fixes

### Takes too long to install
Wait — sentence-transformers downloads ~90MB. Only happens once per session.

### HuggingFaceEmbeddings import error
Run all three pip installs: chromadb, langchain-chroma, sentence-transformers, langchain-huggingface.

### ChromaDB returns empty results
Run the full Step 4 code cell to build the vector database first.

---

## 7. Try It Yourself

### Experiment 1 — See similarity scores
Search with different queries and compare what gets retrieved.
Notice: "list out of bounds" retrieves IndexError docs even without using the word "IndexError"!

### Experiment 2 — Add your own knowledge
Add a new Python concept to PYTHON_KNOWLEDGE and rebuild the vectorstore.
Test if it gets retrieved with a related query.

### Experiment 3 — Compare RAG vs no-RAG
Ask the same question with and without context.
Is the RAG version more specific about the actual error?

---

## 8. Key Vocabulary

| Term | Meaning |
|------|---------|
| RAG | Retrieve relevant knowledge, then generate with context |
| Embedding | Text converted to numbers encoding its meaning |
| Vector | The list of numbers representing text meaning |
| Semantic search | Search by meaning, not exact word match |
| ChromaDB | Local vector database — stores and searches embeddings |
| Retriever | Interface for searching the vector store |
| k=2 | Return top 2 most similar chunks |
| Chunk | A piece of a document (split for better retrieval) |

---

## 9. What Comes Next

RAG gives the agent better context. But the agent still gives its first answer immediately.
What if the first answer has gaps or misses edge cases?

**Stage 5 (Reflection)** makes the agent critique and improve its own output automatically.

➡️ Open `stage5_reflection/Stage5_Reflection.ipynb`
