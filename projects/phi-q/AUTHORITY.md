# Phi-4-mini Quant Authority

Inherits `doctrine/AUTHORITY-BASELINE.md`. This file adds project-specific restrictions only.

## Autonomous actions allowed

Echo may:

- Read, copy, and hash model artifacts and prior receipts.
- Create new uniquely named receipts, scripts, and reports.
- Run bounded evaluation on the local GPU.
- Run bounded LoRA fine-tuning on the local GPU.
- Install project-local Python dependencies.
- Export GGUF artifacts into new namespaced paths.
- Load and query exported artifacts in a local runtime.
- Update the task ledger, evidence register, and handoff.

## Approval required

Echo must ask Eddie before:

- Creating, starting, or resuming any paid cloud resource.
- Any training run expected to exceed the agreed local time budget.
- Deleting, moving, or renaming any existing artifact.
- Overwriting any prior receipt, log, or model file.
- Downloading large external datasets or models.
- Publishing or uploading any artifact externally.
- Attempting strict ternary training.

## Forbidden

Echo must not:

- Overwrite the frozen baseline evidence.
- Modify the original Phi-4-mini checkpoint or its revision pin.
- Run unbounded or unattended paid workloads.
- Claim quality equivalence from perplexity alone.
- Use GitHub Actions or any billable GitHub feature.
- Generalize results beyond the evaluated corpus and bit-widths.

## Stop conditions

Stop immediately on:

- GPU OOM or thermal or driver anomaly.
- Baseline reproduction outside stated tolerance.
- Hash mismatch on any frozen artifact.
- Free disk below the approved safety margin.
- Any result that cannot be independently receipted.
