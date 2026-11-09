# Abstract 2 SDG — 10-week Thesis B implementation plan

Deliver a deployed classifier selected through a controlled comparison on real research abstracts, and a UNSW website where readers click any of the 17 SDGs to see matching papers. Newly indexed papers enter a researcher review workflow so authors can confirm or propose corrections to their tags.

### Week 1 — Label 28 papers and pilot the generators

- Collect the 57 new UNSW papers through OpenAlex. Sample across disciplines, include papers with no supported SDG, and check for duplicates and publication versions. Assign paper IDs to training, development and test before generation.
- Label the first 28 papers using the same rubric for all 17 goals. Save the full label set and a short evidence note per goal.
- Pilot a GPT candidate, a Claude candidate and a smaller open-weight model on the same 8–10 training seeds. Treat GPT-6 Astra and Claude Opus 5.5 as access-dependent candidates. Fix the prompt, output format, generation budget and acceptance rules.
- Verify the OpenAlex affiliation filters on a small sample. Start hosting and researcher email access requests.
- **Deliverable.** First 28 labelled papers, split manifest and pilot outputs from the available decoders.

### Week 2 — Complete annotation and build one dataset per decoder

- Label the remaining 29 papers. Combine them with the original 43 for **100 labelled examples, including 92 paper abstracts and eight showcase summaries**.
- Use 17 of the new papers plus the original 43 for training, 15 new papers for development and 25 new papers for the locked test. Keep related versions together. The 60 training seeds yield **300 synthetic abstracts per decoder** at five variants per seed. To reach **500 per decoder**, add 40 compatible, previously labelled external abstracts to the shared training seed pool after checking overlap. Freeze the same seed pool for every decoder.
- Generate five accepted examples per seed per decoder. Vary topic, setting and writing style while preserving the full SDG label set. Check for duplicates and spot-check missed secondary labels, unsupported labels and implausible text. Save seed IDs, model IDs, prompt version, output and rejection counts. Use matching seed and variant slots for the main comparison.
- Import a small batch of OpenAlex papers into the database. Store stable work IDs, DOI, title, authors, year and abstract availability.
- **Deliverable.** Frozen real-data splits, separate synthetic datasets and a working sample import.

### Week 3 — Fine-tune SciBERT on each dataset

