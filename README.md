# BIPIA EmailQA Indirect Prompt Injection Detection

This repository contains an Advanced Artificial Intelligence Assignment 1 experiment on detecting indirect prompt injection attacks in external email content.

The project derives a supervised binary classification task from the BIPIA EmailQA benchmark:

- `benign`: the original email context.
- `attack`: the same email context with an injected malicious instruction.

The work is connected to secure Retrieval-Augmented Generation systems, where external documents or emails should be treated as untrusted data rather than executable instructions.

## Dataset source

- BIPIA benchmark: https://github.com/microsoft/BIPIA
- Task: EmailQA
- Access date: October 1, 2026
- BIPIA project licence: MIT License
- EmailQA source: OpenAI Evals, https://github.com/openai/evals

The repository includes the BIPIA EmailQA context files and text-attack files required to reproduce the derived binary dataset.

## Dataset construction and leakage control

The binary dataset was created by pairing each email context with:

1. A clean benign sample.
2. An attacked sample formed by appending one text-based indirect prompt injection attack.

Initial data checks found 11 overlapping full texts between the training and test sets, corresponding to 13 overlapping test contexts. To prevent data leakage, all samples from these 13 test contexts were removed.

Final datasets:

| Split | Samples | Benign | Attack |
|---|---:|---:|---:|
| Training | 100 | 50 | 50 |
| Leakage-free test | 74 | 37 | 37 |

The model input is the `text` column only. Identifiers and metadata, including `sample_id`, `context_id`, `split`, `attack_category`, and `attack_number`, are excluded from model features.

## Models

All models use identical TF-IDF preprocessing with lowercase word unigrams and bigrams.

1. Dummy Baseline
2. Multinomial Naive Bayes
3. Logistic Regression
4. Linear Support Vector Machine

## Main results

| Model | Accuracy | Macro F1 | Attack Recall | False Positive Rate | AUC |
|---|---:|---:|---:|---:|---:|
| Linear SVM | 0.649 | 0.642 | 0.514 | 0.216 | 0.760 |
| Logistic Regression | 0.554 | 0.550 | 0.459 | 0.351 | 0.697 |
| Multinomial Naive Bayes | 0.568 | 0.483 | 0.162 | 0.027 | 0.679 |
| Dummy Baseline | 0.500 | 0.333 | 0.000 | 0.000 | 0.500 |

Linear SVM achieved the highest Macro F1 score on the leakage-free test set containing unseen attack categories.

## Ablation

The ablation compared unigram-only TF-IDF features against unigram-bigram TF-IDF features.

| Model | Unigrams only Macro F1 | Unigrams and bigrams Macro F1 |
|---|---:|---:|
| Dummy Baseline | 0.333 | 0.333 |
| Multinomial Naive Bayes | 0.435 | 0.483 |
| Logistic Regression | 0.590 | 0.550 |
| Linear SVM | 0.699 | 0.642 |

The original hypothesis that bigrams explain Linear SVM's advantage was not supported. The results indicate that individual response-oriented words generalise better than exact word pairs across unseen attack templates. Adding bigrams increases sparsity in this small training dataset and reduces Linear SVM performance.

## Repository structure

```text
data/
  raw/
    email/
    attacks/
  processed/
outputs/
  metrics/
src/
  BIPIA_Email_Assignment1_GitHub.ipynb
requirements.txt
README.md
```

## Reproduction

1. Clone the repository and open a terminal in the repository root.
2. Install dependencies:

```bash
pip install -r requirements.txt
```

3. Open and run all cells in:

```text
src/BIPIA_Email_Assignment1_GitHub.ipynb
```

The notebook builds the derived dataset, applies leakage removal, trains all four models, evaluates the held-out test set, runs the n-gram ablation, and saves the outputs.

## Contributions

- Najwa Odeh Abu Amssimir: dataset preparation, data-quality checks, leakage analysis, model experiments, ablation, result interpretation, repository preparation, and presentation preparation.

## AI assistance disclosure

AI tools were used as an assistant for selected parts of the code structure and for explaining some experimental results. All experiments were executed and verified by the author.

## Citation

Yi, J., Xie, Y., Zhu, B., Hines, K., Kiciman, E., Sun, G., Xie, X., & Wu, F. (2023). *Benchmarking and Defending Against Indirect Prompt Injection Attacks on Large Language Models*. arXiv:2312.14197.
