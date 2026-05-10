# 🤖 AI-Powered Engineering Team — Multi-Agent Code Generation Pipeline

> Built with **CrewAI** · **OpenAI GPT-4o** · **Gradio**

---

## 📌 Overview

This project demonstrates a **multi-agent software engineering pipeline** powered by CrewAI.  
Given a set of high-level requirements, the system automatically designs, implements, tests, and builds a UI for a Python module — entirely through coordinated AI agents.

The pipeline targets a **Trading Simulation Account Management System** as its first use case, producing a fully functional Python module with a Gradio UI and unit tests.

---

## 🚀 How It Works

The crew runs **sequentially** — each agent hands off its output to the next:

```
Requirements Input
      ↓
 Engineering Lead  →  Design Document (.md)
      ↓
 Backend Engineer  →  Python Module (accounts.py)
      ↓
 Frontend Engineer →  Gradio UI (app.py)
      ↓
 Test Engineer     →  Unit Tests (test_accounts.py)
```

Each agent reads the previous agent's output as context, ensuring a coherent end-to-end pipeline.

---

## 🧠 Agentic Design Patterns Used

### 1. Sequential Process
All agents run one after another using `Process.sequential`, where each task feeds into the next:

```python
@crew
def crew(self) -> Crew:
    return Crew(
        agents=self.agents,
        tasks=self.tasks,
        process=Process.sequential,
        verbose=True,
    )
```

### 2. Task Context Chaining
Each task explicitly declares which previous tasks it depends on using `context`:

```yaml
code_task:
  context:
    - design_task   # Backend engineer reads the design first

frontend_task:
  context:
    - code_task     # Frontend engineer reads the implemented module
```

### 3. Code Execution Agents
The Backend Engineer and Test Engineer have live code execution enabled via Docker for safety:

```python
backend_engineer = Agent(
    allow_code_execution=True,
    code_execution_mode="safe",   # Runs inside Docker
    max_execution_time=500,
    max_retry_limit=3
)
```

### 4. File Output per Task
Each task saves its output directly to a file:

```yaml
design_task:
  output_file: output/{module_name}_design.md

code_task:
  output_file: output/{module_name}

frontend_task:
  output_file: output/app.py

test_task:
  output_file: output/test_{module_name}
```

---

## 👥 The Agents

| Agent | Role | LLM | Key Capability |
|---|---|---|---|
| **Engineering Lead** | Produces detailed module design | GPT-4o | Technical writing & architecture |
| **Backend Engineer** | Implements the Python module | GPT-4o | Code generation + execution |
| **Frontend Engineer** | Builds the Gradio UI | GPT-4o | UI generation from backend spec |
| **Test Engineer** | Writes unit tests | GPT-4o | Test generation + execution |

---

## 📋 The Tasks

| Task | Input | Output |
|---|---|---|
| `design_task` | Requirements | `{module_name}_design.md` |
| `code_task` | Design doc | `{module_name}` (Python module) |
| `frontend_task` | Python module | `app.py` (Gradio UI) |
| `test_task` | Python module | `test_{module_name}` |

---

## 📁 Project Structure

```
engineering_team/
├── src/
│   └── engineering_team/
│       ├── config/
│       │   ├── agents.yaml        ← Agent definitions
│       │   └── tasks.yaml         ← Task definitions
│       ├── __init__.py
│       ├── crew.py                ← Crew assembly
│       └── main.py                ← Entry point & requirements
├── example_output_4o/             ← Sample output (GPT-4o run)
│   ├── accounts.py                ← Generated backend module
│   ├── accounts.py_design.md      ← Generated design document
│   ├── app.py                     ← Generated Gradio UI
│   └── test_accounts.py           ← Generated unit tests
├── knowledge/
├── .env                           ← API keys (not committed)
├── .gitignore
├── pyproject.toml
├── README.md
└── uv.lock
```

---

## ⚙️ Setup & Installation

### Prerequisites

- Python 3.10–3.12
- [uv](https://docs.astral.sh/uv/) package manager
- OpenAI API key
- Docker (for code execution agents)

### Install

```bash
git clone https://github.com/yourusername/engineering_team.git
cd engineering_team
uv sync
```

### Environment Variables

Create a `.env` file in the root:

```env
OPENAI_API_KEY=sk-your-openai-key-here
```

---

## 🚀 Running the Crew

```bash
uv run run_crew
```

The crew will generate all output files inside the `output/` directory.

### Running the Generated Gradio UI

After the crew finishes:

```bash
cd output
uv run app.py
```

Then open: [http://127.0.0.1:7860](http://127.0.0.1:7860)

---

## 📦 Example Output — Trading Simulation

The `example_output_4o/` folder contains a real run output for this requirement:

> *"A simple account management system for a trading simulation platform — supporting deposits, withdrawals, share buying/selling, portfolio valuation, and transaction history."*

| File | Description |
|---|---|
| `accounts.py_design.md` | Architecture design by Engineering Lead |
| `accounts.py` | Fully implemented `Account` class |
| `app.py` | Gradio UI for interacting with the account |
| `test_accounts.py` | Unit tests for the `Account` class |

---

## 📦 Dependencies

```
crewai[tools]>=0.108.0
gradio>=5.22.0
```

Managed via `uv` — no manual pip installs needed.

---

## 🧪 Tech Stack

| Component | Technology |
|---|---|
| Agent Framework | CrewAI |
| LLM | GPT-4o |
| UI | Gradio |
| Code Execution | Docker (safe mode) |
| Package Manager | uv |
| Env Management | python-dotenv |

---

## 👤 About the Author

**Abdulrahman Salah** — AI Engineer  
Passionate about designing and building intelligent systems that combine LLMs, automation, and scalable backend architecture.

🔗 LinkedIn: [abdulrahman--salah](https://www.linkedin.com/in/abdulrahman--salah/)  
📧 Email: [abdulrahmansalah107@gmail.com](mailto:abdulrahmansalah107@gmail.com)
