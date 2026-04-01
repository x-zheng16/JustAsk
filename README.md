<div align="center">

# JustAsk

### Curious Code Agents Reveal System Prompts in Frontier LLMs

[![arXiv](https://img.shields.io/badge/arXiv-2601.21233-b31b1b.svg)](https://arxiv.org/abs/2601.21233)
[![Gallery](https://img.shields.io/badge/Gallery-System_Prompt_Open-22D3BB.svg)](https://x-zheng16.github.io/System-Prompt-Open/)
[![GitHub](https://img.shields.io/github/stars/x-zheng16/JustAsk?style=social)](https://github.com/x-zheng16/JustAsk)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Python 3.11+](https://img.shields.io/badge/Python-3.11+-blue.svg)](https://www.python.org/downloads/)
[![Maintained](https://img.shields.io/badge/Maintained-yes-green.svg)](https://github.com/x-zheng16/JustAsk/commits/)

**We asked. They answered.**

Tested on **45** black-box commercial models | **85-95%** verified consistency | **Zero** supervision

[Paper](https://arxiv.org/abs/2601.21233) | [Gallery](https://x-zheng16.github.io/System-Prompt-Open/) | [Gallery Data](https://github.com/x-zheng16/System-Prompt-Open)

</div>

---

<div align="center">

<a href="https://x-zheng16.github.io/">Xiang Zheng</a><sup>1</sup>,
<a href="https://github.com/wuyoscar">Yutao Wu</a><sup>2</sup>,
<a href="https://hanxunh.github.io/">Hanxun Huang</a><sup>3</sup>,
<a href="https://github.com/bboylyg">Yige Li</a><sup>4</sup>,
<a href="https://xingjunma.com/">Xingjun Ma</a><sup>5,&#8224;</sup>,
<a href="https://aisecure.github.io/">Bo Li</a><sup>6</sup>,
<a href="https://scholar.google.com/citations?user=f3_FP8AAAAAJ">Yu-Gang Jiang</a><sup>5</sup>,
<a href="https://www.cs.cityu.edu.hk/~congwang/">Cong Wang</a><sup>1,&#8224;</sup>

<sup>1</sup>City University of Hong Kong, <sup>2</sup>Deakin University, <sup>3</sup>The University of Melbourne, <sup>4</sup>Singapore Management University, <sup>5</sup>Fudan University, <sup>6</sup>University of Illinois at Urbana-Champaign

<sup>&#8224;</sup>Corresponding authors

</div>

---

## News

- **[2026-04]** Code and data open-sourced on [GitHub](https://github.com/x-zheng16/JustAsk).
- **[2026-04]** [System Prompt Open Gallery](https://x-zheng16.github.io/System-Prompt-Open/) launched -- browse extracted system prompts from 45+ frontier models.
- **[2026-01]** Paper posted on [arXiv](https://arxiv.org/abs/2601.21233).

## Table of Contents

- [News](#news)
- [Overview](#overview)
- [Results](#results)
- [Abstract](#abstract)
- [Method](#method)
- [Structure](#structure)
- [Setup](#setup)
- [Controlled Evaluation](#controlled-evaluation)
- [Related Projects](#related-projects)
- [Citation](#citation)
- [Star History](#star-history)

## Overview

<div align="center">
<img src="assets/fig_framework.png" alt="JustAsk Framework" width="100%">
<p><em>JustAsk framework: a self-evolving agent that autonomously discovers extraction strategies through UCB-guided skill selection.</em></p>
</div>

JustAsk is a self-evolving framework that autonomously discovers effective system prompt extraction strategies through interaction alone.
Unlike prior prompt-engineering or dataset-based attacks, JustAsk requires no handcrafted prompts, labeled supervision, or privileged access beyond standard user interaction.

**Key Insight:** Autonomous code agents fundamentally expand the LLM attack surface.
JustAsk treats each model interaction as a learning opportunity -- the agent evolves its skill set organically through experience, not model fine-tuning.

## Results

<div align="center">
<img src="assets/fig_validation.png" alt="Extraction Results" width="100%">
<p><em>Validation: JustAsk's semantic extraction (left) closely matches the ground truth obtained via reverse engineering (right), confirming high extraction fidelity.</em></p>
</div>

Browse the full extraction results at the **[System Prompt Open Gallery](https://x-zheng16.github.io/System-Prompt-Open/)**.

## Abstract

Autonomous code agents built on large language models are reshaping software and AI development through tool use, long-horizon reasoning, and self-directed interaction.
However, this autonomy introduces a previously unrecognized security risk: agentic interaction fundamentally expands the LLM attack surface, enabling systematic probing and recovery of hidden system prompts that guide model behavior.
We identify system prompt extraction as an emergent vulnerability intrinsic to code agents and present **JustAsk**, a self-evolving framework that autonomously discovers effective extraction strategies through interaction alone.
Unlike prior prompt-engineering or dataset-based attacks, JustAsk requires no handcrafted prompts, labeled supervision, or privileged access beyond standard user interaction.
It formulates extraction as an online exploration problem, using Upper Confidence Bound-based strategy selection and a hierarchical skill space spanning atomic probes and high-level orchestration.
These skills exploit imperfect system-instruction generalization and inherent tensions between helpfulness and safety.
Evaluated on **45** black-box commercial models across multiple providers, JustAsk consistently achieves full or near-complete system prompt recovery, revealing recurring design- and architecture-level vulnerabilities.
Our results expose system prompts as a critical yet largely unprotected attack surface in modern agent systems.

## Method

**Skill Set Definition:**

```
Skill Set = Skills (fixed) + Rules (evolving) + Stats (evolving)
```

| Component  | Role                                      | Evolves? |
| ---------- | ----------------------------------------- | -------- |
| **Skills** | Fixed vocabulary (L1-L14, H1-H15)         | No       |
| **Rules**  | Exploitation knowledge (long-term memory) | Yes      |
| **Stats**  | Exploration guidance (UCB)                | Yes      |

**Skill Selection (UCB):**

```
UCB(Ci) = success_rate(Ci) + c * sqrt(ln(N) / ni)
           ───────────────   ─────────────────────
            exploitation      exploration (curiosity)
```

## Structure

```
.
├── config/
│   └── exp_config.yaml                # Experiment configuration
├── docs/
│   ├── PAP.md                         # Persuasion templates & skill mappings
│   └── PAP_taxonomy.jsonl             # 40 real-world persuasion patterns
└── src/
    ├── skill_evolving.py              # Main extraction via OpenRouter
    ├── skill_testing.py               # Controlled evaluation
    ├── skill_testing_controlled.py    # Protection-level evaluation
    ├── ucb_ranking.py                 # UCB skill selection algorithm
    ├── knowledge.py                   # Knowledge persistence
    ├── validation.py                  # Cross-verify & self-consistency
    └── ...
```

## Setup

```bash
conda create -n justask python=3.11
conda activate justask
pip install python-dotenv requests numpy
```

Create a `.env` file:

```
OPENROUTER_API_KEY=sk-or-v1-your-key-here
```

## Controlled Evaluation

```bash
python src/skill_testing.py --model openai/gpt-5.2
```

| Metric          | Description                        |
| --------------- | ---------------------------------- |
| **Semantic Sim** | Embedding cosine similarity        |
| **Secret Leak**  | Fraction of injected secrets found |

See `START.md` for detailed agent instructions.

## Related Projects

From the same team:

- **[System Prompt Open](https://github.com/x-zheng16/System-Prompt-Open)** -- Gallery of extracted system prompts from 45+ frontier models.
- **[Awesome Embodied AI Safety](https://github.com/xiangli-chen/Awesome-Embodied-AI-Safety)** -- Survey on safety challenges in embodied AI agents.

## Citation

**BibTeX:**

```bibtex
@article{zheng2026justask,
  title={Just Ask: Curious Code Agents Reveal System
         Prompts in Frontier LLMs},
  author={Zheng, Xiang and Wu, Yutao and Huang, Hanxun
          and Li, Yige and Ma, Xingjun and Li, Bo
          and Jiang, Yu-Gang and Wang, Cong},
  journal={arXiv preprint arXiv:2601.21233},
  year={2026}
}
```

**Plain text:**

> Xiang Zheng, Yutao Wu, Hanxun Huang, Yige Li, Xingjun Ma, Bo Li, Yu-Gang Jiang, and Cong Wang. "Just Ask: Curious Code Agents Reveal System Prompts in Frontier LLMs." arXiv preprint arXiv:2601.21233, 2026.

## Star History

<div align="center">

<a href="https://star-history.com/#x-zheng16/JustAsk&Date">
  <picture>
    <source media="(prefers-color-scheme: dark)" srcset="https://api.star-history.com/svg?repos=x-zheng16/JustAsk&type=Date&theme=dark" />
    <source media="(prefers-color-scheme: light)" srcset="https://api.star-history.com/svg?repos=x-zheng16/JustAsk&type=Date" />
    <img alt="Star History Chart" src="https://api.star-history.com/svg?repos=x-zheng16/JustAsk&type=Date" width="800" />
  </picture>
</a>

</div>

## License

MIT
