# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Multi-agent US bench trial simulation for juvenile recidivism prediction. Three AI agents (Prosecutor, Defense Attorney, Judge) debate whether a juvenile will be rearrested within 3 years. The Judge renders verdicts that are compared against actual outcomes.

## Key Commands

```bash
# Environment setup
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt

# Code quality
ruff check src/                    # Lint
ruff check --fix src/              # Auto-fix lint issues
black src/                         # Format
mypy src/                          # Type check

# Testing
pytest                             # Run all tests
pytest --cov=src                   # With coverage
pytest -v                          # Verbose

# Ollama model management
ollama pull <model_name>           # Pull individual model
python step0_pull_rename_ollama_models.py  # Pull models from config
```

## Architecture

### Processing Pipeline
```
step0: Model setup → step1: Debate generation → step2: Aggregation → step3-6: Analysis/Visualization
```

### Core Classes (in debate simulator scripts)
- **`CourtDebateManager`**: Orchestrates multi-round debates between agents
- **`CourtAgent`**: Represents Prosecutor, Defense, or Judge with persona, memory, and strategy
- **`DebateResponse`**: Pydantic model for structured LLM responses
- **`TranscriptWriter`**: Manages debate transcript persistence (`.txt` and `.json`)
- **`JudgeOpinion`**: Tracks evolving judge predictions and confidence

### Data Flow
1. Load vignettes from `data/vignettes_final_clean.csv`
2. Select cases (random or first-N) with configurable seed
3. For each model/case combo: conduct multi-round debate
4. Save transcripts to `transcripts_*/` directories
5. Aggregate results to `summary_reports*/`

### LLM Provider Support
- **Ollama** (local): Primary, configured via `config_ollama_models_*.yaml`
- **OpenAI**, **Claude**, **Gemini**: API-based alternatives
- **LiteLLM**: Unified interface for multiple providers

## Configuration

Key constants in main scripts:
```python
TOPN_FEATURES_CT = 10              # Features per vignette
RAND_SELECTION_SEED = 42           # Reproducibility
CASE_CT = 30                       # Cases to process
REPEAT_CT = 5                      # Repetitions per case/model
DEFAULT_TEMPERATURE = 0.7
DEFAULT_MAX_TOKENS = 1024
MAX_API_TIME_SEC = 30.0            # API timeout
```

Model config files:
- `config_ollama_models_size.yaml`: Parameter size ensembles (1B-72B)
- `config_ollama_models_oss.yaml`: Open-source model set
- `config_ollama_models_reasoning.yaml`: Reasoning-focused models

## Feature Importance

Multiple algorithms for feature selection:
- LOFO (Leave One Feature Out)
- Mutual Information
- Permutation Importance
- SHAP
- XGBoost

Feature rankings stored in `data/topn_feature_by_algo.json`.

## Output Structure

- `transcripts_*/`: Debate transcripts per model/ensemble
- `summary_reports*/`: Aggregated metrics and comparisons
- Each debate produces both `.txt` (human-readable) and `.json` (structured) outputs
