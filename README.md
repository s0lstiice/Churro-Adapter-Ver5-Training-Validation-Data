# Churro Adapter Version5: complete training and validation lineage

This bundle contains the training, optimization-validation, and separately
marked evaluation-only data used across the complete weight lineage of the
promoted **Churro Adapter Version5**.

## Scope

- **VerA**: mixed-scale corpus with 2,400 line rows and 600 page rows for
  training, plus 264 line and 17 page validation rows.
- **VerB**: 600 official-text training page rows and 17 validation pages, used
  for first-occurrence grounding.
- **VerC**: the same 600/17 page corpus, used for learned visual grounding.
- **Version5**: 2,500 full-page training rows and 250 full-page validation rows. This is the promoted model.
- Evaluation only: the protected unseen-100 set and 45-page premature-omission
  benchmark. These are isolated under `evaluation/` and
  were not gradient-bearing data.

Version5 inherits the earlier LoRA weights in this exact order:
`stanford-oval/churro-3B -> VerA -> VerB -> VerC -> Version5`.
Repeated rows are intentionally preserved because they reproduce training
weighting. Historical epoch names remain only inside provenance records.

## Layout

```text
versions/VerA/{train,validation}/            VerA manifests and browsable images
versions/VerB/{train,validation}/            VerB manifests and browsable images
versions/VerC/{train,validation}/            VerC manifests and browsable images
versions/Version5/{train,validation}/        Version5 manifests and browsable images
versions/<name>/provenance/                  exact historical configuration and summary
evaluation/ProtectedUnseen100/               clean protected comparison holdout
evaluation/PrematureOmission45/              targeted omission benchmark
lineage.json                                checkpoint ancestry
summary.json                                counts and leakage audit
LEAKAGE_AUDIT.md                            cross-stage overlap findings
overlap_audit.json                          exact overlapping IDs and hashes
SHA256SUMS                                  release integrity hashes
```

Each split has `manifest.jsonl` beside `images/`. Images are grouped by their
LOC item ID and use readable page/line IDs instead of hash-only filenames.
Every manifest embeds the supervised `text`; `image_sha256` still provides
content identity independent of filenames. On GitHub, a generic file icon in
the directory listing is normal—click the `.jpg` or `.png` filename to preview
the actual image.

## Leakage interpretation

The same trusted page corpus was deliberately replayed across VerB and VerC,
and parts may reappear in the expanded Version5 training corpus. That is
weight-lineage reuse, not an additional unique page. See `summary.json` for
content-hash overlap counts. Evaluation-only images must remain excluded from
future training and optimization validation.

The audit found zero training overlap in the protected unseen-100 set. It also
found six Version5 validation images used in earlier training and four images
from the omission-45 benchmark used during Version5 training. Consequently,
the full validation-250 and omission-45 results are not wholly unseen across
the complete inherited weight lineage. See `LEAKAGE_AUDIT.md`; do not conceal
or discard these records when reporting results.

## Labels

The source dataset summaries state that model predictions were not used as
training text. Labels come from official LOC per-page text, LOC TEI page text,
or LOC page metadata descriptions, as recorded per row. These sources have
different evidentiary strength and should remain distinguishable in analysis.
