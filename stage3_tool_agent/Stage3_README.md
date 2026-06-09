# Stage 3 — Tool Agent 🔧

> **Concept:** Tool Orchestration — detect what the user wants and route to the right specialist skill.

---

## 1. What You Will Learn

- What **tool orchestration** means in agentic AI
- Why **specialised prompts** produce far better output than one generic prompt
- How an **intent router** classifies user requests using keywords
- How a **skill dispatcher** routes to the correct tool
- The foundation of the **ReAct pattern** (Reasoning + Acting)

---

## 2. The Concept

### The Problem (Stage 2 — generic prompt)
One prompt tries to debug, explain, AND review all at once. Output is vague and unfocused.

### The Solution (Stage 3 — tool orchestration)

```
User: "debug this code — it crashes"
          ↓
   detect_intent()       reads keywords → returns "debug"
          ↓
   run_skill("debug")    picks DEBUG_PROMPT
          ↓
   Llama 3               structured bug analysis with fix!
```

### The three skills

| Skill | Role given to Llama 3 | Output structure |
|-------|----------------------|-----------------|
| debug | Expert Python debugger | Bug / Why / Fixed code / Explanation |
| explain | Patient beginner tutor | What / How / Concepts / Tips |
| review | Senior developer | Score / Issues / Improvements / Positives |

> **Analogy:** A hospital sends you to a specialist — cardiologist, neurologist, surgeon.
> One expert per job. The intent router is the receptionist who directs you.

### How keyword matching works

```
request = "debug this code — it crashes"
        ↓
request_lower = "debug this code — it crashes"
        ↓
"debug" in request_lower? YES → return "debug"
```

---

## 3. Setup

Same Groq API key from Stage 1. Run Cell 1 (API key) and Cell 2 (install) first.

---

## 4. Expected Output

```
Testing all 3 skills on the SAME code:

Request: 'This code crashes with empty list. Can you debug it?'
  Detected intent: debug
  Routing to: Debug Skill

  CodePilot (Debug Skill):
  1. BUG FOUND: ZeroDivisionError when empty list is passed
  2. WHY IT HAPPENS: len([]) returns 0, dividing by 0 raises ZeroDivisionError
  3. FIXED CODE:
     def calculate_average(numbers):
         if not numbers:
             return 0
         return sum(numbers) / len(numbers)
  4. EXPLANATION: Added guard clause to handle empty list edge case

---
Request: 'Can you explain what this code does?'
  Detected intent: explain
  Routing to: Explain Skill

  CodePilot (Explain Skill):
  1. WHAT IT DOES: Calculates the arithmetic average of a list of numbers
  2. HOW IT WORKS: Loops through numbers, adds them, divides by count...

---
KEY INSIGHT: Same code, 3 different requests -> 3 very different outputs.
The intent router picked the right specialist automatically!
```

---

## 5. Code Walkthrough

### Intent detection
```python
def detect_intent(user_request: str) -> str:
    request_lower = user_request.lower()    # case-insensitive matching
    debug_keywords   = ["debug", "fix", "error", "bug", "crash", ...]
    explain_keywords = ["explain", "what does", "understand", ...]
    review_keywords  = ["review", "improve", "better", "quality", ...]

    if any(kw in request_lower for kw in debug_keywords):
        return "debug"
    elif any(kw in request_lower for kw in explain_keywords):
        return "explain"
    ...
```
`any()` returns True if ANY keyword from the list is found in the request.

### Skill prompt (debug example)
```python
DEBUG_PROMPT = PromptTemplate(
    input_variables=["code"],
    template="""You are an expert Python debugger.
1. BUG FOUND: [describe the bug]
2. WHY IT HAPPENS: [root cause]
3. FIXED CODE: [complete corrected code]
4. EXPLANATION: [what changed and why]
Code: {code}"""
)
```

### Skill dispatcher
```python
def run_skill(intent: str, code: str) -> str:
    if intent == "debug":
        prompt = DEBUG_PROMPT.format(code=code)  # fills {code} placeholder
    elif intent == "explain":
        prompt = EXPLAIN_PROMPT.format(code=code)
    ...
    return llm.invoke(prompt).content
```

---

## 6. Common Errors and Fixes

### All requests route to "unknown"
Request doesn't contain recognised keywords. Use clear words like "debug", "explain", "review".

### Response not structured (just a paragraph)
Make the prompt stricter: add "You MUST use this EXACT numbered format."

### Wrong skill selected
Request contains keywords from multiple categories. The first match wins.

---

## 7. Try It Yourself

### Experiment 1 — Test with your own code
Replace SAMPLE_CODE with a bug from your own assignment. Try all 3 request types.

### Experiment 2 — Add an optimize skill
Add keywords: `["optimize", "slow", "performance", "faster"]`
Create OPTIMIZE_PROMPT and add it to run_skill().

### Experiment 3 — Use LLM for routing
Replace keyword matching with:
```python
r = llm.invoke(f"Classify as debug/explain/review/unknown. Reply ONE word only: '{request}'")
return r.content.strip().lower()
```
More flexible but uses an extra API call. Better for ambiguous requests.

---

## 8. Key Vocabulary

| Term | Meaning |
|------|---------|
| Tool orchestration | Routing tasks to specialised skills |
| Intent detection | Classifying what the user wants |
| Skill dispatcher | Function that picks the right tool |
| ReAct pattern | Reasoning (detect intent) + Acting (run skill) |
| PromptTemplate | Prompt with {variable} placeholders |

---

## 9. What Comes Next

Stages 1–3 complete! Stages 4–6 are homework.

| Stage | Adds |
|-------|------|
| Stage 4 | Long-term memory — ChromaDB + RAG |
| Stage 5 | Self-improvement — reflection loop |
| Stage 6 | Graph workflow — LangGraph + multi-agent |

➡️ Open `stage4_rag_explain/Stage4_RAG_Explain.ipynb`
