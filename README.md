# 20S Proteasome Inhibitor Analysis

Exploratory cheminformatics analysis of small-molecule inhibitors of the **20S proteasome**, built on bioactivity data exported from [ChEMBL](https://www.ebi.ac.uk/chembl/). The notebook cleans the raw activity data, normalizes potency values to pIC50, labels compounds by potency, and explores the chemical space through drug-likeness scoring, property distributions, and Murcko scaffold analysis.

## Overview

The 20S proteasome is the catalytic core of the proteasome complex and a validated drug target (e.g. bortezomib, carfilzomib in multiple myeloma). This project takes a set of measured inhibitors and asks a few practical questions:

- How potent are the known compounds, and where do they sit on a pIC50 scale?
- What separates the active compounds from the inactive ones (molecular weight, lipophilicity)?
- How drug-like are the most potent hits?
- What core scaffolds show up most often among the active compounds?

It is a single, self-contained analysis notebook intended for exploration rather than a production pipeline.

## Workflow

The notebook (`20SProteasome.ipynb`) runs top to bottom through the following steps:

1. **Load & clean** — Reads `20Sproteasome.csv` (semicolon-separated), lowercases and normalizes column names.
2. **Filter to IC50** — Keeps only rows where `standard_type == "IC50"`, the dataset's most common measurement type.
3. **Normalize units & compute pIC50** — Converts µM values to nM, then converts IC50 (nM) to pIC50 using `pIC50 = -log10(IC50 × 10⁻⁹)`.
4. **Label compounds** — Flags each compound as `active` (pIC50 ≥ 7.0) and `potent` (pIC50 ≥ 9.0).
5. **Drug-likeness (QED)** — Parses SMILES into RDKit molecules and computes the QED score for the potent subset.
6. **Visualize** — pIC50 distribution histograms, active-vs-inactive comparisons, molecular weight and AlogP boxplots, and a property correlation heatmap.
7. **Scaffold analysis** — Extracts Bemis–Murcko scaffolds from the active compounds and renders the six most frequent cores as a molecule grid image.

## Requirements

- Python 3.13 (any recent Python 3 should work)
- Jupyter (Notebook or Lab)

Python packages:

```bash
pip install pandas numpy matplotlib seaborn rdkit jupyter
```

> Note: the package is `rdkit` on modern PyPI. If you hit installation issues, `conda install -c conda-forge rdkit` is a reliable alternative.

## Data

The notebook expects a file named **`20Sproteasome.csv`** in the same directory, using **`;`** as the separator. This is a ChEMBL bioactivity export and includes columns such as:

`molecule_chembl_id`, `molecule_name`, `molecular_weight`, `#ro5_violations`, `alogp`, `smiles`, `standard_type`, `standard_relation`, `standard_value`, `standard_units`, and assay/document metadata.

The CSV is **not included** in this repository. To reproduce the analysis, download a 20S proteasome activity set from ChEMBL and save it with the expected filename, or update the path in the first cell.

## Usage

```bash
jupyter lab 20SProteasome.ipynb   # or: jupyter notebook
```

Run the cells in order. Make sure `20Sproteasome.csv` is present before running the first cell.

## Key definitions

| Term | Definition |
|------|------------|
| **pIC50** | Negative log of molar IC50; higher means more potent. |
| **active** | pIC50 ≥ 7.0 (IC50 ≤ 100 nM). |
| **potent** | pIC50 ≥ 9.0 (IC50 ≤ 1 nM). |
| **QED** | Quantitative Estimate of Drug-likeness (0–1, higher is more drug-like). |
| **Murcko scaffold** | The core ring system + linkers of a molecule, with side chains removed. |

## Notes & caveats

- Potency thresholds (7.0 and 9.0) are conventional but arbitrary cutoffs; adjust to suit your use case.
- pIC50 values above 12 are dropped as likely outliers/artifacts before plotting.
- The dataset mixes measurement types; only IC50 records are analyzed here. Other endpoints (Ki, EC50, % inhibition, etc.) are discarded.
- This is descriptive exploratory analysis, not a predictive model.

## License

No license specified. Add one (e.g. MIT) if you intend to share or reuse this work.
