# Open Materials Data Pipeline for Hydrogel Bioink Design

A reproducible materials-informatics project for curating hydrogel bioink data, preserving experimental context, and deciding whether open records support reliable analysis or machine learning.

**Status:** Proposed. Source feasibility and data audit come first. No analysis or model results are claimed until the source records have been inspected and the planned checks have been completed.

## Quick navigation

### Phase 0 — Scope and source registry

- [Jupyter notebook — scope and source registry](https://github.com/tehsongxuan/hydrogel-bioink-data-curation/blob/main/notebooks/00_scope_and_source_registry.ipynb)
- [HTML export — scope and source registry](https://github.com/tehsongxuan/hydrogel-bioink-data-curation/blob/main/reports/00_scope_and_source_registry.html)
## Project overview

Materials research creates records at several levels: ingredients, formulations, processing steps, physical samples, measurements, printing runs, constructs, and images. Those records may live in different tables, instrument exports, archives, and image folders. They are scientifically useful together only when their meanings, units, and relationships are clear.

This project uses an open hydrogel bioink dataset as its experimental case study and uses three complementary materials-informatics resources to practise computational data ingestion, model evaluation, and schema design. The central question is:

> Can open materials records be organized into a traceable workflow that connects documented bioink formulations and processing conditions with rheological measurements and printed-construct information, while preserving the limits of the source data?

The workflow is deliberately data-first: verify sources; audit files; define the data model; validate and standardize values; examine comparable observations; then decide whether prediction is justified. Machine learning is a possible final step, not an assumption made at the beginning.

## Problem statement

The primary experimental source concerns phenol-modified alginate (ALG-Ph) and hyaluronic-acid (HA-Ph) hydrogel inks used in 3D bioprinting. The Zenodo record describes shear-dependent viscosity, storage modulus (`G′`), loss modulus (`G″`), printing metadata, indexed construct images, and files associated with the source authors’ analyses. The planned version is `v0.0.2`, to be checked again before processing. [Zenodo record](https://zenodo.org/records/19602891) · [Dataset DOI](https://doi.org/10.5281/zenodo.19602891)

A rheology file may contain many points from one experiment. Those points are repeated observations, not separate formulations. An image index may describe an image or construct, not an independent material sample. A formulation label may not uniquely identify a physical sample. A successful software join between two tables does not prove they describe the same experiment.

The project therefore asks what each row and file represents, which material and process it describes, and whether a link is supported by source identifiers or documentation. Unexplained rows, missing information, and unmatched records will be recorded rather than silently removed, filled with zero, or forced into a relationship. Keeping these records traceable preserves evidence for review and prevents the curated dataset from appearing more complete or certain than its source.

## Polymer and hydrogel context

A hydrogel is a water-rich polymer network. A bioink is a formulation intended for bioprinting; it may be hydrogel-based and may contain cells, but the word alone does not prove that a dataset includes cells or measures biological performance. The Zenodo case study describes acellular hydrogel printing, so this project will not infer cell viability or tissue response from its rheology or images.

Polymer behaviour depends on more than polymer identity. Concentration, molecular characteristics, chemical modification, additives, crosslinking, processing history, temperature, and test conditions can matter. The source may not report every factor. The project will preserve this incompleteness rather than inventing chemical descriptors or sample conditions.

Rheology describes flow and deformation. Viscosity measures resistance to flow. If viscosity decreases as shear rate increases, the material exhibits shear-thinning behaviour. This can be relevant to extrusion through a printing nozzle, but shear thinning alone does not prove printability. Recovery after extrusion, crosslinking kinetics, nozzle geometry, speed, pressure, and other processing conditions may also matter.

Hydrogels are viscoelastic: their response can include elastic energy storage and viscous energy dissipation. `G′` describes the elastic contribution and `G″` the viscous contribution under the specified oscillatory test conditions. These values should be interpreted alongside information such as frequency, strain, temperature, and sample history. A difference between formulations cannot be attributed to chemistry alone if test conditions or sample identity are uncertain.

The scientific chain of interest is:

**ingredients and polymer chemistry → formulation → processing or crosslinking → measured rheology → printing conditions → construct or documented outcome**

Each connection must be supported by the source. If an identifier or documented convention does not establish a link, it remains unresolved.

## Resources and their roles

The platforms provide different evidence types. They will share data-management and provenance principles, but they will not be merged into one artificial training dataset.

| Resource | Role in this project | Boundary |
|---|---|---|
| [Zenodo bioink dataset](https://zenodo.org/records/19602891) | Primary experimental case study for rheology, printing metadata, and indexed construct images. | Only source-supported links and outcomes will be used. Shape labels and images are not automatically validated print-quality scores. |
| [Materials Project](https://next-gen.materialsproject.org/) | Practise reproducible API retrieval of selected computational materials records, retaining material IDs, query details, returned fields, and calculation provenance. | Its inorganic computational records are not hydrogel rheology measurements and remain in a separate branch. |
| [Matbench](https://matbench.materialsproject.org/) | Reproduce a defined materials-property benchmark to practise controlled model evaluation. The proposed `matbench_dielectric` task predicts refractive index from inorganic crystal structure. | It is not a polymer or hydrogel benchmark; its scores cannot validate a hydrogel model. |
| [Citrine / GEMD](https://citrineinformatics.github.io/gemd-docs/) | Use materials-process-measurement concepts to guide schema design and represent experimental history. | GEMD is a data model, not a hydrogel dataset. Citrination access will be checked before depending on any data there. |

Materials Project provides a Python API client. API retrieval requires an API key, and a query should specify fields that are actually available and needed. The query, selected fields, and retrieval date form part of the provenance record. [API setup](https://docs.materialsproject.org/downloading-data/using-the-api/getting-started) · [Query guidance](https://docs.materialsproject.org/downloading-data/using-the-api/querying-data)

Matbench provides curated tasks for materials-property machine learning. Reproducing its task protocol is a separate exercise in controlled evaluation; it does not transfer a model’s validity from inorganic crystals to polymer networks. [Matbench documentation](https://docs.materialsproject.org/services/ml-and-ai-applications/matbench) · [Matbench repository](https://github.com/materialsproject/matbench)

GEMD helps represent ingredients, materials, processes, measurements, conditions, and results as a connected material history. This can guide the experimental schema even if no Citrination dataset is used. Dataset availability and terms will be checked before any Citrination records are treated as a project input. [GEMD overview](https://citrineinformatics.github.io/gemd-docs/high-level-overview/) · [Citrine Python data model](https://citrineinformatics.github.io/citrine-python/getting_started/data_model.html)

## Linked workflow

Each phase has an input, checks, output, and exit condition. The output from one phase becomes a controlled input to the next. This prevents later plots or models from depending on undocumented joins or guessed unit conversions.

### Phase 0 — Define scope and register sources

**Question:** What does each source contain, and what conclusions can it support?

Record each source URL or DOI, dataset/API identifier, version, retrieval date, access and reuse terms, source type, citation, and local file details or checksum where practical. Confirm the Zenodo version and files; select a bounded Materials Project query; identify the exact Matbench task and protocol; and distinguish Citrine/GEMD documentation from access to Citrination datasets.

**Data-science concept:** provenance starts at acquisition. A property value is not fully described without its source, unit, experiment or material identifier, and processing history.

**Output and handoff:** a project-scope note and source manifest. This manifest controls what is audited in Phase 1 and records the project’s non-goals. Exit when each source and version has a defined, limited purpose.

### Phase 1 — Audit the Zenodo source

**Question:** What files, fields, units, identifiers, and relationships are actually present?

Inventory archives, filenames, formats, tables, columns, row counts, data types, units, labels, IDs, missing values, duplicates, and image files. Compare image names with the image index and read the source documentation for stated record relationships. Separate facts from assumptions: for example, a `shape` label is not automatically a numerical fidelity score.

Log unexpected or unexplained rows, inconsistent labels or units, missing identifiers, and duplicate-looking records. Preserve the original source and record the evidence reviewed. An unusual value is a reason to inspect context, not automatic evidence for deleting or correcting it.

**Data-science concept:** profiling exposes structural problems before transformations or modelling. **Polymer concept:** concentration, crosslinking, and measurement conditions can change the meaning of a rheology value, so the audit checks whether these are measured, reported, or absent.

**Output and handoff:** file and field inventories plus a quality-control issue log. The observed fields and keys—not assumed ones—define Phase 2. Exit when source structure and uncertainty are documented well enough to design the schema.

### Phase 2 — Design the experimental schema

**Question:** What real-world entity does one row represent in each table?

Separate formulation records, physical samples if identified, rheology experiments, rheology measurement points, printing events, constructs, and images. One experiment may produce many rheology points; one run may have several images. These are different levels of observation. A replicate in a filename is not proof of physical sample identity unless the source says so.

Define field names, meanings, types, units, allowed values, missing-value semantics, identifiers, and source mapping. A formulation table might hold composition; an experiment table holds test type and conditions; a measurement table holds point-level values. A printing event and image index should be separate if the source supports them. The schema must not invent fields just because they would be useful for modelling.

GEMD concepts can be used as a reference: ingredients enter processes; processes produce or modify materials; measurements characterize materials under conditions and yield results. Intended specifications should be distinguished from what actually occurred in an experimental run.

**Data-science concept:** schema design makes data granularity and relationships explicit. **Polymer concept:** separating material identity, formulation, process, and measurement context keeps the property attached to the conditions that produced it.

**Output and handoff:** schema map, data dictionary, identifier rules, and source-to-schema mapping. These become validation rules for Phase 3. Exit when every field has a definition and each relationship is supported or marked unresolved.

### Phase 3 — Standardize values and run quality checks

**Question:** Can equivalent information be represented consistently without losing the original evidence?

Standardize field names, labels, types, and units only where meaning is clear. Keep original values and units alongside standardized values, units, and conversion rules. Do not convert an ambiguous unit by guessing. Distinguish zero from not measured, not reported, not applicable, unknown, and unavailable.

Validate required fields, ID uniqueness, foreign keys, duplicate records, numeric types, row counts, unit consistency, and scientifically justified ranges. Treat range checks as flags for review, not automatic correction. Keep a decision trail in the issue log.

**Data-science concept:** executable checks make assumptions repeatable and expose violations. Passing checks does not prove that a scientific relationship is true. **Polymer concept:** `G′` in pascals and viscosity in pascal-seconds describe different properties; even equal units do not ensure comparability if frequency or temperature differs.

**Output and handoff:** standardized tables, validation summary, and transformation lineage. Their verified identifiers support Phase 4. Exit when transformations are reproducible and unresolved problems remain visible.

### Phase 4 — Assess record linkage

**Question:** Which formulation, rheology, printing, and image records can genuinely be connected?

For each candidate join, record the key and its source. Check match rates, unmatched rows, duplicated keys, and one-to-one or one-to-many relationships. Some one-to-many links are expected: a rheology experiment can contain many points. A join that runs successfully in software does not prove that the records describe the same physical formulation or run.

Classify links as confirmed by an explicit ID, documented by a source convention, possible, or unresolved. Similar labels alone may suggest a match but do not confirm one. Report ambiguous records rather than forcing them into a combined table.

**Data-science concept:** this is entity resolution and join validation. It also determines the independent experimental unit for later summaries and model splits. **Polymer concept:** formulation-to-rheology linkage is necessary before a measured response can be interpreted as formulation-dependent.

**Output and handoff:** linkage map, join-coverage report, and unresolved-link register. Only supported links move into Phase 5. Exit when each comparison has a defined population and evidence basis.

### Phase 5 — Explore rheology and printing context

**Question:** What patterns occur in validated, comparable measurements?

Plot shear-dependent viscosity against shear rate and describe whether the observed curve is consistent with shear-thinning over the measured range. Plot `G′` and `G″` against the source’s measured variable when test conditions allow comparison. Keep individual experiments visible and group summaries at the correct level; do not treat curve points as independent samples.

A derived value such as `tan δ = G″/G′` should be calculated only when both moduli are available and compatible. Record the formula, retain source values, and leave the result undefined when the denominator is zero. Do not alter a measurement to make a derived column complete.

Explore image and printing metadata only if source-defined identifiers link them to rheology or formulation. Define what each image label represents. A requested shape, image category, and validated print-quality outcome are not interchangeable. Describe associations rather than causal effects; unrecorded conditions may explain observed differences.

**Data-science concept:** exploratory analysis describes distributions, coverage, variation, and possible relationships before inference. **Polymer concept:** viscosity, `G′`, and `G″` describe different aspects of flow and viscoelasticity; their printing relevance depends on test and process conditions.

**Output and handoff:** reproducible figures, tables, and a written interpretation with comparability limits. These inform model readiness in Phase 10. Exit when each plot states the source population, unit, condition, and experimental unit.

### Phase 6 — Ingest a bounded Materials Project extract

**Question:** Can computational materials records be retrieved and traced reproducibly?

Use the official `mp-api` client for a bounded query. Retain material IDs, query parameters, requested fields, retrieval date, and available calculation provenance. Verify actual returned fields rather than assuming every material has every property. Store API credentials outside version control.

**Data-science concept:** a saved query and source ID make API acquisition reproducible and easier to refresh. **Materials concept:** a calculated inorganic property is a different evidence type from an experimental hydrogel measurement; each keeps its method and provenance.

**Output and handoff:** a documented extract and executable query notebook. This is a separate test case for the metadata design in Phase 9, not a feature table for the hydrogel model. Exit when a reviewer can reproduce the query and identify the origin of each returned value.

### Phase 7 — Reproduce a Matbench task

**Question:** Can a materials-property model be evaluated under a defined protocol?

Reproduce the selected `matbench_dielectric` task using the documented data, folds, and metrics. Its target is refractive index from inorganic crystal structure; despite the name, it is not a hydrogel or polymer dielectric task. Start with a transparent baseline and report features, model, evaluation protocol, and error metrics. Do not tune on the benchmark test data and still call the result a faithful reproduction.

**Data-science concept:** baselines, fixed evaluation procedures, metrics, and leakage checks make results comparable. **Materials-informatics concept:** crystal-structure features do not substitute for polymer chemistry or formulation descriptors.

**Output and handoff:** a reproducible benchmark notebook and score interpretation. The evaluation practices inform Phase 10, but benchmark performance is not evidence that the hydrogel data can support prediction. Exit when task identity, target, inputs, protocol, and limitations are explicit.

### Phase 8 — Map the schema to GEMD

**Question:** Can the documented experimental history be represented as materials, ingredients, processes, measurements, and results?

Map source-supported entities to GEMD concepts. An ingredient may enter a formulation process; a process may create or modify a material; a measurement may be performed on that material under specified conditions. Printing and imaging can be separate processes or records if the source documents them.

GEMD distinguishes intended specifications from experimental runs. A planned concentration is not necessarily the concentration of a prepared and measured sample. A more expressive schema cannot fill a missing sample ID or create evidence absent from the dataset.

**Data-science concept:** knowledge representation captures relationships and constraints that would otherwise remain implicit in column names. **Polymer concept:** formulation and process history can be essential to interpreting a hydrogel’s measured behaviour.

**Output and handoff:** source-to-GEMD mapping, a small example representation, and a list of unsupported fields. This informs common metadata in Phase 9. Exit when mappings do not imply unsupported physical relationships.

### Phase 9 — Apply shared provenance rules

**Question:** What metadata must remain attached so records retain their origin and meaning?

Across the separate source-specific tables, retain source platform and ID, version, source type, material system, property name, original value and unit, standardized value and unit if justified, method, conditions, retrieval date, processing history, and QC status. Distinguish source-reported, transformed, derived, calculated, predicted, and benchmark values.

Record code version, environment, API query, and file checksum where practical. A shared provenance layer supports interoperability; it does not make records scientifically equivalent.

**Data-science concept:** lineage supports audit, debugging, reproducibility, and version updates. **Materials-informatics concept:** consistent metadata can support data exchange while preserving differences among polymer experiments, inorganic calculations, and benchmark tasks.

**Output and handoff:** provenance fields and reusable validation logic. These records allow the final readiness review to rely on traceable evidence. Exit when every processed value can be traced to a source and transformation history.

### Phase 10 — Decide whether hydrogel prediction is justified

**Question:** Do the curated experimental records support a meaningful, independent prediction task?

Define the target before training. “Printability” is not a target until an outcome and measurement method are specified. A source-defined completeness label, objectively calculated shape deviation, or measured rheological property might be candidates if available and consistent. A shape category should not be relabelled as quality without evidence.

Count independent formulations, samples, or printing runs—not just measurement points or image files. Group dependent records together during validation. Check whether predictors are available at the intended prediction time: post-print images cannot serve as predictors for a pre-print decision. Look for target-derived features, missingness, class imbalance, and incompatible measurement conditions.

Possible outcomes are: a defined task is supported; only a restricted task is supportable; or the data support curation and exploration but not reliable prediction. The last outcome identifies which identifiers, measurements, or independent experiments a future dataset would need. If modelling is justified, start with a baseline, grouped validation, error analysis, and a stated domain of applicability.

**Data-science concept:** model readiness depends on target definition, sample size, predictors, leakage-safe validation, and coverage. **Polymer concept:** models need formulation, process, and measurement context; they cannot learn unrecorded chemistry or conditions.

**Output:** a model-readiness report and, only if supported, a limited baseline model. Exit with a defensible statement of what the data can and cannot predict.

## Concepts that connect the phases

- **Data granularity:** define whether a row represents a formulation, sample, experiment, point, run, construct, or image. This guides schema design, summaries, joins, and validation splits.
- **Provenance and lineage:** preserve source identifiers, versions, units, query details, and transformations. This begins in Phase 0 and remains attached through every later output.
- **Missingness semantics:** distinguish zero from unknown, not measured, not reported, not applicable, or unavailable. This affects QC and model feature selection.
- **Standardization:** normalize names and units only with documented rules; keep raw source values available for review.
- **Experimental hierarchy:** points from one curve and images from one run are nested records, not necessarily independent materials. This matters for uncertainty and model evaluation.
- **Leakage control:** related records must not be split carelessly between training and testing. The split should reflect the intended future prediction and the true independent unit.
- **Exploration versus causation:** patterns can motivate hypotheses but do not isolate causal effects when formulation, process, and conditions vary together.

## Planned outputs

Subject to feasibility, the repository may contain:

- Versioned source manifest and file inventory
- Field inventory, data dictionary, and schema map
- Quality-control issue log and validation summary
- Standardized tables with source-to-output traceability
- Record-linkage report with match coverage and unresolved links
- Reproducible notebooks for audit, validation, and exploratory analysis
- Figures and summaries based on validated, comparable observations
- A bounded Materials Project API-ingestion example
- A Matbench reproduction with documented protocol and metrics
- A GEMD mapping and example representation
- A hydrogel model-readiness report and, only if appropriate, a baseline model

Large source archives and API credentials will not be committed by default. The repository will provide source links, retrieval instructions, environment details, and code for reproducing derived outputs, subject to each provider’s terms.

### Proposed repository structure

```text
open-materials-hydrogel-bioink-pipeline/
├── README.md
├── environment.yml
├── .gitignore
├── DATA_SOURCES.md
├── DATA_DICTIONARY.md
├── PROVENANCE.md
├── notebooks/
│   ├── 00_scope_and_source_registry.ipynb
│   ├── 01_zenodo_source_audit.ipynb
│   ├── 02_schema_and_quality_control.ipynb
│   ├── 03_record_linkage.ipynb
│   ├── 04_hydrogel_rheology_analysis.ipynb
│   ├── 05_materials_project_api_ingestion.ipynb
│   ├── 06_matbench_reproduction.ipynb
│   ├── 07_gemd_mapping.ipynb
│   └── 08_model_readiness.ipynb
├── src/
│   ├── ingestion/
│   ├── validation/
│   └── analysis/
├── data/
│   └── metadata/
├── reports/
│   ├── figures/
│   └── tables/
└── docs/
    ├── schema_design.md
    ├── quality_control_rules.md
    └── scientific_boundaries.md
```

This structure is provisional. It will be updated to reflect work actually implemented; listing a notebook here does not claim that it already exists.

## Scientific boundaries

- Materials Project records remain computational and separate from hydrogel experimental data.
- Matbench scores demonstrate benchmark practice, not hydrogel-model validity.
- GEMD guides representation but cannot supply missing source evidence.
- No physical sample or experiment link will be invented.
- Missing values will not be replaced by zero without source justification.
- Unit conversions and derived variables will be traceable to original values.
- Repeated rheology points will not be counted as independent formulations.
- Rheology and images alone will not be used to infer cell viability, tissue function, or clinical performance.
- Observational patterns will not be described as causal effects of polymer chemistry or concentration.
- Modelling will proceed only if the target, independent sample size, record linkage, predictors, and evaluation design support it.
- The project does not claim new polymer synthesis, instrument or sensor development, robotics, or biological testing.

These boundaries shape the source audit, schema, joins, plots, and modelling decision from the beginning.

## Success criteria

The workflow should be able to answer, with evidence: Where did each value originate? What does each row represent? Which values are experimental, computational, derived, or benchmark data? What material, formulation, process, and measurement conditions are documented? Which records can be linked, and which relationships remain uncertain? Which transformations and QC decisions were applied? Which observations are independent? What comparisons are scientifically appropriate? Is there a meaningful, leakage-safe prediction target? What does the dataset support, and what remains unknown?

A finding that the source data are not yet ready for hydrogel machine learning can still be a useful result. It identifies the data gaps that future experiments or repositories need to address.

## Current status and next step

**Planning and source-feasibility review.** Begin with Phase 0: confirm the Zenodo version and reuse conditions, register the sources, then inspect the primary archive. Finalize the schema, joins, analysis, and any prediction task from the records actually found. Materials Project, Matbench, and GEMD remain separate extensions until the primary audit establishes a manageable scope.

## GitHub publication: Phase 0 checkpoint

The first publishable checkpoint consists of the project documentation, the Jupyter notebook that checks the working environment and expected project files, and an optional HTML rendering of that notebook. Add the files to the repository using the paths below so these links work on GitHub.

- **Jupyter notebook (executable Python):** [`00_scope_and_source_registry.ipynb`](notebooks/00_scope_and_source_registry.ipynb)
- **HTML notebook export (read-only view):** [`00_scope_and_source_registry.html`](reports/00_scope_and_source_registry.html)

The notebook is the editable, rerunnable source. The HTML file is a convenient browser-readable copy. Keep both links relative to the repository so they continue to work after the repository is renamed or moved.

### Files for the initial GitHub upload

- `README.md` — this project overview and workflow plan.
- `notebooks/00_scope_and_source_registry.ipynb` — the Phase 0 Jupyter notebook.
- `reports/00_scope_and_source_registry.html` — optional HTML export of the notebook.
- `.gitignore` — recommended to keep local datasets, notebook checkpoints, and environment-specific files out of version control.

Do not include the downloaded Zenodo archive or `image_index.csv` in this initial public upload while reuse and redistribution conditions are being checked. Keep those source files in the local project folder. Additional dataset archives are not required for this Phase 0 checkpoint and can be added to the local project later when downloaded and reviewed.

## Sources and technical documentation

### Experimental hydrogel bioink data

- [Zenodo dataset record](https://zenodo.org/records/19602891)
- [Dataset DOI](https://doi.org/10.5281/zenodo.19602891)
- [Associated publication](https://doi.org/10.1080/17452759.2026.2671497)
- [Authors’ source-code repository](https://github.com/KORINZ/generative-ai-bioprinting-framework)

### Materials Project

- [Materials Project home](https://next-gen.materialsproject.org/)
- [Materials Explorer](https://next-gen.materialsproject.org/materials)
- [General documentation](https://docs.materialsproject.org/)
- [API getting started](https://docs.materialsproject.org/downloading-data/using-the-api/getting-started)
- [API query guidance](https://docs.materialsproject.org/downloading-data/using-the-api/querying-data)
- [Python API client documentation](https://materialsproject.github.io/api/)
- [API client source code](https://github.com/materialsproject/api)

### Matbench and matminer

- [Matbench benchmark and leaderboard](https://matbench.materialsproject.org/)
- [Materials Project Matbench documentation](https://docs.materialsproject.org/services/ml-and-ai-applications/matbench)
- [Matbench source repository](https://github.com/materialsproject/matbench)
- [Matbench methodology paper](https://doi.org/10.1038/s41524-020-00406-3)
- [matminer documentation](https://hackingmaterials.lbl.gov/matminer/)
- [matminer dataset summary](https://hackingmaterials.lbl.gov/matminer/dataset_summary.html)

### Citrine, GEMD, and Citrination

- [Citrine Informatics](https://citrine.io/)
- [Citrine DataManager](https://citrine.io/platform/citrine-datamanager/)
- [GEMD documentation](https://citrineinformatics.github.io/gemd-docs/)
- [GEMD high-level overview](https://citrineinformatics.github.io/gemd-docs/high-level-overview/)
- [Citrine Python data-model overview](https://citrineinformatics.github.io/citrine-python/getting_started/data_model.html)
- [gemd-python source repository](https://github.com/CitrineInformatics/gemd-python)
- [Citrination](https://citrination.com/) — included as a platform reference; dataset availability and access will be verified before use.

