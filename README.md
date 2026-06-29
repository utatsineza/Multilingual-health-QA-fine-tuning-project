# Multilingual Health Question Answering — Low-Resource African Languages

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](COLAB_LINK_HERE)

Final project for Machine Learning Techniques I — African Leadership University.
Fine-tunes google/mt5-small to answer maternal, sexual, and reproductive health 
questions in eight language/country subsets including four low-resource African 
languages: Akan (Ghana), Luganda (Uganda), Swahili (Kenya), and Amharic (Ethiopia).

**Zindi competition:** Multilingual Health Question Answering in Low-Resource 
African Languages Challenge by ITU

**Student:** Henriette Utatsineza  
**Kaggle username:** henrietteutatsineza  
**Public leaderboard score:** 0.255044  
**Private leaderboard score:** 0.257264  

---

## Project Overview

- **Task:** Given a health question in one of 8 language/locale subsets, generate 
  a fluent, accurate answer in the same language
- **Evaluation:** Weighted mean of ROUGE-1 F1 (0.37), ROUGE-L F1 (0.37), 
  and LLM-as-a-Judge (0.26)
- **Approach:** Fine-tune google/mt5-small as a conditional generation task, 
  with a language-tag prefix prepended to each input

---

## Dataset

| Subset  | Language | Train | Test | Avg answer (words) |
|---------|----------|------:|-----:|-------------------:|
| Eng_Uga | English (Uganda) | 7,624 | 744 | 95.4 |
| Aka_Gha | Akan (Ghana) | 4,455 | 492 | 105.6 |
| Eng_Gha | English (Ghana) | 4,443 | 491 | 75.1 |
| Eng_Eth | English (Ethiopia) | 3,915 | 60 | 24.5 |
| Lug_Uga | Luganda (Uganda) | 3,383 | 374 | 79.7 |
| Eng_Ken | English (Kenya) | 2,080 | 167 | 78.7 |
| Swa_Ken | Swahili (Kenya) | 2,070 | 229 | 84.3 |
| Amh_Eth | Amharic (Ethiopia) | 1,845 | 61 | 20.2 |

---

## Repository Structure
.

├── README.md

├── colab_experiments.ipynb     # Experiments 1-2 (Google Colab)

├── kaggle_pipeline.ipynb       # Full pipeline: EDA → training → submission (Kaggle)

├── experiment_tracker.xlsx     # Full experiment log (10 experiments)

├── submissions/

│   └── submission_exp2.csv     # Final submission file (Exp 3, score: 0.255044)

└── report/

└── Feza_FinalProject.pdf
---

## Platform Note

Early experiments (Exp 1 fp16 failure, Exp 2 fp32 2-epoch baseline) were conducted 
in Google Colab. The project migrated to Kaggle when free-tier GPU quota was 
exhausted after ~3 hours. The full reproducible pipeline is in 
`kaggle_pipeline.ipynb`. This migration is documented in Section 6.5 of the report.

---

## Data Access

Data is provided by Zindi under CC-BY SA 4.0. To reproduce:
1. Join the competition on Zindi
2. Download Train.csv, Test.csv, SampleSubmission.csv from the Data tab
3. Upload to Kaggle as a dataset or place in the data/ folder for Colab

---

## How to Reproduce (Kaggle)

1. Open `kaggle_pipeline.ipynb` in Kaggle
2. Add the dataset as input
3. Set accelerator to GPU T4 x2
4. Run all cells top to bottom
5. The final cell generates `submission_exp2.csv`

Key notes:
- Use `fp16=False` — fp16 causes NaN loss with mT5 on T4 GPUs
- Use `save_strategy="no"` — checkpoints fill the 20GB disk quota quickly
- Use `per_device_train_batch_size=4` — batch size 8 causes OOM with fp32 on dual T4
- Fixed random seed: `random_state=42`

---

## Key Results

| Experiment | Change | Val Loss | Public LB Score |
|------------|--------|----------|-----------------|
| Exp 1 | mT5-small, fp16=True, 3 epochs | NaN (failed) | — |
| Exp 2 | fp32, 2 epochs, batch=8 | 4.28 | — |
| Exp 3 | fp32, 4 epochs, batch=4 | 3.48 | **0.255044** |

---

## Ethical Considerations

This model generates health information in sensitive domains (maternal, sexual, 
and reproductive health). It is a course project and must not be used to make 
real health decisions without review by qualified professionals. Key risks include 
hallucinated medical claims and lower performance for the most underrepresented 
language communities (Amharic, Akan).

---

## AI Usage Disclosure

Claude (Anthropic) was used for debugging environment errors, explaining 
HuggingFace API changes, and drafting boilerplate code. All experimental 
decisions, results, and analysis are the student's own work.

---

## References

- Xue et al. (2021). mT5: A massively multilingual pre-trained text-to-text transformer. NAACL 2021.
- Wolf et al. (2020). Transformers: State-of-the-art NLP. EMNLP 2020.
- Lin (2004). ROUGE: A package for automatic evaluation of summaries.
- ITU/HASH Consortium (2026). Multilingual Health QA Challenge. Zindi Africa.