# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

Entry for the Kaggle code competition **Name That LLM** (https://www.kaggle.com/competitions/name-that-llm): classify ~4,000 unlabeled passages (40–250 words, unseen prompts across philosophy, physics, math, biology, CS, creative prose) by which of 18 LLMs wrote them. The repo is currently scaffolding only (no code, no data yet).

Key dates: entry/merge deadline 2027-01-06, final submission 2027-01-13.

## Competition constraints that shape the code

- **Metric is balanced accuracy** (mean per-class recall). Class mix in test is unequal and differs between public and private leaderboards. Use `class_weight='balanced'` / weighted loss and validate with `sklearn.metrics.balanced_accuracy_score`, never plain accuracy.
- **Code competition, no internet.** The submitted notebook reads `/kaggle/input/competitions/name-that-llm/test.csv` and writes `submission.parquet` to its working directory. Pretrained models must be attached as Kaggle datasets, not downloaded at runtime. Limit: 9 h CPU or GPU. In interactive sessions `test.csv` has a single example row; the full ~4000-row test only appears on submission.
- **Submission format:** columns `id`, `model`; one row per id, no nulls, labels restricted to the 18 valid names below, otherwise rejected.
- **Style, not topic or length.** Prompts are unseen, so topic keywords and character-count shortcuts don't generalize. If prompts can be grouped in train data, use group-aware CV to avoid topic leakage.
- Hard part is separating models within the same family (e.g. the four Claude, five OpenAI, four Gemini models); inspect the confusion matrix.

Valid labels: `gpt-5.5`, `gpt-5.6-luna`, `gpt-5.6-sol`, `gpt-5.6-terra`, `gpt-6-astra`, `gemini-3.1-pro-preview`, `gemini-3.6-flash`, `gemini-3.7-flash`, `gemini-3.8-flash`, `claude-fable-5`, `claude-opus-4-6`, `claude-opus-4-8`, `claude-opus-5`, `claude-sonnet-5`, `qwen3.8-max`, `glm-5.3`, `glm-5.3-flash`, `kimi-k3`.

## Layout

- `data/` – Kaggle data (gitignored; `train.csv`/`test.csv` to be placed here for local work)
- `notebooks/` – EDA and the final Kaggle submission notebook
- `src/` – feature extraction and training code, kept importable so the submission notebook can reuse it
- `models/` – saved weights/checkpoints (gitignored); upload to Kaggle as a dataset for offline inference
- `submissions/` – local `submission.parquet` outputs (gitignored)

## Planned approach

TF-IDF (char 2–5-grams + word 1–3-grams) with a linear model as baseline → stylistic features (markdown, dashes, quotes, punctuation, sentence length) → fine-tuned small encoder (DeBERTa-v3 / ModernBERT) → probability ensemble with per-class threshold tuning for balanced accuracy.

## Commands

None established yet; no environment, dependencies or tests exist. Update this section once a baseline is in place.
