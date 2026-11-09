# SDGi report-grouped baselines

Test results (%). Best scores in each column are bold.

## All languages

Train: 5,880 · Validation: 735 · Test: 735

| Model           |       Precision |          Recall |              F1 |     Exact match |
| :-------------- | --------------: | --------------: | --------------: | --------------: |
| XLM-RoBERTa     | **94.34** | **89.57** | **90.31** | **82.72** |
| BERT            |           93.58 |           87.60 |           88.97 |           81.63 |
| SciBERT         |           92.95 |           88.21 |           88.91 |           81.22 |
| DeBERTa-v3-base |           92.65 |           87.92 |           88.41 |           80.54 |
| BioBERT         |           90.93 |           88.45 |           88.26 |           79.73 |
| RoBERTa-base    |           93.32 |           87.02 |           88.23 |           82.04 |

## English only

Train: 4,226 · Validation: 528 · Test: 528

| Model           |       Precision |          Recall |              F1 |     Exact match |
| :-------------- | --------------: | --------------: | --------------: | --------------: |
| BERT            |           90.48 | **84.96** | **85.62** |           75.38 |
| RoBERTa-base    | **91.63** |           83.85 |           85.36 | **76.70** |
| XLM-RoBERTa     |           90.94 |           84.52 |           85.36 |           75.76 |
| SciBERT         |           91.37 |           83.44 |           84.92 |           75.38 |
| BioBERT         |           90.44 |           83.76 |           84.90 |           76.52 |
| DeBERTa-v3-base |           88.13 |           81.15 |           82.13 |           72.92 |

Precision, recall and F1 are averaged across passages. Exact match requires the complete predicted label set to match the true labels.

All runs use 10 training epochs, seed 42 and threshold 0.5. The checkpoint with the lowest validation loss is evaluated. These are single-seed results.

The datasets have different test sets, so the tables are not a controlled comparison of language settings.

Source: the 12 model-level results.csv files in the all and en directories.
