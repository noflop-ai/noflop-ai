# Noflop.ai — Production Codebase

> Don't let your idea flop.
> AI-powered startup idea validation engine. Brutal honesty. 9-dimension scoring. Real signal.

---

## What This Is

Noflop.ai is NOT a chatbot or a GPT wrapper.
It is a structured decision system — a production-grade validation engine that evaluates startup ideas across 9 dimensions using Claude Sonnet 4.5.

**Stack:**
- Backend: FastAPI (Python)
- AI: Claude Sonnet 4.5 via Anthropic API
- Frontend: React
- Storage: localStorage (v1) — no database needed

---

## Project Structure

```
noflop/
├── backend/
│   ├── app/
│   │   ├── main.py                  # FastAPI entry point
│   │   ├── api/
│   │   │   └── routes.py            # /evaluate endpoint
│   │   ├── core/
│   │   │   ├── config.py            # API keys, weights, settings
│   │   │   └── orchestrator.py      # Main pipeline — YOUR CORE IP
│   │   ├── modules/
│   │   │   ├── problem.py           # Problem strength evaluator
│   │   │   ├── market.py            # Market reality evaluator
│   │   │   ├── dimensions.py        # ICP, Behavior, Feasibility, Monetization, Advantage, Execution, Risk
│   │   │   └── synthesis.py         # Differentiation, User Questions, Similar Flops, Final Synthesis
│   │   ├── schemas/
│   │   │   └── schemas.py           # Pydantic input/output validation
│   │   ├── services/
│   │   │   └── llm_service.py       # All Claude API calls with retry logic
│   │   └── utils/
│   │       └── scoring.py           # Weighted scoring formula + verdict logic
│   ├── requirements.txt
│   └── .env.example
│
└── frontend/
    └── src/
        ├── App.jsx                  # Main app with state management
        ├── App.css                  # Dark theme styles
        ├── components/
        │   ├── IdeaForm.jsx         # 15-field structured input form
        │   └── ResultCard.jsx       # Full result display component
        └── services/
            └── api.js               # API calls to backend
```

---

## Setup — Backend

### Step 1: Clone and navigate
```bash
cd noflop/backend
```

### Step 2: Create virtual environment
```bash
python -m venv venv
source venv/bin/activate        # Mac/Linux
venv\Scripts\activate           # Windows
```

### Step 3: Install dependencies
```bash
pip install -r requirements.txt
```

### Step 4: Set up environment variables
```bash
cp .env.example .env
```

Open `.env` and add your Anthropic API key:
```
ANTHROPIC_API_KEY=sk-ant-your-key-here
MODEL=claude-sonnet-4-5
MAX_TOKENS=2000
CORS_ORIGINS=http://localhost:3000
```

Get your API key from: https://console.anthropic.com

### Step 5: Run the backend
```bash
uvicorn app.main:app --reload --port 8000
```

Backend is now live at: http://localhost:8000
API docs at: http://localhost:8000/docs

---

## Setup — Frontend

### Step 1: Navigate to frontend
```bash
cd noflop/frontend
```

### Step 2: Install dependencies
```bash
npm install
```

### Step 3: Create environment file
```bash
echo "REACT_APP_API_URL=http://localhost:8000/api/v1" > .env
```

### Step 4: Run the frontend
```bash
npm start
```

Frontend is now live at: http://localhost:3000

---

## How It Works — The Pipeline

```
User fills 15-field form
        ↓
POST /api/v1/evaluate
        ↓
FastAPI receives IdeaInput (Pydantic validated)
        ↓
Orchestrator runs 9 dimension modules in parallel
        ↓
Each module calls Claude with a strict JSON-only prompt
        ↓
Scores extracted, validated, clamped to 0-10
        ↓
Weighted formula calculates final score
        ↓
Synthesis module generates strengths, risks, insights
        ↓
Differentiation + User Questions + Similar Flops generated
        ↓
Verdict determined: BUILD / REFINE / AVOID
        ↓
NoFlopOutput returned as JSON
        ↓
Frontend renders full result card
```

---

## Scoring Formula

```
Final Score =
  Problem      × 0.20
  Market       × 0.15
  Behavior     × 0.15
  ICP          × 0.10
  Feasibility  × 0.10
  Monetization × 0.10
  Advantage    × 0.10
  Execution    × 0.05
  Risk         × 0.05
```

