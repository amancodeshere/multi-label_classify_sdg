# SDGi report-grouped baselines

Each model directory contains model_finetuned.ipynb, the saved model, results.csv and training_history.json. The notebooks retain their executed cells and outputs. Training also creates a checkpoints folder.

- all uses sdgi_report_grouped/data for all languages.
- en uses sdgi_report_grouped_en/data for English passages.

Use the thesis environment and run each training notebook from top to bottom. Dependencies are listed in requirements.txt.

## Models

| Directory | Notebook | Pretrained checkpoint |
|---|---|---|
| xlm_roberta | xlm_roberta_finetuned.ipynb | FacebookAI/xlm-roberta-base |
| bert | bert_finetuned.ipynb | google-bert/bert-base-uncased |
| scibert | scibert_finetuned.ipynb | allenai/scibert_scivocab_uncased |
| biobert | biobert_finetuned.ipynb | dmis-lab/biobert-base-cased-v1.2 |
| roberta_base | roberta_base_finetuned.ipynb | FacebookAI/roberta-base |
| deberta_v3 | deberta_v3_finetuned.ipynb | microsoft/deberta-v3-base |

## Training and evaluation

All models start from their original pretrained checkpoints and train for 10 full epochs without early stopping. Settings are seed 42, learning rate 2e-5, batch size 4, weight decay 0.01, dynamic padding and a maximum of 512 tokens.

The checkpoint with the lowest validation loss is used for final test evaluation. Predictions use a sigmoid threshold of 0.5. Precision, recall and F1 are averaged across passages; accuracy requires an exact label-set match.

The executed comparison notebooks sort results by validation samples F1 and save comparison.csv:

- [All languages](all/00_compare_results.ipynb)
- [English only](en/00_compare_results.ipynb)

The two datasets have different test sets and are compared separately. These are baselines with shared training settings rather than individually tuned models. XLM-RoBERTa is multilingual; the other checkpoints were pretrained primarily on English. Report-grouped results are reported separately from the official passage-split baseline.

## W&B tracking

The thesis .env file supplies WANDB_API_KEY and HF_TOKEN. Runs are logged to thesis-sdgi-report-grouped and named model/all or model/en.

W&B stores training settings, losses, validation metrics and final scores. Models and executed notebooks are saved locally. Earlier outputs are retained in previous_runs.
