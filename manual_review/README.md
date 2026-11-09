# Manual review experiment

`manual_review.ipynb` follows the existing training notebooks' numbered sections, Markdown/code layout, imports, metric conventions and W&B setup. It evaluates all six model families in both `all` and `en` variants using the saved fine-tuned checkpoints, with each checkpoint's saved tokenizer, maximum length 512 and sigmoid threshold 0.5.

Use the existing `thesis` Python environment and run the notebook from this directory or another directory inside the thesis workspace. Dependencies match `base-lines-sdgi-report-grouped/requirements.txt`, plus Jupyter execution tools. The root `.env` supplies credentials; values are never embedded in the notebook.

The two-column CSV and provenance are in `dataset/`. See `dataset/README.md` for collection and annotation details.

The notebook saves `results/comparison.csv` and `results/predictions.csv` and logs the evaluation to W&B. `results/wandb_run.json` records the latest experiment URL. Precision, recall and F1 are averaged across passages; accuracy requires an exact label-set match, matching the training notebooks.

Additional diagnostic files from the original detailed evaluation remain in `results/` for reference. The simplified notebook focuses on the four training metrics and does not regenerate those diagnostics.
