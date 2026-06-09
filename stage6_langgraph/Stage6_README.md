# Stage 6 — LangGraph Workflow 🕸️

> **Concept:** Stateful graph-based workflow with conditional branching, loops, and simulated multi-agent conversation.

---

## 1. What You Will Learn

- Why linear chains are **not enough** for complex agent workflows
- What a **StateGraph** is — nodes, edges, and shared state
- How **state** flows between nodes like a shared whiteboard
- How **conditional edges** create dynamic branching and loops
- How to simulate **multi-agent conversation** (the AutoGen concept)

---

## 2. The Concept

### LangChain vs LangGraph

```
LangChain (linear):          LangGraph (dynamic):

A -> B -> C -> END           analyze -> fix -> reflect
                                                |
Always same path.            approved? YES -> END
Cannot loop back.                      NO  -> analyze (loop!)
Cannot branch.
                             Path decided at RUNTIME!
```

### The State Machine

```
START
  |
  v
analyze_node    reads: code
                writes: analysis
  |
  v
fix_node        reads: analysis
                writes: fix, iteration
  |
  v
reflect_node    reads: fix
                writes: critique, approved
  |
  v
should_continue() -> "end" or "analyze"
  |                           |
  v (end)                     v (analyze)
 END                     loop back!
```

### AgentState — The Shared Whiteboard

```python
class AgentState(TypedDict):
    code:      str    # the buggy code (never changes)
    analysis:  str    # written by analyze_node
    fix:       str    # written by fix_node
    critique:  str    # written by reflect_node
    iteration: int    # incremented by fix_node
    approved:  bool   # True when critique approves
```

Think of this as a **shared whiteboard** in a meeting room.
Every node reads from it and writes updates to it.
No node passes data directly to another — they all use the state!

### Multi-Agent (AutoGen Concept)

```
DebuggerBot: "Found ZeroDivisionError. Here is my fix: added divisor check."
ReviewerBot: "Handles zero but misses empty list. REJECTED."
DebuggerBot: "Good catch! Updated: handles both cases now."
ReviewerBot: "Both bugs fixed. APPROVED."
```

Each agent has ONE specialised prompt. They share outputs as messages.
This is the AutoGen pattern — without needing the full AutoGen framework!

---

## 3. Setup

```
pip install langchain-groq langchain langchain-community langgraph
```

Same Groq API key. Run Cell 1 and Cell 2 first.

---

## 4. Expected Output

```
CodePilot AI Studio — Stage 6: LangGraph Workflow
Running graph...

  [Node: ANALYZE] iteration 1
  [Node: FIX] generating fix...
  [Node: REFLECT] critiquing fix...
  Result: NEEDS_IMPROVEMENT
  Completed: reflect | iteration: 1

  [Node: ANALYZE] iteration 2
  [Node: FIX] generating fix...
  [Node: REFLECT] critiquing fix...
  Result: APPROVED
  Completed: reflect | iteration: 2

FINAL OUTPUT:
Fix (iteration 2):
def divide_all(numbers, divisor):
    if divisor == 0:
        raise ValueError("divisor cannot be zero")
    if not numbers:
        return []
    return [num / divisor for num in numbers]

KEY: The graph decided its own path based on the critique result!

=== Multi-Agent Review ===
[DebuggerBot] Generating fix...
[ReviewerBot] Reviewing DebuggerBot's fix...

DebuggerBot: Fixed ZeroDivisionError with guard clause...
ReviewerBot: APPROVED — all edge cases handled. Ready to ship.
```

---

## 5. Code Walkthrough

### Building the graph
```python
graph = StateGraph(AgentState)

# Add nodes (name -> function)
graph.add_node("analyze", analyze_node)
graph.add_node("fix",     fix_node)
graph.add_node("reflect", reflect_node)

# Fixed edges: always go here
graph.add_edge("analyze", "fix")
graph.add_edge("fix", "reflect")

# Dynamic edge: decided at runtime by should_continue()
graph.add_conditional_edges(
    "reflect",
    should_continue,                          # this function returns next node name
    {"analyze": "analyze", "end": END}        # maps return value to node
)

graph.set_entry_point("analyze")
app = graph.compile()
```

### Conditional routing function
```python
def should_continue(state: AgentState) -> str:
    if state["approved"]:
        return "end"       # stop -- fix is approved!
    elif state["iteration"] >= 2:
        return "end"       # stop -- hit max iterations
    else:
        return "analyze"   # loop back -- try again!
```

This function returns a STRING that maps to the next node.
LangGraph uses this to dynamically route execution!

### Running the graph
```python
for step in app.stream(initial_state):
    node_name = list(step.keys())[0]
    final_state = step[node_name]
    print(f"Completed: {node_name}")
```
`app.stream()` runs the graph and yields state after EACH node.
Watch the nodes execute in real time!

---

## 6. Common Errors and Fixes

### ModuleNotFoundError: No module named 'langgraph'
Run the setup cell: `!pip install -q langgraph`

### ValueError: Node not found
Node name in add_edge() doesn't match name in add_node(). Check spelling.

### Graph runs forever
should_continue() never returns "end". Lower MAX_ITER to 1 to force stop.

---

## 7. Try It Yourself

### Experiment 1 — Add a documentation node
Add `document_node` after reflect_node that writes a docstring.
Change the conditional edge to route to "document" when approved.
Add "document" -> END edge.

### Experiment 2 — Watch state evolve
After each node, print the iteration and approved values.
Watch how state changes as the graph progresses!

### Experiment 3 — Add a third agent
Add TesterBot to multi_agent_review():
```python
tester = llm.invoke(
    f"You are TesterBot. Write 3 pytest test cases for this:\n{result['debugger']}"
)
```

---

## 8. Key Vocabulary

| Term | Meaning |
|------|---------|
| StateGraph | Graph where nodes share a typed state dictionary |
| Node | Function: reads state, does job, returns updates only |
| Edge | Fixed connection between two nodes |
| Conditional edge | Dynamic routing decided at runtime |
| should_continue() | Returns next node name as a string |
| graph.compile() | Validates graph and prepares for execution |
| app.stream() | Runs graph, yields state after each node |
| Multi-agent | Multiple specialised agents passing outputs as messages |

---

## Congratulations! All 6 Stages Complete!

You have built a complete CodePilot AI Studio:

- **Stage 1:** Cloud LLM via Groq API
- **Stage 2:** Short-term conversation memory
- **Stage 3:** Intent routing to 3 specialised skills
- **Stage 4:** Long-term memory with RAG and ChromaDB
- **Stage 5:** Self-improving reflection loop
- **Stage 6:** Stateful graph workflow and multi-agent system

This is the foundation of production-grade agentic AI systems!
