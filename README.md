# Collagen Hydrogel Rheology Data Curation and Analysis

[![Dataset DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.17413651.svg)](https://doi.org/10.5281/zenodo.17413651)

A reproducible Python workflow for auditing, structuring, validating and exploring rheology data reported in bovine-collagen hydrogel workbooks. The project applies materials-informatics practices to experimental data: it makes provenance, schemas, quality-control decisions and analysis reproducible while keeping conclusions within the limits of the source records.

## Project status

Stages 01–04 are complete. Stage 04 passed `Kernel → Restart & Run All`; its executed notebook was reviewed in the rendered report.

| Stage | Notebook | Status | Purpose |
|---|---|---|---|
| 01 — Source-data audit | [`01_data_audit.ipynb`](notebooks/01_data_audit.ipynb) | Complete | Audit workbook provenance, layout, unit labels and quality-control findings. |
| 02 — Schema design | [`02_schema_design.ipynb`](notebooks/02_schema_design.ipynb) | Complete | Define experiment and measurement records, identifiers, units and missing-value policies. |
| 03 — Measurement extraction and validation | [`03_measurement_extraction_validation.ipynb`](notebooks/03_measurement_extraction_validation.ipynb) | Complete | Extract source values into a canonical schema and validate IDs, rows, units, missingness and QC linkage. |
| 04 — Exploratory rheology analysis | [`04_exploratory_rheology_analysis.ipynb`](notebooks/04_exploratory_rheology_analysis.ipynb) | Complete | Explore frequency- and time-sweep patterns with experiment-level replication and conservative interpretation. |

## Quick navigation

### Notebooks

- [Stage 01 — Source-data audit](notebooks/01_data_audit.ipynb)
- [Stage 02 — Schema design](notebooks/02_schema_design.ipynb)
- [Stage 03 — Measurement extraction and validation](notebooks/03_measurement_extraction_validation.ipynb)
- [Stage 04 — Exploratory rheology analysis](notebooks/04_exploratory_rheology_analysis.ipynb)

### Rendered reports

These links open the rendered HTML reports directly on GitHub Pages:

- [Stage 01 — Data Audit Report](https://tehsongxuan.github.io/collagen-hydrogel-rheology-data-curation/01_data_audit_report.html)
- [Stage 02 — Schema Design Report](https://tehsongxuan.github.io/collagen-hydrogel-rheology-data-curation/02_schema_design.html)
- [Stage 03 — Measurement Extraction and Validation Report](https://tehsongxuan.github.io/collagen-hydrogel-rheology-data-curation/03_measurement_extraction_validation.html)
- [Stage 04 — Exploratory Rheology Analysis Report](https://tehsongxuan.github.io/collagen-hydrogel-rheology-data-curation/04_exploratory_rheology_analysis.html)

### Metadata and quality-control records

- [Experiment register](metadata/experiment_register.csv)
- [Experiment data dictionary](metadata/experiment_data_dictionary.csv)
- [Measurement data dictionary](metadata/measurement_data_dictionary.csv)
- [Data quality issue log](data/quality_control/data_quality_issue_log.csv)
- [Unit-label mapping audit](data/quality_control/unit_label_mapping_audit.csv)
- [Source manifest](metadata/source_manifest.csv)
- [Extracted workbook inventory](metadata/extracted_file_inventory.csv)
- [Workbook structure inventory](metadata/workbook_structure_inventory.csv)
- [Workbook schema QC](metadata/workbook_schema_qc.csv)

The links above point to repository documentation and metadata. The source archive, original Excel workbooks and row-level processed measurement tables are not linked or distributed here.

## Dataset and scientific context

The source dataset is the Zenodo record [PID2020-113790RB-I00 data set: hydrogel viscoelastic properties from rheology tests](https://doi.org/10.5281/zenodo.17413651), associated with the article [Quantitative atlas of collagen hydrogels reveals mesenchymal cancer cell traction adaptation to the matrix nanoarchitecture](https://doi.org/10.1016/j.actbio.2024.07.002).

The project concerns bovine-collagen hydrogels at reported concentrations of 0.8, 1.5 and 2.3 mg/mL. The workbooks contain frequency-sweep and time-sweep measurements, with a nominal temperature of 37 °C.

| Test type | Source workbooks | Measurements | Points per experiment |
|---|---:|---:|---:|
| Frequency sweep | 10 | 210 | 21 |
| Time sweep | 10 | 450 | 45 |
| **Total** | **20** | **660** | — |

The associated research studies hydrogel properties and biological interactions using several techniques. This repository focuses on the rheology workbooks and the reproducibility of their curation and analysis; it does not reproduce the microscopy or cell-traction analyses.

## Polymer and biomaterials informatics relevance

This project is a **materials-data and workflow-engineering project for a collagen biomaterial**. It focuses on making experimental rheology data usable and traceable before attempting cross-study synthesis or modelling. Collagen is a biological polymer, and the measured hydrogel response is shaped by material composition and experimental conditions; the workbook values therefore need their experimental context and provenance alongside them.

The four stages create that foundation:

1. **Audit the source records.** Identify how the Excel files encode measurements, units, formulas and unusual values before transforming them.
2. **Represent the experiment explicitly.** Separate the experiment register, repeated measurement points and QC findings, with defined fields and missing-value rules.
3. **Create validated machine-readable measurements.** Keep each processed value traceable to its source workbook and Excel row, and validate identifiers, units, expected row counts and structural missingness.
4. **Explore material response at the correct experimental level.** Compare storage modulus (`G′`), loss modulus (`G″`) and derived loss factor across angular frequency; examine time-sweep measurements in source order; and show experiment-to-experiment variation without treating repeated points as independent samples.

This is **not a polymer-property prediction or machine-learning study**. It does not use polymer structures, molecular descriptors or a predictive model. Its contribution is a reproducible data pipeline and a scientifically cautious first analysis of a biomaterial's rheology. A well-documented schema could support future integration with formulation, processing, imaging or biological-response data if those measurements and linkages are available. This project does not invent those links or claim that they are already present.

## Workflow and results

### Stage 01 — Source-data audit

The audit records source provenance, inventories workbook and worksheet structures, inspects headers and units, and investigates unusual columns or measurements before transformation. It confirmed eight QC findings:

- One frequency-sweep workbook contains an undocumented formula column. It is excluded from the canonical dataset, while the source workbook is preserved.
- One frequency-sweep observation has `G′ = 0 Pa` at 0.628 rad/s. It is retained and traceable; no replacement value is imposed.
- Six unit-label inconsistencies are character-encoding differences. Approved label mappings standardise the labels without changing numerical measurements.

### Stage 02 — Rheology schema design

The schema distinguishes source workbooks and experiments from the repeated measurement points within each experiment. The experiment register and data dictionaries describe identifiers, units, field meanings and missing-value rules. A replicate label in a filename or register is not treated as proof of physical sample identity.

### Stage 03 — Controlled measurement extraction and validation

The extraction produced two local standardised tables with the same 11-field measurement schema:

| Local processed table | Rows | Experiments |
|---|---:|---:|
| Frequency sweep | 210 | 10 |
| Time sweep | 450 | 10 |
| **Total** | **660** | **20** |

The completed checks found no duplicate measurement IDs, duplicate experiment/source-row pairs, invalid source rows, invalid measurement points, non-numeric populated rheology values or workbook row-count mismatches. The documented zero storage modulus remains unchanged and linked to its QC record. The three time-sweep-only fields are structurally missing in all frequency-sweep rows; they are not filled with zero.

The processed tables are local analysis inputs and are excluded from the public repository under `.gitignore`.

### Stage 04 — Exploratory rheology analysis

Stage 04 loads the validated tables and experiment register, checks schemas, coverage, IDs and missingness, and reviews the QC log before producing descriptive summaries and plots.

**Frequency sweeps.** The 10 experiments share a 21-point angular-frequency grid from 0.628 to 62.8 rad/s. The notebook plots source-reported storage modulus (`G′`) and loss modulus (`G″`) by concentration, and derives the dimensionless loss factor `tan δ = G″/G′`. At the retained `G′ = 0` observation, `tan δ` is undefined and represented as missing; the source value is preserved. A comparison table summarises experiment-level observations at the shared frequency of 6.28 rad/s.

**Time sweeps.** The 10 experiments have 45 measurement points each and a constant recorded angular frequency of 6.28 rad/s. The notebook plots values against `measurement_point`, which is source order. It does not convert measurement order into elapsed time or calculate rates. Endpoint differences compare points 1 and 45 within each experiment and are explicitly not time-normalised.

**Replicates and concentration.** Each test type has four registered experiments at 0.8 mg/mL and three experiments at each of 1.5 and 2.3 mg/mL. Individual experiment traces remain visible, with medians and observed ranges used only as descriptive summaries. The shaded bands show the minimum-to-maximum range across experiments at each measurement point; they are generated by the analysis and are not shaded regions copied from the original Excel files. Measurement points within an experiment are repeated observations, not independent material replicates.

**A high time-sweep observation.** At 0.8 mg/mL, replicate 1, measurement point 20 (source Excel row 25), the processed table reports a complex viscosity of 10.9 Pa·s and shear stress of 0.691 Pa. This observation stretches the range band at that point while the median line remains near the other experiments. The Stage 03 QC log does not flag it. It is retained as supplied and should be checked against the source workbook before any correction is considered.

The concentration comparisons are observational. The dataset does not establish that concentration alone caused the observed differences. No formal hypothesis tests are performed.

## Reproducibility

Use the project environment in [`environment.yml`](environment.yml) and the Jupyter kernel named `Python (hydrogel-rheology)`. From the project folder, open the Stage 04 notebook and run:

```text
Kernel → Restart & Run All
```

The notebooks use explicit imports and project-relative file discovery. Stage 04 requires the two Stage 03 processed tables locally, alongside the experiment register, measurement data dictionary and QC issue log. It does not depend on hidden variables from a previous interactive session.

## Data rights and repository safeguards

The Zenodo source archive is publicly accessible, but public access alone does not establish permission to redistribute the archive or its measurements. This repository links to the authoritative Zenodo record and does not include:

- `Reología.rar`;
- the original 20 Excel workbooks; or
- `frequency_sweep_standardised.csv` and `time_sweep_standardised.csv`.

The processed measurement tables are used locally. Repository safeguards exclude `data/raw/`, `data/interim/` and `data/processed/` from Git tracking. Only publish row-level source or processed measurements if their reuse and publication rights are independently confirmed.

## Scientific boundaries

- **Physical sample linkage is unresolved.** Frequency- and time-sweep experiments with matching concentration and replicate labels are not assumed to use the same physical hydrogel specimen.
- **Measurement order is not elapsed time.** The time-sweep files do not provide a confirmed time interval; no elapsed-time axis or rate is inferred.
- **Repeated measurements are nested within experiments.** The 660 rows are not treated as 660 independent hydrogel samples.
- **The zero storage-modulus value is retained.** Derived calculations handle division by zero explicitly.
- **QC records do not establish the physical cause of every unusual value.** The Stage 04 high time-sweep observation is described and traced, not silently removed or altered.
- **Experimental metadata are limited.** The available files do not fully describe preparation history, physical sample linkage or all factors that could influence rheology.

## Repository structure

```text
collagen-hydrogel-rheology-data-curation/
├── README.md
├── environment.yml
├── .gitignore
├── notebooks/
│   ├── 01_data_audit.ipynb
│   ├── 02_schema_design.ipynb
│   ├── 03_measurement_extraction_validation.ipynb
│   └── 04_exploratory_rheology_analysis.ipynb
├── docs/
│   ├── 01_data_audit_report.html
│   ├── 02_schema_design.html
│   ├── 03_measurement_extraction_validation.html
│   └── 04_exploratory_rheology_analysis.html
├── metadata/
│   ├── source_manifest.csv
│   ├── extracted_file_inventory.csv
│   ├── workbook_structure_inventory.csv
│   ├── workbook_schema_qc.csv
│   ├── experiment_register.csv
│   ├── experiment_data_dictionary.csv
│   └── measurement_data_dictionary.csv
└── data/
    ├── raw/                 # local; excluded from Git
    ├── interim/             # local; excluded from Git
    ├── processed/           # local; excluded from Git
    └── quality_control/
        ├── data_quality_issue_log.csv
        └── unit_label_mapping_audit.csv
```

## Citation and data attribution

Please cite the source dataset and associated article using their records:

- Dataset: [Zenodo DOI 10.5281/zenodo.17413651](https://doi.org/10.5281/zenodo.17413651)
- Related article: [DOI 10.1016/j.actbio.2024.07.002](https://doi.org/10.1016/j.actbio.2024.07.002)

The experimental measurements remain attributable to their original creators. Consult the Zenodo record and publication for source provenance and reuse conditions.

