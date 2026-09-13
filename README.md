# CHURRO LOC Layout-Robust Epoch 22: complete data lineage

This bundle contains the training, optimization-validation, and separately
marked evaluation-only data used across the complete weight lineage of the
promoted **CHURRO LOC Layout-Robust Epoch 22** adapter.

## Scope

- Epochs 1-19: mixed-scale corpus with 2,400 line rows and 600 page rows for
  training, plus 264 line and 17 page validation rows.
- Epoch 20: 600 official-text training page rows and 17 validation pages, used
  for first-occurrence grounding.
- Epoch 21: the same 600/17 page corpus, used for learned visual grounding.
- Epoch 22: 2,500 full-page training rows and 250 full-page validation rows.
- Evaluation only: the protected unseen-100 set and 45-page premature-omission
  benchmark. These are isolated under `manifests/*/evaluation_only.jsonl` and
  were not gradient-bearing data.

Later stages inherit earlier LoRA weights, so the epoch-22 model reflects all
four stages. Repeated stage manifests are intentionally preserved; image bytes
are content-addressed and stored once.

## Layout

```text
assets/images/<sha-prefix>/<sha256>.<ext>   deduplicated image assets
manifests/<stage>/<role>.jsonl              portable image/text rows
provenance/<stage>/config.json              recorded training configuration
provenance/<stage>/summary.json             dataset or training summary
lineage.json                                checkpoint ancestry
summary.json                                counts and leakage audit
LEAKAGE_AUDIT.md                            cross-stage overlap findings
overlap_audit.json                          exact overlapping IDs and hashes
SHA256SUMS                                  release integrity hashes
```

Each portable manifest embeds the supervised `text` and points `image` to a
relative content-addressed asset. Workstation-specific auxiliary paths were
removed. `image_sha256` provides identity independent of filenames.

## Leakage interpretation

The same trusted page corpus was deliberately replayed across epochs 20 and
21, and parts may reappear in the expanded epoch-22 training corpus. That is
weight-lineage reuse, not an additional unique page. See `summary.json` for
content-hash overlap counts. Evaluation-only images must remain excluded from
future training and optimization validation.

The audit found zero training overlap in the protected unseen-100 set. It also
found six epoch-22 validation images used in earlier training and four images
from the omission-45 benchmark used during epoch-22 training. Consequently,
the full validation-250 and omission-45 results are not wholly unseen across
the complete inherited weight lineage. See `LEAKAGE_AUDIT.md`; do not conceal
or discard these records when reporting results.

## Labels

The source dataset summaries state that model predictions were not used as
training text. Labels come from official LOC per-page text, LOC TEI page text,
or LOC page metadata descriptions, as recorded per row. These sources have
different evidentiary strength and should remain distinguishable in analysis.
