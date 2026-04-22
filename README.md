### Summary

The core concept of BugHound is understanding how an agentic workflow combines analysis, action, and evaluation to process code in a structured way rather than relying on a single model output. 

Students will most likely struggle with distinguishing between heuristic rules and AI-generated behavior, especially when interpreting why the system sometimes ignores or falls back from model responses. 

AI is helpful in identifying patterns and suggesting broader or more nuanced issues that heuristics miss, but it can also be misleading when it produces incomplete, overconfident, or improperly formatted outputs that the agent cannot reliably parse. This makes it important to treat AI output as fallible rather than authoritative. 

One effective way I would guide students is to have them trace a single input through each stage of the pipeline and identify exactly where decisions are made, rather than focusing only on the final fix.

# Proposing Fixes and Evaluating Risk

To improve reliability, I modified the auto-fix condition to require both a low risk level and a small number of issues. Specifically, I changed the condition to only allow auto-fix when there is at most one issue. This makes the agent more cautious by preventing automatic application of fixes that involve multiple changes.

# GeminiAPI Setup

I successfully integrated the Gemini API into BugHound by configuring the .env file with my API key and running the app in Gemini mode. This allowed the agent to switch from heuristic-based analysis to AI-powered analysis.

# Agent's Behavior

One place where the agent’s behavior feels unreliable is when the heuristic analyzer only checks for a few simple patterns and does not truly understand the logic of the code. This means it can miss important bugs while still reporting that no issues were found. As a result, the rest of the workflow, especially the proposed fix and risk assessment depends on incomplete or shallow analysis, which can lead to misleading results.

# 🐶 BugHound

BugHound is a small, agent-style debugging tool. It analyzes a Python code snippet, proposes a fix, and runs basic reliability checks before deciding whether the fix is safe to apply automatically.

---

## What BugHound Does

Given a short Python snippet, BugHound:

1. **Analyzes** the code for potential issues  
   - Uses heuristics in offline mode  
   - Uses Gemini when API access is enabled  

2. **Proposes a fix**  
   - Either heuristic-based or LLM-generated  
   - Attempts minimal, behavior-preserving changes  

3. **Assesses risk**  
   - Scores the fix  
   - Flags high-risk changes  
   - Decides whether the fix should be auto-applied or reviewed by a human  

4. **Shows its work**  
   - Displays detected issues  
   - Shows a diff between original and fixed code  
   - Logs each agent step

---

## Setup

### 1. Create a virtual environment (recommended)

```bash
python -m venv .venv
source .venv/bin/activate   # macOS/Linux
# or
.venv\Scripts\activate      # Windows
```

### 2. Install dependencies

```bash
pip install -r requirements.txt
```

---

## Running in Offline (Heuristic) Mode

No API key required.

```bash
streamlit run bughound_app.py
```

In the sidebar, select:

* **Model mode:** Heuristic only (no API)

This mode uses simple pattern-based rules and is useful for testing the workflow without network access.

---

## Running with Gemini

### 1. Set up your API key

Copy the example file:

```bash
cp .env.example .env
```

Edit `.env` and add your Gemini API key:

```text
GEMINI_API_KEY=your_real_key_here
```

### 2. Run the app

```bash
streamlit run bughound_app.py
```

In the sidebar, select:

* **Model mode:** Gemini (requires API key)
* Choose a Gemini model and temperature

BugHound will now use Gemini for analysis and fix generation, while still applying local reliability checks.

---

## Running Tests

Tests focus on **reliability logic** and **agent behavior**, not the UI.

```bash
pytest
```

You should see tests covering:

* Risk scoring and guardrails
* Heuristic fallbacks when LLM output is invalid
* End-to-end agent workflow shape
