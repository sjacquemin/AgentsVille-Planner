# AgentsVille Trip Planner — Agentic AI Capstone

Design and implementation of an **agentic travel-planning system** that plans a multi-day trip to “AgentsVille,” showcasing **role-based prompting**, **Chain-of-Thought (CoT)** planning, **ReAct** (reason–act–observe) loops, and **self-evaluation feedback** to refine itineraries under changing constraints like weather and traveler preferences.

## Why this project
Recruiters and hiring managers want to see **working evidence** of agentic patterns beyond simple single-prompt demos. This project:
- Implements an **end-to-end ReAct loop** with tool use (mocked APIs), structured state, and observable outputs.
- Uses **typed schemas (Pydantic)** to validate inputs/outputs so agent steps are auditable and production-friendly.
- Demonstrates **feedback-driven iteration**: the agent evaluates and revises a plan based on traveler feedback and environmental constraints (e.g., weather).

---

## Core Features

- **ItineraryAgent**: Generates a day-by-day plan from trip details (duration, interests, constraints) using a guided system prompt and stepwise reasoning.  
- **Typed Contracts**: `VacationInfo` and itinerary data classes (Pydantic v2) ensure **input validity and deterministic structure**.  
- **Tool Use via ReAct**:  
  - **Weather Tool (mock)** returns daily conditions for the trip window.  
  - **Activities Tool (mock)** retrieves candidate events per day; results are consolidated into data frames for ranking and selection.  
- **Constraint Checking**: The agent rejects or swaps activities incompatible with weather (e.g., rain-sensitive outdoor events).  
- **Self-Evaluation / Feedback Loop**: A second agent (“ChatAgent – Evaluator”) scores whether traveler feedback is fully incorporated and explains why, enabling automatic revision cycles.  

---

## System Architecture (Agentic Pattern)

1. **Intent Structuring**  
   - Parse user goals and constraints into `VacationInfo` (typed, validated).  
2. **Plan (CoT)**  
   - The itinerary agent plans per-day activities with explicit reasoning and a guided system prompt.  
3. **Act (Tools)**  
   - Query **Weather API (mock)** and **Activities API (mock)**; materialize into `pandas` DataFrames for explainability.  
4. **Observe**  
   - Evaluate weather–activity compatibility and prune risky picks.  
5. **Critique & Revise**  
   - A feedback evaluator agent checks whether the revised plan meets user feedback (e.g., “≥ 2 activities/day”) and provides a reasoned verdict, feeding another round if needed.  

---

## Tech Stack

- **Python**, **Pandas** for data framing & display.  
- **Pydantic v2** for schema validation.  
- **OpenAI Python SDK** (for LLM agent prompts; can be swapped for a local model).  
- **json-repair**, **numexpr**, **python-dotenv**.  
Install set pinned in-notebook:  
```bash
pip install -q json-repair==0.47.1 numexpr==2.11.0 openai==1.74.0 pandas==2.3.0 pydantic==2.11.7 python-dotenv==1.1.0
```

> **Note:** The project uses **mocked tools** for weather and activities so the agentic loop is reproducible and testable offline.  

---

## Example Outputs

- **Weather frame (excerpt)**  
  | date       | city        | temperature | unit    | condition      | description |
  |------------|-------------|-------------|---------|----------------|-------------|
  | 2025-06-10 | AgentsVille | 31          | celsius | clear          | A bright and sunny day… |
  | 2025-06-12 | AgentsVille | 28          | celsius | thunderstorm   | A thunderstorm is expected… |

- **Activities (excerpt)**  
  Shows ID, name, start/end, location, description, price, interests; e.g., *FutureTech Breakfast Meet-Up* on 2025-06-10 09:00–11:00 at “The Innovation Atrium, Tech District, AgentsVille.”

- **Constraint Enforcement**  
  Outdoor activity auto-flagged as incompatible on thunderstorm day; indoor alternative kept.

- **Feedback Evaluator Prompt (excerpt)**  
  Structured **ANALYSIS** + **FINAL OUTPUT** rubric to verify feedback incorporation.  

---

## How to Run

1. **Clone & set up**
   ```bash
   git clone <your-repo-url>
   cd <repo>
   pip install -r requirements.txt   # or use the pinned cell command above
   ```
2. **Environment**
   - Create a `.env` with `OPENAI_API_KEY=...` (or configure your local LLM endpoint).
3. **Notebook**
   - Open `project_starter.ipynb`, run cells sequentially:
     - Define trip window & preferences
     - Generate weather & activity candidates (mock tools)
     - Run **ItineraryAgent** (planning) → **Evaluator** (feedback) → **revision**

> This repo is structured to make swapping the LLM **trivial**: point the OpenAI SDK at a local server (e.g., vLLM/Ollama) or adapt the call site.

---

## What I Did (Highlights)

- **Designed agent prompts** that elicit stepwise planning (CoT) and tool-aware ReAct behaviors.  
- **Implemented typed data models** and **deterministic intermediate artifacts** (DataFrames) for reviewability and testing.  
- **Engineered a critique loop** that detects requirement drift (e.g., “≥2 activities/day”) and compels plan revision.  
- **Stress-tested constraints** under adverse weather with graceful degradation (swap outdoor → indoor).  

---

## What This Demonstrates

- Practical **Agentic AI**: not just prompting, but orchestrating **multi-step autonomy** with verifiable state and guardrails.
- **Production mindset**: schemas, idempotent mocks, and inspectable data frames make it **testable, auditable, and extensible**.
- **User-in-the-loop design**: the feedback agent provides a **clear rubric** and rationale to drive iterative improvement.  

---

## Next Steps / Extensions

- Replace mocks with **live APIs** (weather, events) behind typed tool interfaces.  
- Add **cost/time optimization** (e.g., ILP/heuristics) for schedule packing.  
- Introduce **multi-agent collaboration** (planner ↔ critic ↔ booker) and **memory** across revisions.  
- Port to **LangGraph** for a visual, persisted DAG of the agent state and transitions.

---

## Setup Notes

If you run in a fresh environment, the notebook includes a pinned install cell for all dependencies.  

---

