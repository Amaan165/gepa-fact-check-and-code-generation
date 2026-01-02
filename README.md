# GEPA for LLM Reliability: Improving Fact-Checking and Code Generation

[![Blog](https://img.shields.io/badge/Blog-Read%20More-blue)](https://bit.ly/4rRPdcf)

> **Improving Reliability of Large Language Models using GEPA for Fact Checking and Code Generation**


---

## 📋 Table of Contents

- [Overview](#overview)
- [Key Results](#key-results)
- [Background](#background)
- [Methodology](#methodology)
- [Datasets](#datasets)
- [Experimental Setup](#experimental-setup)
- [Results](#results)
- [Novel Contributions](#novel-contributions)
- [Installation](#installation)
- [Usage](#usage)
- [Project Structure](#project-structure)
- [Limitations](#limitations)
- [Future Work](#future-work)
- [References](#references)
- [Team](#team)
- [Acknowledgments](#acknowledgments)

---

## 🎯 Overview

Large Language Models (LLMs) demonstrate remarkable capabilities across diverse tasks, yet they frequently produce incorrect facts or buggy code with high confidence. This project explores **GEPA (Genetic Evolutionary Prompt Algorithm)** — a novel approach to enhancing LLM reliability without heavy fine-tuning or extensive human feedback.

Instead of modifying model parameters, GEPA enables LLMs to **iteratively reflect on their own outputs** and **refine their prompts** to achieve better results. We apply this methodology to two challenging domains:

1. **Fact Checking**: Verifying claims against evidence (HoVer dataset)
2. **Code Generation**: Writing correct code that passes test cases (HumanEval benchmark)

### 🎯 Core Innovation

GEPA transforms reliability improvement into a **conversation the model has with itself**:
- ✅ No parameter updates required
- ✅ No extensive training data needed
- ✅ Achieves significant gains in just **5 iterations**
- ✅ Human-readable, interpretable prompt evolution

---

## 🏆 Key Results

### Code Generation (HumanEval)

| Model | Metric | Seed Prompt | GEPA-Optimized | Improvement |
|-------|--------|-------------|----------------|-------------|
| **GPT-3.5 Turbo** | Pass@1 | 79.69% | **100.0%** | **+20.31pp** |
| **GPT-3.5 Turbo** | Pass@3 | 84.38% | **100.0%** | **+15.62pp** |
| **GPT-3.5 Turbo** | Pass@5 | 85.94% | **100.0%** | **+14.06pp** |
| **Qwen 3-8B** | Pass@1 | 42.19% | **68.75%** | **+26.56pp** |
| **Qwen 3-8B** | Pass@3 | 57.81% | **87.50%** | **+29.69pp** |
| **Qwen 3-8B** | Pass@5 | 59.38% | **92.19%** | **+32.81pp** |
| **GPT-4.1 mini** | Pass@1/3/5 | 100.0% | 100.0% | Already saturated |

### Fact Checking (HoVer)

| Model | Run Type | Baseline | GEPA-Optimized | Improvement |
|-------|----------|----------|----------------|-------------|
| **GPT-4.1 mini** | Zero-shot | 62.8% | **65.4%** | **+2.6pp** |
| **GPT-4.1 mini** | FS-style (3-shot) | 65.4% | **69.2%** | **+3.8pp** |
| **GPT-3.5 Turbo** | Zero-shot | 57.0% | **61.6%** | **+4.6pp** |
| **Qwen 3-8B** | Zero-shot | 55.2% | **56.2%** | **+1.0pp** |

---

## 📚 Background

### The Reliability Challenge

LLMs often lack a guaranteed grasp of truth or correctness:
- **Factual hallucinations**: Stating wrong dates, facts, or references with confidence
- **Code bugs**: Generating plausible-looking code that fails on edge cases
- **Overconfidence**: No calibrated uncertainty about their outputs

### Traditional Approaches vs. GEPA

**Traditional Methods:**
- ❌ Fine-tuning: Requires large datasets, thousands of iterations, expensive
- ❌ RLHF: Needs extensive human feedback collection
- ❌ Parameter updates: Creates "black box" improvements

**GEPA Approach:**
- ✅ Keeps base model fixed
- ✅ Uses automatic metrics (accuracy, test pass rates)
- ✅ Leverages reflective LLM analysis
- ✅ Produces human-readable prompt improvements
- ✅ Achieves gains with ~dozens of trials instead of thousands

### What is GEPA?

**GEPA (Genetic Evolutionary Prompt Algorithm)** allows an LLM to iteratively:
1. **Attempt** a task with current prompt
2. **Reflect** on failures using a larger "reflector" model
3. **Mutate** prompts based on natural language insights
4. **Select** best prompt variants (Pareto optimization)
5. **Iterate** until performance targets or budget exhausted

---

## 🔬 Methodology

### GEPA Workflow

```
┌─────────────────────────────────────────────────────────────┐
│  1. INITIALIZATION                                          │
│     • Start with seed prompt(s)                             │
│     • Evaluate on validation set                            │
└─────────────────────────────────────────────────────────────┘
                           ↓
┌─────────────────────────────────────────────────────────────┐
│  2. TASK EXECUTION                                          │
│     • Task LLM attempts problem with current prompt         │
│     • Collect: output, chain-of-thought, tools used         │
└─────────────────────────────────────────────────────────────┘
                           ↓
┌─────────────────────────────────────────────────────────────┐
│  3. EVALUATION                                              │
│     • HoVer: Check if label correct + evidence valid        │
│     • HumanEval: Run code against unit tests                │
└─────────────────────────────────────────────────────────────┘
                           ↓
                    ┌──────────────┐
                    │  Success?    │
                    └──────────────┘
                      ↙           ↘
                   YES             NO
                    ↓               ↓
              Continue      ┌──────────────────────┐
                            │  4. REFLECTION       │
                            │  • Reflector LLM     │
                            │    analyzes failure  │
                            │  • Explains why      │
                            │  • Suggests fixes    │
                            └──────────────────────┘
                                     ↓
                            ┌──────────────────────┐
                            │  5. MUTATION         │
                            │  • Update prompt     │
                            │  • Add instructions  │
                            │  • Crossover variants│
                            └──────────────────────┘
                                     ↓
                            ┌──────────────────────┐
                            │  6. SELECTION        │
                            │  • Pareto filtering  │
                            │  • Keep diverse best │
                            └──────────────────────┘
                                     ↓
                              Repeat (5 iterations)
```

### Model Configuration

**Task LLMs** (models performing fact-checking/coding):
- OpenAI GPT-4.1 mini
- OpenAI GPT-3.5 Turbo
- Qwen 3-8B

**Reflective LLM** (analyzes failures and suggests improvements):
- GPT-5 (separate, larger model)

---

## 📊 Datasets

### HoVer: Fact Checking

**Task**: Many-hop fact extraction and claim verification

**Dataset Statistics**:
- Training set: 100 claims
- Validation set: 30 claims
- Test set: 500 claims

**Example**:
```
Claim: "The composer of Hamilton was inspired by a 2004 biography."
Task: SUPPORTED or NOT_SUPPORTED + evidence extraction
```

**Challenges**:
- Multi-hop reasoning (need multiple Wikipedia documents)
- Complex claims with multiple parts to verify
- Evidence must support/refute each component

### HumanEval: Code Generation

**Task**: Generate Python functions from natural language descriptions

**Dataset Statistics**:
- Training set: 50 problems
- Validation set: 50 problems  
- Test set: 64 problems

**Example**:
```python
def is_prime(n):
    """
    Return True if n is prime, otherwise False
    """
    # Model must generate implementation
```

**Evaluation Metric**: **Pass@k**
- Pass@1: % of problems where at least 1 of 1 samples passes all tests
- Pass@3: % of problems where at least 1 of 3 samples passes all tests
- Pass@5: % of problems where at least 1 of 5 samples passes all tests

---

## 🧪 Experimental Setup

### Configuration

```yaml
GEPA Parameters:
  max_iterations: 5
  max_metric_calls: 200
  reflection_batch_size: 5
  
HoVer Setup:
  train_claims: 100
  val_claims: 30
  test_claims: 500
  
HumanEval Setup:
  train_problems: 50
  val_problems: 50
  test_problems: 64
```

### Baseline Comparisons

We evaluate **6 configurations** for HoVer:

**Zero-shot run:**
1. Baseline (Seed, Zero-shot)
2. GEPA (Zero-shot Training, Zero-shot Inference)
3. Few-shot-Optimized GEPA (3-shot Training, Zero-shot Inference)

**FS-style (3-shot) run:**
4. Baseline (Seed, 3-shot Inference)
5. GEPA (Zero-shot Training, 3-shot Inference)
6. Few-shot-Optimized GEPA (3-shot Training, 3-shot Inference)

For HumanEval:
1. Seed Prompt Baseline
2. GEPA-Optimized Prompt

---

## 📈 Results

### HumanEval: Code Generation Breakdown

#### GPT-3.5 Turbo
- **Baseline**: Competent but imperfect (79.69% Pass@1)
- **After GEPA**: Perfect performance (100% Pass@1)
- **Key improvements**: Better edge case handling, defensive programming

#### Qwen 3-8B
- **Baseline**: Weak performance (42.19% Pass@1)
- **After GEPA**: Strong improvement (68.75% Pass@1, 92.19% Pass@5)
- **Interpretation**: Mix of prompt optimization + capacity limits

#### GPT-4.1 mini
- **Baseline**: Already perfect (100% Pass@1/3/5)
- **After GEPA**: No improvement possible (saturated)
- **Insight**: GEPA needs headroom to be effective

### HoVer: Fact Checking Breakdown

#### Best Results (GPT-4.1 mini, FS-style run)
- Baseline (3-shot): **65.4%**
- GEPA + 3-shot: **69.2%** (+3.8pp)

#### Key Pattern
- GEPA provides consistent modest gains (1-4pp)
- Combining GEPA with few-shot prompting yields best results
- Improvements more subtle than HumanEval (different task characteristics)

### Prompt Evolution Patterns

**HoVer Optimizations Discovered:**
- "Make sure to gather all necessary evidence and double-check each part of the claim"
- "Consider both supported and refuted evidence for each clause"
- "List all relevant evidence before deciding on verdict"

**HumanEval Optimizations Discovered:**
- "Remember to consider edge cases and handle errors or unusual inputs"
- "Plan your approach before coding: outline algorithm, identify tricky cases"
- "Verify correctness on empty inputs, boundary values, and type constraints"

---

## 🌟 Novel Contributions

### 1. **First Application of GEPA to General-Purpose Code Generation**

Prior GEPA work focused on specialized domains (hardware kernels, systems code). We demonstrate:
- ✅ GEPA works on mainstream Python programming (HumanEval)
- ✅ Unit test outcomes provide effective reflection feedback
- ✅ Single optimized system prompt reused across all 164 problems

**Impact**: 
- GPT-3.5 Turbo: 79.69% → 100% Pass@1
- Qwen 3-8B: 42.19% → 68.75% Pass@1

### 2. **Cross-Domain Evaluation Framework**

- ✅ Same GEPA framework applied to both fact verification and code generation
- ✅ Minimal task-specific modifications required
- ✅ Demonstrates GEPA as versatile strategy, not task-specific trick

### 3. **Novel Few-Shot-Aware GEPA Variant**

**Innovation**: Optimizer sees 3-shot demonstrations during training

**Key Findings**:
- Training-inference alignment matters
- Best results: GEPA (zero-shot trained) + 3-shot inference
- Mismatch can hurt: 3-shot training → zero-shot testing degrades performance

**Academic Contribution**: First systematic analysis of few-shot conditioning in prompt optimization

### 4. **Comprehensive Multi-Model Analysis**

**Insight**: Model headroom determines GEPA effectiveness

| Model Regime | Baseline | GEPA Gain | Interpretation |
|--------------|----------|-----------|----------------|
| **Saturated** (GPT-4.1 mini HumanEval) | 100% | 0% | No learning signal |
| **Mid-range** (GPT-3.5 Turbo) | 80% | +20pp | Latent ability unlocked |
| **Low-range** (Qwen 3-8B) | 42% | +27pp | Mix of prompt + capacity |

### 5. **Structured Analysis of Prompt Evolution**

**Data-Driven Prompt Engineering**:
- Document recurring patterns in successful prompts
- Provide catalog of "best practices" discovered by reflection
- Enable transfer of insights to related tasks

---

## 🚀 Installation

### Prerequisites

```bash
# Python 3.8+
python --version

# Required packages
pip install --upgrade pip
```

### Setup

```bash
# Clone the repository
git clone https://github.com/YOUR_USERNAME/gepa-llm-reliability.git
cd gepa-llm-reliability

# Create virtual environment
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate

# Install dependencies
pip install -r requirements.txt

# Set up API keys
cp .env.example .env
# Edit .env with your OpenAI API key
```

### Requirements

```txt
# requirements.txt
openai>=1.0.0
anthropic>=0.8.0
numpy>=1.24.0
pandas>=2.0.0
matplotlib>=3.7.0
seaborn>=0.12.0
tqdm>=4.65.0
python-dotenv>=1.0.0
datasets>=2.14.0
transformers>=4.30.0
torch>=2.0.0
```

---

## 💻 Usage

### Quick Start

```python
from gepa import GEPAOptimizer
from tasks import HumanEvalTask, HoVerTask

# Initialize GEPA for code generation
optimizer = GEPAOptimizer(
    task_model="gpt-3.5-turbo",
    reflective_model="gpt-4",
    max_iterations=5,
    max_metric_calls=200
)

# Load HumanEval task
task = HumanEvalTask(
    train_size=50,
    val_size=50,
    test_size=64
)

# Run optimization
best_prompt, results = optimizer.optimize(task)

# Evaluate on test set
test_results = task.evaluate(best_prompt)
print(f"Pass@1: {test_results['pass@1']:.2f}%")
print(f"Pass@3: {test_results['pass@3']:.2f}%")
print(f"Pass@5: {test_results['pass@5']:.2f}%")
```

### Running Experiments

#### HumanEval Code Generation

```bash
# Run GEPA optimization on HumanEval
python experiments/run_humaneval.py \
    --model gpt-3.5-turbo \
    --reflector gpt-4 \
    --iterations 5 \
    --output results/humaneval_gpt35

# Evaluate specific prompt
python experiments/evaluate_humaneval.py \
    --prompt_file results/humaneval_gpt35/best_prompt.txt \
    --model gpt-3.5-turbo
```

#### HoVer Fact Checking

```bash
# Run GEPA optimization on HoVer
python experiments/run_hover.py \
    --model gpt-4-mini \
    --reflector gpt-4 \
    --run_type zero-shot \
    --iterations 5 \
    --output results/hover_gpt4mini

# Run few-shot variant
python experiments/run_hover.py \
    --model gpt-4-mini \
    --reflector gpt-4 \
    --run_type few-shot \
    --num_shots 3 \
    --output results/hover_gpt4mini_fewshot
```

### Analyzing Results

```python
# Load and visualize results
from analysis import ResultsAnalyzer

analyzer = ResultsAnalyzer("results/humaneval_gpt35")

# Plot iteration progress
analyzer.plot_progress()

# Show prompt evolution
analyzer.show_prompt_evolution()

# Compare against baseline
analyzer.compare_to_baseline(baseline_file="baselines/humaneval_seed.json")
```

---

## 📁 Project Structure

```
gepa-llm-reliability/
│
├── README.md
├── requirements.txt
├── .env.example
├── setup.py
│
├── gepa/                          # Core GEPA implementation
│   ├── __init__.py
│   ├── optimizer.py               # Main GEPA loop
│   ├── reflector.py               # Reflection module
│   ├── mutation.py                # Prompt mutation strategies
│   ├── selection.py               # Pareto selection logic
│   └── utils.py
│
├── tasks/                         # Task-specific implementations
│   ├── __init__.py
│   ├── base_task.py              # Abstract task interface
│   ├── humaneval_task.py         # HumanEval code generation
│   ├── hover_task.py             # HoVer fact checking
│   └── evaluation/
│       ├── code_executor.py      # Safe code execution sandbox
│       └── fact_checker.py       # Evidence-based verification
│
├── experiments/                   # Experiment scripts
│   ├── run_humaneval.py
│   ├── run_hover.py
│   ├── evaluate_humaneval.py
│   ├── evaluate_hover.py
│   └── configs/
│       ├── humaneval_config.yaml
│       └── hover_config.yaml
│
├── analysis/                      # Results analysis
│   ├── __init__.py
│   ├── results_analyzer.py
│   ├── visualization.py
│   └── statistical_tests.py
│
├── data/                         # Dataset storage
│   ├── humaneval/
│   │   ├── train.jsonl
│   │   ├── val.jsonl
│   │   └── test.jsonl
│   └── hover/
│       ├── train.jsonl
│       ├── val.jsonl
│       └── test.jsonl
│
├── results/                      # Experiment outputs
│   ├── humaneval/
│   └── hover/
│
├── notebooks/                    # Jupyter notebooks
│   ├── 01_data_exploration.ipynb
│   ├── 02_baseline_evaluation.ipynb
│   ├── 03_gepa_analysis.ipynb
│   └── 04_results_visualization.ipynb
│
└── docs/                        # Documentation
    ├── methodology.md
    ├── api_reference.md
    └── experiment_guide.md
```

---

## ⚠️ Limitations

### 1. **Knowledge Boundaries**
- GEPA improves *how* models use knowledge, not *what* they know
- Cannot fix fundamental knowledge gaps or capacity limits
- Reflector quality directly impacts optimization quality

### 2. **Reflection Quality Variance**
- Some reflections misdiagnose errors (e.g., focus on syntax vs. logic)
- No built-in filtering mechanism for low-quality reflections
- Later iterations often self-correct, but adds noise

### 3. **Prompt Length Growth**
- Prompts tend to become longer through iterations
- Can approach context limits in extended runs
- May benefit from automatic simplification/distillation

### 4. **Saturation Effects**
- GEPA ineffective when baseline already near-perfect
- Requires measurable performance gap to optimize
- GPT-4.1 mini on HumanEval: no improvement possible

### 5. **Task-Specific Tuning**
- Reflection prompts need light customization per task
- Evaluation metrics must be automatable
- Not plug-and-play for all domains

---

## 🔮 Future Work

### Short-term Extensions

1. **Additional Benchmarks**
   - MMLU (multitask language understanding)
   - GSM8K (math word problems)
   - DROP (reading comprehension with discrete reasoning)

2. **Reflection Quality Control**
   - Add judge model to score reflection usefulness
   - Filter or weight reflections before mutation
   - Meta-learning to improve reflector over time

3. **Prompt Distillation**
   - Automatically simplify evolved prompts
   - Extract core principles, remove redundancy
   - Balance length vs. effectiveness

### Long-term Directions

1. **Continuous GEPA**
   - Periodically re-run on fresh data
   - Keep prompts aligned with evolving use cases
   - Adapt to distribution shift

2. **Human-in-the-Loop**
   - Incorporate lightweight human feedback
   - Supervise reflections on especially hard tasks
   - Combine automated exploration with domain expertise

3. **Multi-Task GEPA**
   - Optimize prompts across task families simultaneously
   - Discover general-purpose instruction improvements
   - Transfer learning for prompt evolution

4. **GEPA for Alignment**
   - Apply to safety and alignment objectives
   - Optimize for helpfulness, harmlessness, honesty
   - Complement RLHF with interpretable prompt evolution

---

## 📚 References

1. **Agrawal et al.** (2025). "GEPA: Reflective Prompt Evolution Can Outperform Reinforcement Learning." *arXiv:2507.19457*.

2. **Chen et al.** (2021). "Evaluating Large Language Models Trained on Code." *arXiv:2107.03374*.

3. **Jiang et al.** (2020). "HoVer: A Dataset for Many-Hop Fact Extraction and Claim Verification." *Findings of EMNLP*.

4. **Austin et al.** (2021). "Program Synthesis with Large Language Models." *NeurIPS Datasets & Benchmarks*.

---

## 👥 Team

**Research Team** (NYU Center for Data Science):
- **Amaan Mansuri** 
- **Ninad Chaudhari**
- **Shravan Khunti**
- **Harshit Bhargava**
- **Vishwa Raval**

**Course**: DS-GA 1011 - Fundamentals of Natural Language Processing  
**Institution**: New York University, Center for Data Science  
**Semester**: Fall 2025

---



## 🔗 Links

- 📝 [Blog Post](https://bit.ly/4rRPdcf) - Detailed write-up with examples
- 📄 [Full Report (PDF)](./docs/NLP_Blog_Report.pdf) - Complete academic report
- 🎯 [Course Page](https://nyu-ds-ga-1011.github.io/) - NLP course information

---

## ⭐ Citation

If you use this work in your research, please cite:

```bibtex
@article{mansuri2025gepa,
  title={Improving Reliability of LLMs using GEPA for Fact Checking and Code Generation},
  author={Mansuri, Amaan and Chaudhari, Ninad and Khunti, Shravan and Bhargava, Harshit and Raval, Vishwa},
  journal={NYU DS-GA 1011 Course Project},
  year={2025}
}
```
