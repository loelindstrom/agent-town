# Roadmap: Self-Sustained AI Agent Crew (Prototype)

## Goal
A **Linux cloud instance** running:
- A **local LLM** (fast, private, unlimited)
- A **CrewAI agent system** with:
  - **1 Master Agent** (user-facing, delegates tasks)
  - Access to **terminal (bash)** → can create/edit agents, run commands
  - Ability to **spawn new sub-agents** dynamically
  - **Traceable logs**, **Git integration**, **steering doc** for context

---

## Step-by-Step Prototype (Testable, Bite-Sized)

| Step | Action | Test |
|------|--------|------|
| 1 | **Choose local LLM** | `qwen2.5-coder:7b-q4_0` (fast, 4GB RAM) or `deepseek-coder:16b-q4_K_M` (stronger, 8GB+) |
| 2 | **Get cheap Linux VM** | DigitalOcean **$6/mo** (2 vCPU, 4GB RAM) — or **Oracle Always Free** (2 VMs, 1GB each) |
| 3 | **Install Ollama + LLM** | `curl -fsSL https://ollama.com/install.sh | sh`<br>`ollama pull qwen2.5-coder:7b-q4_0` |
| 4 | **Test LLM** | `ollama run qwen2.5-coder:7b` → “Write a Python function to calculate mortgage” → works? |
| 5 | **Install CrewAI** | `pip install crewai langchain-ollama` |
| 6 | **Create Master Agent (Python)** | Simple script: master has `bash` tool, uses local LLM, can read/write files |
| 7 | **Test Master** | Prompt: “List files in current directory” → uses `ls` via bash → success |
| 8 | **Add Chat UI (optional)** | `pip install streamlit` → `streamlit run master.py` → chat in browser |

**Done: Working prototype. You can now chat with the master.**

---

## Early Robustness (Do This First)

1. **Agree on Sub-Agent Template**  
   Master creates new agents by **appending to `agents.py`** using this format:
   ```python
   coder = Agent(
       role="Frontend Coder",
       goal="Build React components",
       backstory="...",
       llm=llm,
       tools=[bash_tool]
   )
2. Create steering_doc.md
    - Project name, goals, architecture
    - Where to find: guidelines/, logs/, app/
    - Updated by master after long chats → enables reset & restart

1. Test Delegation
Prompt:
“Create a Coder agent, clone this repo: github.com/user/house-bid, add a file calc.js, commit and push.”
→ Master spawns Coder → Coder runs git clone, edits, pushes → logs shown

---

## Nice-to-Have (Add Later)

Traceability: All agent actions logged to logs/trace.log with timestamps, agent name, command
Git Visibility: Auto-commit on task complete → git log --oneline shows agent work
Live Preview: npm run dev → open port 3000 → see app in browser
Hybrid LLMs: Master uses cloud LLM (Grok/Claude) for planning; subs use local for speed

---

## One Project = One Instance
Keeps context clean
Reuse: git clone your agents/, guidelines/, steering_doc.md to new instance  