**Verdict thresholds:**
- Score ≥ 7.0 → BUILD IT
- Score ≥ 4.0 → REFINE IT
- Score < 4.0 → AVOID IT

To change weights, edit `backend/app/core/config.py`

---

## API Reference

### POST /api/v1/evaluate

**Request body:**
```json
{
  "idea": "string",
  "target_user": "string",
  "problem": "string",
  "current_behavior": "string",
  "trigger_moment": "string",
  "frequency": "string",
  "pain_level": "string",
  "existing_alternatives": "string",
  "monetization_idea": "string (optional)",
  "willingness_to_pay": "yes | no | unsure",
  "why_now": "string",
  "unfair_advantage": "string",
  "mvp_plan": "string",
  "time_to_build": "string",
  "failure_risk": "string"
}
```

**Response:**
```json
{
  "verdict": "BUILD IT | REFINE IT | AVOID IT",
  "score": {
    "problem": 7,
    "market": 5,
    "icp": 6,
    "behavior": 4,
    "feasibility": 8,
    "monetization": 5,
    "advantage": 4,
    "execution": 7,
    "risk": 6,
    "final": 5.8
  },
  "strengths": ["...", "...", "..."],
  "risks": ["...", "...", "..."],
  "critical_insights": ["...", "..."],
  "actionable_suggestions": ["...", "...", "..."],
  "differentiation_angles": ["...", "...", "..."],
  "similar_flops": [
    { "name": "...", "what_they_tried": "...", "why_failed": "..." }
  ],
  "user_research_questions": ["...", "...", "..."],
  "verdict_reason": "..."
}
```

---

## Deploying to Production

### Backend — Deploy to Railway or Render

1. Push backend folder to GitHub
2. Connect to Railway (railway.app) or Render (render.com)
3. Set environment variables in the dashboard:
   - ANTHROPIC_API_KEY
   - MODEL
   - CORS_ORIGINS (set to your Vercel frontend URL)
4. Start command: `uvicorn app.main:app --host 0.0.0.0 --port $PORT`

### Frontend — Deploy to Vercel

1. Push frontend folder to GitHub
2. Connect to Vercel (vercel.com)
3. Set environment variable:
   - REACT_APP_API_URL = your Railway/Render backend URL
4. Deploy

---

## What to Build Next (Roadmap)

### Phase 1 — Immediate improvements
- [ ] Add real-time web search signal (SerpAPI or Tavily)
- [ ] Add confidence score when AI is uncertain about an input
- [ ] Build outcome tracking — founders report back what happened
- [ ] Add PDF export of validation result
- [ ] Add idea history with localStorage

### Phase 2 — Community and moat
- [ ] Public idea board — optional sharing
- [ ] Failure database — founders submit dead startups
- [ ] Builder leaderboard
- [ ] Founder blind spots detector

### Phase 3 — B2B and API
- [ ] Validation-as-a-Service API for accelerators
- [ ] Bulk validation for cohorts
- [ ] Investor readiness score
- [ ] White-label output

---

## Key Design Decisions

**Why FastAPI?**
Fast, async, automatic API docs via /docs. Perfect for LLM workloads.

**Why separate modules per dimension?**
Each dimension gets its own focused prompt. Better quality than one giant prompt. Each can be improved independently.

**Why JSON-only prompts with retry logic?**
LLMs sometimes return markdown or loose text. Retry logic + JSON extraction ensures the pipeline never breaks.

**Why no database in v1?**
localStorage is enough to validate the concept. Add Postgres in v2 when you have real users.

---

## Environment Variables Reference

| Variable | Required | Default | Description |
|---|---|---|---|
| ANTHROPIC_API_KEY | Yes | None | Your Anthropic API key |
| MODEL | No | claude-sonnet-4-5 | Claude model to use |
| MAX_TOKENS | No | 2000 | Max tokens per LLM call |
| CORS_ORIGINS | No | http://localhost:3000 | Allowed frontend origins |

---

## Built By

Sahib — AI Engineer, Founder of Indian Data Club
Built at VibeCon India 2026

---

*noflop.ai — Built for builders who want truth, not therapy.*
