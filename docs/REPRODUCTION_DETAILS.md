# What Can Activation Patching Actually Establish? — code, data and claim ledger

Release accompanying the arXiv version of Kadu, Kalyanasundaram and Krishnan, *What Can Activation Patching Actually
Establish? Reachable-State Geometry and the Limits of Mechanistic Evidence*. An earlier version was accepted as a poster
at the NeurIPS 2026 Workshop TAE (Trust-AI-Eval); the arXiv version corrects errors found after acceptance
(Appendix A of the paper; `corrections_ledger.json`).

## Contents

| path | what it is |
|---|---|
| `e0/*.py` | analysis, capture and figure-data scripts |
| `e0/*.npz`, `e0/*.json` | intervention captures and the analysis artifacts every number is derived from (`DATA_FILES.txt`) |
| `ledger_paperA.py`, `ledger_additions.py` | the claim ledger: re-derives every number in the paper from hashed artifacts |
| `claims_paperA.tex`, `claims_paperA.json` | ledger output: one LaTeX macro per number, with unit, aggregation, note and artifact hashes |
| `MANIFEST.sha256` | hashes of every script and data file above |
| `prereg/` | the preregistration of the fresh-template replication, its prompt pool, and the hashes frozen before any fresh forward pass (`FREEZE.sha256`; `MANIFEST_at_freeze.sha256` is the manifest as frozen) |
| `replication/` | the replication's registered outputs (`analysis/analysis.json`, `pilot/pilot_report.json`) and its 576 fresh captures (`fresh/*.npz`, hashed in `CAPTURES.sha256`) |
| `corrections_ledger.json`, `CORRECTIONS_LEDGER.md` | the 57 corrections to the accepted version, with sources, scripts, independent re-derivations and review verdicts |
| `paper/` | manuscript sources; `paper/build.sh` builds the PDF from this tree's own ledger output |
| `tools/` | manifest, release and verification scripts |

## Verify

Requirements: Python 3.13 with the packages in `requirements.txt`; `pdflatex`, `bibtex` and `pdftotext` for the paper.
No GPU is needed for verification.

```bash
sha256sum -c MANIFEST.sha256                          # every script and data file
python3 ledger_paperA.py --verify                     # re-derive every number in the paper; fails on any drift
python3 -m pytest -q e0/test_attribution_adequacy.py e0/test_heldout_semantics.py
(cd paper && E0_DIR=../e0 ./build.sh)                 # rebuild the paper from the ledger output
```

`tools/clean_checkout_gate.sh <this directory>` runs all of the above in a fresh copy, re-runs the registered
replication analysis from the released captures and checks that it reproduces `analysis.json` exactly.

## Replication

The preregistration (`prereg/PREREG_transport_replication.md`) fixed the templates, prompts, criteria, driver
(`e0/replicate_transport.py`) and analysis (`e0/analyse_transport_replication.py`) by hash before any fresh data were
collected. Re-collecting the captures needs a GPU and GPT-2 small (`python3 e0/replicate_transport.py --mode pilot`,
then `--mode fresh`; both frozen scripts write under the directory named by `OUT` at their top); the analysis runs
on CPU.
