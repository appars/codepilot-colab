# Stage 5 — Reflection Loops 🔄

> **Concept:** The agent generates a fix, critiques its own output, and improves it — automatically, without human help.

---

## 1. What You Will Learn

- What a **reflection loop** is in agentic AI
- How **Generate → Critique → Improve** works in practice
- Why self-critique produces dramatically better output quality
- How `max_iterations` prevents infinite loops safely
- Why this separates basic AI agents from production-grade ones

---

## 2. The Concept

### Human analogy

```
Student WITHOUT reflection:       Student WITH reflection:
Write exam answer -> submit       Write answer -> re-read ->
                                  spot mistake -> fix -> check again
Result: mediocre mark             -> submit
                                  Result: much better mark!
```

The reflection loop gives the AI this same self-awareness.

### The three-phase loop

```
     Phase 1
     Generate Fix
          |
          v
     Phase 2
     Critique Fix --- NEEDS_IMPROVEMENT --> Phase 3: Improve Fix
          |                                        |
       APPROVED                              (loop back to Phase 2)
          |
          v
     Return Final Fix
```

### Three prompts, same Llama 3 model

| Prompt | Role | Job |
|--------|------|-----|
| GENERATE_PROMPT | Junior developer | Write first fix (not perfect) |
| CRITIQUE_PROMPT | Strict senior engineer | Review harshly, find all gaps |
| IMPROVE_PROMPT | Expert developer | Incorporate all feedback |

This is **prompt engineering** — one model, three different behaviours!

### Why max_iterations?

Without a limit, a strict critique could say NEEDS_IMPROVEMENT forever.
max_iterations=2 means: try at most 2 improvement cycles, then stop.
In production, 2-3 iterations give the best quality/speed tradeoff.

---

## 3. Setup

Same Groq API key. Run Cell 1 and Cell 2 first.

---

## 4. Expected Output

```
Step 1: Generating initial fix...
  Initial fix: 312 chars

  Step 2 (iteration 1): Critiquing the fix...
  Critique says NEEDS_IMPROVEMENT. Improving...

  Step 2 (iteration 2): Critiquing the fix...
  Critique APPROVED after 2 iteration(s)!

INITIAL FIX (before reflection):
def calculate_average(numbers):
    if len(numbers) == 0:
        return 0
    return sum(numbers) / len(numbers)
NOTE: handles empty list but NOT wrong types

FINAL FIX (after reflection):
def calculate_average(numbers):
    if not isinstance(numbers, list):
        raise TypeError(f"Expected list, got {type(numbers).__name__}")
    if not numbers:
        return 0.0
    if not all(isinstance(n, (int, float)) for n in numbers):
        raise TypeError("All elements must be numbers")
    return sum(numbers) / len(numbers)
NOTE: handles empty list AND wrong types AND non-numeric elements!

Iterations run: 2
Approved: True

KEY: The agent improved its OWN output — no human intervention needed!
```

---

## 5. Code Walkthrough

### The reflection loop
```python
def reflection_review(code, max_iterations=2):
    # Phase 1: Generate first fix
    current_fix = llm.invoke(GENERATE_PROMPT.format(code=code)).content

    # Phase 2: Critique -> Improve loop
    for i in range(max_iterations):
        critique = llm.invoke(CRITIQUE_PROMPT.format(
            original_code=code, proposed_fix=current_fix
        )).content

        # Check if approved
        if "APPROVED" in critique.upper() and "NEEDS_IMPROVEMENT" not in critique.upper():
            break   # stop early -- good enough!

        # Not approved -- improve based on feedback
        current_fix = llm.invoke(IMPROVE_PROMPT.format(
            original_code=code,
            previous_fix=current_fix,
            critique=critique
        )).content

    return current_fix
```

### Why check for "APPROVED"?
The CRITIQUE_PROMPT tells Llama 3 to write exactly "APPROVED" or "NEEDS_IMPROVEMENT".
The code checks for this keyword to decide whether to loop or stop.
This shows why prompt design matters — the output format affects program logic!

---

## 6. Common Errors and Fixes

### Agent always says APPROVED immediately
CRITIQUE_PROMPT is not strict enough. Add: "You MUST find at least one issue."

### Agent never gets APPROVED (always loops max times)
This is fine — max_iterations stops it. Final fix is still improved.

### Takes too long
Each iteration is a separate LLM call (up to 5 calls total).
Use max_iterations=1 for faster testing.

---

## 7. Try It Yourself

### Experiment 1 — Compare initial vs final
Print both side by side. What specific improvements did reflection add?

### Experiment 2 — Make critique stricter
Add to CRITIQUE_PROMPT: "Always find at least 2 issues. Check docstrings, type hints, edge cases."
Does the final fix improve more dramatically?

### Experiment 3 — Test with already-good code
Pass a well-written function with docstrings and type hints.
Does the critique say APPROVED immediately?
What does it find to critique in perfect code?

---

## 8. Key Vocabulary

| Term | Meaning |
|------|---------|
| Reflection loop | Agent evaluates and improves its own output |
| Generate then Critique then Improve | The three phases of self-improvement |
| Early stopping | Exit loop when APPROVED — no need to keep going |
| max_iterations | Safety limit to prevent infinite loops |
| Prompt persona | Using different role descriptions for different tasks |
| Self-improvement | Getting better without any human feedback |

---

## 9. What Comes Next

Reflection improves quality. But the flow is still sequential.
What if we needed a workflow that can branch, loop back, or take different paths?

**Stage 6 (LangGraph)** gives us a stateful graph where the agent controls its own path.

➡️ Open `stage6_langgraph/Stage6_LangGraph.ipynb`
