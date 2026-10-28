# GSM-Symbolic: a toy-scale reproduction

Paper-reading assignment for **FLLM**: Shah Aryan (24NA10066), Group 3.

**Paper:** Mirzadeh, Alizadeh, Shahrokhi, Tuzel, Bengio, Farajtabar. *GSM-Symbolic: Understanding the Limitations of Mathematical Reasoning in Large Language Models.* **ICLR 2025.** [arXiv 2410.05229](https://arxiv.org/abs/2410.05229) · [official templates](https://github.com/apple/ml-gsm-symbolic)

**Video walkthrough (12 min):** https://youtu.be/Etk5_uYIx1k

## What is here

| File | Contents |
|---|---|
| [`gsm_symbolic_full_run.ipynb`](gsm_symbolic_full_run.ipynb) | **Main notebook**, executed with `QUICK = False` (60 templates × 25 datasets). Includes all outputs, plots and the takeaways in §10 |
| [`gsm_symbolic_quick_run.ipynb`](gsm_symbolic_quick_run.ipynb) | The same notebook executed with `QUICK = True` (20 × 8), as a smaller replicate |
| [`slides/GSM-Symbolic_video.pptx`](slides/GSM-Symbolic_video.pptx) | Slides used in the video. The speaker notes contain the narration |

## What the notebook implements

Apple released the GSM-Symbolic **templates**, but not the code that turns them into questions. The notebook builds that generator and the experiments around it:

1. **Template parser and rejection sampler** for Apple's annotation language (`{var, default}` slots, `#init`, `#conditions`, `#answer`). It is validated on all 250 released templates; 90/100 Symbolic templates sample correctly, and 79 reproduce the original GSM8K answer exactly.
2. **Perturbation modes**: `original`, `names`, `numbers`, `both`, plus the P1/P2 difficulty splits.
3. **GSM-NoOp**: hand-written irrelevant clauses for 14 templates. The NoOp templates were not released.
4. **Evaluation**: 8-shot chain-of-thought with greedy decoding on Qwen2.5-0.5B-Instruct and Qwen2.5-1.5B-Instruct.
5. **The paper's four experiments** (E1–E4), plus **statistical checks**: binomial noise baseline, cluster-bootstrap CIs, and a number-magnitude confound.

## Results (full run)

| | Qwen2.5-0.5B | Qwen2.5-1.5B |
|---|---|---|
| E1: original GSM8K → symbolic | 40.0 → 34.6 | 66.7 → 65.1 |
| E2: names only / numbers only | 41.4 / 35.6 | 70.5 / 63.4 |
| E3: Symbolic → P1 → P2 | 34.7 → 20.6 → 9.2 | 65.0 → 45.6 → 22.4 |
| E4: base → NoOp | 46.0 → 18.0 | 85.1 → 31.7 |

**Holds up:** accuracy falls sharply as clauses are added (P1/P2), and one irrelevant sentence (NoOp) costs up to 53 points.

**Weaker than claimed:** the original → symbolic drop is not significant (95% bootstrap CIs include 0). The spread across datasets is almost exactly binomial noise (observed SD 4.3/4.4 pts vs 4.0/4.8 expected). The sampled numbers are also larger than GSM8K's, which confounds the "numbers" effect.

## Running it

Open the notebook in Google Colab with a T4 GPU and run all cells. The templates are downloaded from Apple's repository at runtime. `QUICK = True` takes about 20–30 min; `QUICK = False` takes several times longer. Model generations are cached in `results/`, so re-running the analysis is instant.

```bash
pip install -r requirements.txt
```

The GSM-Symbolic templates are © Apple and are fetched from [apple/ml-gsm-symbolic](https://github.com/apple/ml-gsm-symbolic) under their license; they are not redistributed here.
