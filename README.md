# 🧠 AI Decision Simulator System

A **multi-agent AI application** that takes any decision problem and simulates future scenarios, predicts outcomes, calculates risk, and delivers a final recommendation — all powered by Google Gemini.

---

## 📐 Architecture

```
ai_decision_simulator/
│
├── app.py                     # Streamlit UI (main entry point)
│
├── agents/
│   ├── context_analyzer.py    # Agent 1: extracts structured context from user input
│   ├── scenario_generator.py  # Agent 2: generates 4 realistic future scenarios
│   ├── outcome_predictor.py   # Agent 3: predicts positive/negative outcomes per scenario
│   └── decision_engine.py     # Agent 4: pure Python scoring & ranking (no AI)
│
├── core/
│   ├── pipeline.py            # Orchestrates the full agent workflow
│   ├── prompts.py             # All Gemini prompts (structured for JSON output)
│   └── schemas.py             # JSON schemas and Python dataclasses
│
├── utils/
│   ├── risk_calculator.py     # Computes 0–100 risk score per scenario
│   └── helpers.py             # JSON parsing, text formatting, label utilities
│
├── data/
│   └── history.json           # Persisted past decisions (auto-updated)
│
├── requirements.txt
└── README.md
```

---

## 🚀 Setup Instructions

### 1. Clone or download the project

```bash
git clone <repo-url>
cd ai_decision_simulator
```

### 2. Create and activate a virtual environment

```bash
python -m venv venv
source venv/bin/activate       # macOS/Linux
venv\Scripts\activate          # Windows
```

### 3. Install dependencies

```bash
pip install -r requirements.txt
```

### 4. Add your Gemini API Key

The API key is entered **directly in the Streamlit sidebar UI** at runtime — no `.env` file required.

If you prefer to hardcode it for development, open `core/pipeline.py` and locate:

```python
def __init__(self, api_key: str):
    genai.configure(api_key=api_key)
```

You can replace `api_key` with your key directly:
```python
GOOGLE_API_KEY = "your_api_key_here"   # ← insert your key here
genai.configure(api_key=GOOGLE_API_KEY)
```

Get your free Gemini API key at: **https://aistudio.google.com**

---

## ▶️ Running the App

```bash
streamlit run app.py
```

The app opens at **http://localhost:8501**

---

## 🤖 Agent Pipeline

| Step | Agent | AI? | Output |
|------|-------|-----|--------|
| 1 | `ContextAnalyzer` | ✅ Gemini | Structured decision context (JSON) |
| 2 | `ScenarioGenerator` | ✅ Gemini | 4 future scenario paths (JSON) |
| 3 | `OutcomePredictor` | ✅ Gemini | Positive/negative impacts per scenario |
| 4 | `RiskCalculator` | ❌ Pure Python | Risk score 0–100 per scenario |
| 5 | `DecisionEngine` | ❌ Pure Python | Ranked scenarios + final recommendation |

---

## 💡 Example Inputs & Outputs

### Input
```
Should I switch jobs to a startup offering 30% more pay but higher risk?
```

### Output (summary)

**Context extracted:**
- Decision type: `career`
- Primary goal: Increase income and career growth
- Constraints: Financial stability, risk tolerance
- Urgency: `medium`

**Scenarios generated:**
1. Accept the startup offer immediately
2. Negotiate current role for raise first
3. Explore multiple options before deciding
4. Stay and upskill for 6 months

**Risk scores:**
- Scenario 1: 62/100 (High Risk)
- Scenario 2: 28/100 (Low Risk)
- Scenario 3: 35/100 (Moderate)
- Scenario 4: 22/100 (Low Risk)

**Recommendation:** Negotiate current role first (highest composite score, lowest regret potential)

---

### More example inputs:
- `"Should I relocate to Germany for a tech job?"`
- `"Should I invest $50k in real estate or index funds?"`
- `"Should I drop out of college to pursue my startup?"`
- `"Should I end my long-term relationship?"`
- `"Should I go back to school for an MBA?"`

---

## 🔧 Configuration

### Gemini Model
Default model: `gemini-1.5-flash` (fast + capable). To switch to Gemini Pro:

In `core/pipeline.py`:
```python
model = genai.GenerativeModel("gemini-1.5-pro")
```

### Risk Score Weights
Adjust weights in `utils/risk_calculator.py` under the `WEIGHTS` dict.

### Decision Engine Weights
Adjust scoring weights in `agents/decision_engine.py` under `SCORE_WEIGHTS`.

---

## 📦 Dependencies

| Package | Purpose |
|---------|---------|
| `streamlit` | Web UI framework |
| `google-generativeai` | Gemini API client |

---

## 📝 History

All past analyses are automatically saved to `data/history.json`. The last 5 are shown in the sidebar. You can clear history programmatically:

```python
from core.pipeline import DecisionPipeline
pipeline = DecisionPipeline(api_key="your_key")
pipeline.clear_history()
```

---

## 🏗️ Extending the System

- **Add a new agent**: Create a class in `agents/`, add its prompt to `core/prompts.py`, and wire it in `core/pipeline.py`
- **Custom risk factors**: Edit `utils/risk_calculator.py`
- **New UI tabs**: Add to the `st.tabs()` block in `app.py`
