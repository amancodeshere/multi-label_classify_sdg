# SDGi English-only report-grouped passages

[01_prepare_sdgi_report_grouped_en.ipynb](01_prepare_sdgi_report_grouped_en.ipynb) prepares and verifies the dataset from both official SDGi splits. Run the notebook from top to bottom in the thesis environment.

## English filtering and report grouping

Only passages labelled en in metadata.language are retained. Filtering happens before splitting, so assignments can differ from the multilingual dataset.

Report IDs come from metadata.file_id after removing the SDG folder and final numeric passage suffix. Each inferred report stays in one split. Year and the rest of the filename are retained, so differently named versions can still appear in separate splits.

Reports are shuffled with seed 42, processed from largest to smallest, and assigned to the largest remaining passage deficit. Assignment uses report sizes only, with targets of 80% training, 10% validation and 10% test.

| Split | Passages |
|---|---:|
| Train | 4,226 |
| Validation | 528 |
| Test | 528 |

## Saved files and verification

The data folder contains train.parquet, validation.parquet, test.parquet, split_summary.csv and sdg_coverage.csv.

Each passage retains text, labels, metadata, official_split, report_id and split. Original embeddings are omitted.

Verification compares every saved field with the raw English records and expected assignments, then checks language, report separation, duplicate texts and summary tables.

Training starts from a fresh pretrained checkpoint. Validation supports model selection; test is reserved for final evaluation. The original corpus and multilingual dataset remain separate records.
