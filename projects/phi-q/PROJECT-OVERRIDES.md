# Phi-4-mini Quant Project Overrides

Inherits `doctrine/DOCTRINE.md`, `doctrine/AUTHORITY-BASELINE.md`, and `doctrine/GITHUB-BILLING-SAFETY.md`.

Overrides may only add restrictions. They may not weaken baseline safety, evidence, scope, or billing controls.

## * Reporting additions for this project

Reports must additionally include:

- The exact evaluation corpus and chunking used
- Bits per weight and the allocation map for mixed-precision runs
- Whether a result is perplexity, generation, or both
- Explicit statement that perplexity alone is not a quality claim
- The frozen baseline comparison for every measured number

## * Authority restrictions for this project

- No paid cloud resource may be created without separate explicit approval.
- No training run may start without a stated time budget.
- No strict ternary training may begin under this project ID.
- No prior artifact, receipt, or model file may be modified or removed.

## * Evidence additions for this project

- Every measured number must name the artifact hash it was measured on.
- Every mixed-precision result must record the full allocation rule, not just the average bits.
- Failed and aborted runs must be preserved as first-class evidence.
