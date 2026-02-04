# AgenticSimLaw: Multi-Agent Debate for High-Stakes Tabular Decision-Making

Anonymous repository for TMLR 2026 submission.

## Overview

AgenticSimLaw is a role-structured multi-agent evaluation framework that provides transparent, controllable, and auditable reasoning for high-stakes tabular decision-making. Through a courtroom-style debate protocol with explicit agent roles (Prosecutor, Defense Attorney, Judge), a 7-turn interaction sequence, and complete logging of utterances and belief updates, the framework addresses core challenges in LLM-based multi-agent systems.

## Repository Structure

```
AgenticSimLaw-MAD-courtroom-simulation/
├── README.md                          # This file
├── requirements.txt                   # Python dependencies
├── configs/                           # Model configuration files
│   ├── config_ollama_models_all.yaml  # Complete model list
│   ├── config_ollama_models_oss.yaml  # Open-source models
│   ├── config_ollama_models_size.yaml # Size-stratified ensemble
│   └── config_ollama_models_reasoning.yaml
├── data/
│   ├── sample_vignettes.csv           # Sample case vignettes (30 examples)
│   ├── topn_feature_by_algo.json      # Feature importance rankings
│   └── topn_datasets/                 # Pre-computed feature subsets
│       ├── vignettes_shap_top10.csv
│       ├── vignettes_xgboost_top10.csv
│       └── ...
├── docs/                              # Documentation and prompts
│   └── Documentation - Prompts *.txt
├── src/                               # Source code
│   ├── step0_pull_rename_ollama_models_ver3.py   # Model setup
│   ├── step1_ai-debators-openai_ver25_FREEZE.py  # Main debate simulator
│   ├── step1_ai-debators-openai_ver20-ensembles.py
│   ├── step1_ai-debators-openai_ver20-oss.py
│   ├── step2_aggregate_transcripts_ver5_FREEZE.py
│   ├── step3_statistical_analysis_ver11_FREEZE.py
│   ├── step4_visualize_model_statistics_ver5_FREEZE.py
│   ├── step5_merge_standard-agenticsim_ver3_o1.py
│   ├── step6_visualize_performance_comparison_ver6.py
│   ├── compute_topn_features_ver7.py
│   └── compute_topn_features_utils.py
├── papers/tmlr2026/                   # Paper source files
│   ├── agenticsimlaw.tex
│   ├── agenticsimlaw.bib
│   ├── tmlr.sty
│   └── images/
└── scripts/                           # Utility scripts
```

## Installation

```bash
# Clone repository
git clone https://github.com/anonymous/AgenticSimLaw-MAD-courtroom-simulation.git
cd AgenticSimLaw-MAD-courtroom-simulation

# Create virtual environment
python -m venv .venv
source .venv/bin/activate  # On Windows: .venv\Scripts\activate

# Install dependencies
pip install -r requirements.txt

# Install Ollama (for local LLM inference)
# See: https://ollama.ai/download
```

## Quick Start

### 1. Setup Models

```bash
# Pull and configure Ollama models
python src/step0_pull_rename_ollama_models_ver3.py
```

### 2. Run Debate Simulation

```bash
# Run main debate simulation (uses configs/config_ollama_models_size.yaml)
python src/step1_ai-debators-openai_ver25_FREEZE.py

# Alternative: OSS ensemble
python src/step1_ai-debators-openai_ver20-oss.py
```

### 3. Aggregate Results

```bash
python src/step2_aggregate_transcripts_ver5_FREEZE.py
```

### 4. Statistical Analysis

```bash
python src/step3_statistical_analysis_ver11_FREEZE.py
```

### 5. Visualization

```bash
python src/step4_visualize_model_statistics_ver5_FREEZE.py
python src/step6_visualize_performance_comparison_ver6.py
```

## Configuration

### Key Parameters (in step1 scripts)

| Parameter | Default | Description |
|-----------|---------|-------------|
| `TOPN_FEATURES_CT` | 10 | Features per case vignette |
| `RAND_SELECTION_SEED` | 42 | Random seed for reproducibility |
| `CASE_CT` | 30 | Number of cases to process |
| `REPEAT_CT` | 5 | Repetitions per case/model |
| `DEFAULT_TEMPERATURE` | 0.7 | LLM sampling temperature |
| `DEFAULT_MAX_TOKENS` | 1024 | Max tokens per response |

### Model Configuration

Edit YAML files in `configs/` to customize model ensembles:

```yaml
models:
  - name: "llama3.1:8b-instruct-q4_K_M"
    renamed: "llama3_1_8b_instruct_q4_k_m"
  - name: "qwen2.5:7b-instruct-q4_K_M"
    renamed: "qwen2_5_7b_instruct_q4_k_m"
```

## 7-Turn Debate Protocol

1. **Turn 1**: Prosecutor presents opening arguments
2. **Turn 2**: Defense presents rebuttal
3. **Turn 3**: Prosecutor cross-examination
4. **Turn 4**: Defense responds to cross-examination
5. **Turn 5**: Prosecutor closing arguments
6. **Turn 6**: Defense closing arguments
7. **Turn 7**: Judge deliberation and verdict

## Data

The sample vignettes in `data/sample_vignettes.csv` are derived from the NLSY97 dataset and contain anonymized case features. Full dataset access requires NLSY97 data use agreement.

### Feature Categories

- **Demographics**: age, sex, race
- **Social**: married status, household size, parent relations
- **Economic**: employment, food stamps, homelessness
- **Criminal History**: prior arrests, convictions, family incarceration
- **Behavioral**: substance use, religious practice, victimization

## Output Structure

Transcript outputs are saved to `transcripts_ensemble_*/model_name/`:

```
transcripts_ensemble_final_YYYYMMDD/
├── llama3_1_8b_instruct_q4_k_m/
│   ├── transcript_row-0_ver-0.json    # Structured JSON
│   ├── transcript_row-0_ver-0.txt     # Human-readable
│   └── ...
├── qwen2_5_7b_instruct_q4_k_m/
│   └── ...
```

## Metrics

The framework computes:
- **Accuracy**: Overall prediction accuracy
- **F1 Score**: Harmonic mean of precision and recall
- **Precision/Recall**: Per-class metrics
- **Confusion Matrix**: TP, TN, FP, FN counts

## Reproducibility

To reproduce paper results:

1. Use random seed `42` (default)
2. Use model configurations from `configs/`
3. Process 150 cases for StandardLLM, 100 cases for AgenticSimLaw
4. Run 5 repetitions per case/model combination

## Citation

```bibtex
@article{anonymous2026agenticsimlaw,
  title={AgenticSimLaw: Multi-Agent Debate for High-Stakes Tabular Decision-Making},
  author={Anonymous},
  journal={Transactions on Machine Learning Research},
  year={2026}
}
```

## License

This code is released under the MIT License for research purposes only.

## Responsible Use Statement

This framework is intended for research, benchmarking, and transparency analysis only. It should NOT be deployed for operational sentencing, parole, probation, or any consequential criminal justice decisions. See paper Section 5 for detailed discussion.
