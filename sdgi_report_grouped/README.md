# SDGi report-grouped passages

[01_prepare_sdgi_report_grouped.ipynb](01_prepare_sdgi_report_grouped.ipynb) prepares the dataset from both official SDGi splits. Run the notebook from top to bottom in the thesis environment.

## Report grouping

Passages from the same source report stay in one split to reduce overlap in report wording and context. Report IDs come from metadata.file_id after removing the SDG folder and final numeric passage suffix. Year and language remain part of the ID, so translations and differently named versions may still appear in separate splits.

Reports are shuffled with seed 42, processed from largest to smallest, and assigned to the largest remaining passage deficit. Assignment uses report sizes only, with targets of 80% training, 10% validation and 10% test.

| Split | Passages |
|---|---:|
| Train | 5,880 |
| Validation | 735 |
| Test | 735 |

## Saved files

The data folder contains train.parquet, validation.parquet, test.parquet, split_summary.csv and sdg_coverage.csv.

Each passage retains text, labels, metadata, official_split, report_id and split. Original embeddings are omitted.

Training starts from a fresh pretrained checkpoint. Validation supports model selection; test is reserved for final evaluation. Report-grouped results are reported separately from the official passage-split baseline.