- Run the frozen Thesis A SciBERT checkpoint on the development split. Then train a real-only model, a duplicate-oversampling control and one real-plus-synthetic model per decoder.
- Start each run from the same SDGi-trained checkpoint. Match optimizer, seed, batch size and update budget. Match the ratio of real to added examples between the oversampling control and synthetic runs. Start with multi-label binary cross-entropy, AdamW, learning rate 2e-5, effective batch size 32, maximum length 512 and a shared cap of three epoch-equivalents.
- Run a layer-freezing ablation on the hand-curated training split. From the same saved SciBERT checkpoint, compare training only the existing 17-label head, freezing embeddings and the bottom nine encoder blocks while training the top three and the head, and fine-tuning all layers. Include the frozen checkpoint with a development-tuned threshold. Match training examples, update budget and evaluation protocol, and record trainable parameters and wall time. Keep the freezing recipe fixed across decoder datasets in the main comparison. If partial freezing looks promising, repeat it on the best synthetic dataset selected on development data before opening the locked test. [Lee et al., Freezing Layers During Transformer Fine-Tuning](https://arxiv.org/abs/1911.03090)
- Add scheduled OpenAlex ingestion with DOI or source-ID deduplication. Start the frontend with a 17-goal landing page backed by sample data.
- **Deliverable.** First trained checkpoint and development predictions for each dataset, plus a scheduled importer and a clickable goal page.

### Week 4 — Compare datasets on real abstracts

- Tune a shared threshold for each run on the same 15 development papers. Inspect example F1, exact match, false tags and missed goals per paper. Check whether improvements come from synthetic data or from seeing more examples of the same label sets.
- Freeze the recipes, thresholds and planned seeds before opening the 25-paper test. Run the locked comparison together once those runs are ready. Report per-goal support and uncertainty given the small test set.
- Build the paginated endpoint for each goal and connect it to paper lists with title, authors, year and source link. Define the suggested, pending review and reviewed states.
- **Deliverable.** Development comparison and frozen test protocol, with locked results by week end if planned runs are ready. Goal pages load from the backend.

### Week 5 — Finish the model comparison and integrate the worker

- Complete any scheduled seed repeats and locked evaluation carried over from Week 4. Select the deployment candidate using the prespecified comparison. Measure throughput on a batch of OpenAlex abstracts.
- Package the checkpoint, tokenizer, label map and thresholds. Make the worker classify new or changed abstracts, store versioned predictions and leave missing abstracts unclassified.
- Connect the paper lists to worker output. Verify that rerunning an import creates no duplicate papers or predictions.
- **Deliverable.** Model comparison table, packaged checkpoint and a working import-to-goal-page flow.

### Week 6 — Deploy staging and build the researcher review flow

- Deploy the frontend, backend, database and scheduled worker to staging. Use the Thesis A checkpoint as the fallback release.
- Build a queue that emails one verified UNSW author per eligible new paper. Include the paper and suggested SDGs with a secure review link. Start with a sandbox mailbox.
- Add actions to confirm tags or propose additions and removals. Record the original prediction and the author's response separately.
- **Deliverable.** Accessible staging site and a working sandbox email and review form.

### Week 7 — Deploy the selected model and backfill

- Install the selected model in staging and run the initial backfill for a defined publication window. Record imported, classified and missing-abstract counts and measure throughput.
- Verify that a paper can appear under multiple goals and that lists show model-suggested or reviewed status. Display the covered date range and last successful refresh.
- Keep historical backfill separate from invitations for newly ingested papers so the first import does not trigger bulk outreach.
- **Deliverable.** Populated staging catalogue and a documented backfill and rollback procedure.

### Week 8 — Test author consultation and corrections end to end

- Import a new paper, classify its abstract and send a sandbox email. Use the review link to propose adding one goal and removing another.
- Review the proposal with the author in the test scenario and record the agreed correction. Verify that the paper moves between goal lists accordingly and that another sync or inference run preserves the correction.
- Test a failed send, an unmatched author and a repeated import. Confirm the queue can recover without duplicate notifications.
- **Deliverable.** Recorded end-to-end demo covering import, notification, consultation and updated public tags.

### Week 9 — Release and monitor the hosted catalogue

- Publish the hosted goal catalogue and verify scheduled refreshes, source links and the new-paper workflow in the release environment.
- Start a small researcher pilot if the contact channel is ready. Track emails delivered, responses, agreed corrections and time to resolve them. Use the sandbox demo if live access is pending.
- **Deliverable.** Hosted URL, refresh logs and pilot status.

### Week 10 — Fix issues and hand over

- Resolve remaining deployment and review issues. Check that the scheduled refresh recovers from failure and that the previous model can be restored.
- Package the evaluation results, dataset manifests, prompts, model settings, coverage limits and deployment instructions.
- **Deliverable.** Final catalogue and model release, experiment evidence for the thesis and a runbook for whoever maintains the site.

### Week-by-week Gantt

**●** active work · **◆** milestone or completion check

| Workstream | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 | 10 |
|---|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| Hand-label 57 papers and lock splits | ● | ◆ | | | | | | | | |
| Pilot and generate per-decoder datasets | ● | ◆ | | | | | | | | |
| Fine-tune SciBERT and compare datasets | | | ● | ◆ | | | | | | |
| Verify shortlist and package model | | | | | ● | ◆ | | | | |
| OpenAlex ingestion, database and API | ● | ● | ● | ● | ◆ | | | | | |
| Goal landing page and paper lists | | | ● | ● | ● | ◆ | | | | |
| Author email, review and corrections | | | | ● | ● | ● | ● | ◆ | | |
| Model serving, staging and backfill | | | | | | ● | ● | ◆ | ● | | |
| End-to-end checks and hosted release | | | | | | | ● | ● | ◆ | |
| Buffer, documentation and handover | | | | | | | | | ● | ◆ |

