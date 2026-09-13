# Cross-stage leakage audit

Identity was checked using exact SHA-256 image hashes across every compiled
manifest. Individual dataset builders checked their own split boundaries, but
the promoted checkpoint inherits all earlier stages, so cross-stage checks are
also required.

## Findings

- Protected unseen-100 versus any training stage: **0 overlaps**.
- Training versus optimization validation across the complete lineage:
  **6 exact-image overlaps**.
- Training versus the 45-page omission benchmark: **4 overlaps**.

The protected unseen-100 benchmark remains clean by exact image hash. The
complete epoch-22 validation split is not wholly unseen across the inherited
weight lineage, because six of its pages were used in earlier training. The
omission benchmark contains four pages used during epoch-22 training. Metrics
for those two sets must either exclude the listed pages or label the results as
partially contaminated.

## Epoch-22 validation pages seen during earlier training

- `mal1466200_page_001`
- `mal1466200_page_002`
- `mal1468000_page_001`
- `mal1468000_page_002`
- `mal1469000_page_001`
- `mal1477000_page_001`

## Omission-benchmark pages seen during training

- `mal1463400_page_001`
- `mal1463400_page_002`
- `mal1468500_page_001`
- `mal1469200_page_001`

See `overlap_audit.json` for every role, stage, manifest, and image hash.
