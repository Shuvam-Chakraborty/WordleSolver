# 🟩 Wordle Solver — Algorithm Benchmark

Benchmarks 8 Wordle-solving strategies across a 20,159-word vocabulary. Built and run on a **Kaggle P100 GPU**.

---

## Dataset

Three public word lists are merged and filtered to 5-letter alphabetic words:

| Source | Words Added |
|---|---|
| SOWPODS | 12,478 |
| Wordle Allowed list | 2,389 |
| Big Kahuna | 5,292 |
| **Total** | **20,159** |

Saved to `combined_5_letter_words.csv`.

---

## How It Works

A **20,159 × 20,159 feedback matrix** is precomputed once (~88s, ~406 MB) and shared across all models. Each cell encodes the Wordle colour feedback for every (guess, answer) pair: `2` = green, `1` = yellow, `0` = grey.

---

## Models

| # | Model | Strategy | First Guess |
|---|---|---|---|
| M1 | Positional Frequency | Score words by per-position letter frequency | SANES |
| M2 | Entropy | Maximise Shannon entropy over remaining candidates | TARES |
| M3 | Minimax | Minimise worst-case partition size | RAISE |
| M4 | Dynamic Programming | Minimise expected future cost via depth-limited tree | TARES |
| M5 | Bayesian | Maximise expected info gain under a posterior distribution | TARES |
| M6 | ERC | Minimise expected number of remaining candidates | LARES |
| M7 | Hybrid Entropy-Minimax | Entropy in early game, minimax in late game | TARES |
| M8 | Beam Search (B=100) | Multi-path search over top-100 candidate guesses | TARES |

---

## Results

| Rank | Model | Avg Steps | Time (s) |
|---|---|---|---|
| 🥇 | M5: Bayesian | **4.2346** | 1951 |
| 2 | M2: Entropy | 4.2355 | 1988 |
| 3 | M4: DP | 4.2358 | **69** |
| 4 | M8: Beam Search | 4.2360 | 2370 |
| 5 | M6: ERC | 4.2899 | **19** |
| 6 | M7: Hybrid | 4.2965 | 2339 |
| 7 | M3: Minimax | 4.4672 | 1637 |
| 8 | M1: Positional Freq | 5.2193 | 653 |

**Best accuracy:** M5 Bayesian at 4.23 avg guesses.  
**Fastest:** M6 ERC solves all 20,159 words in under 20 seconds.  
**Sweet spot:** M4 DP matches Bayesian accuracy at just 69 seconds (~28× faster).  
**Best opener:** `TARES` — independently chosen by 5 of 8 models.

---

## Requirements

```
numpy, pandas, matplotlib, seaborn, requests, torch
```

Run the notebook on **Kaggle with GPU (P100) enabled**. The feedback matrix build takes ~90s; full benchmark runs take several hours (M4 and M6 complete in under 2 minutes and are recommended for quick testing).
