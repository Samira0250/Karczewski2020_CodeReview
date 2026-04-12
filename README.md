# Code Review: Karczewski et al. (2020)

**Paper:** Karczewski et al. (2020) — *The mutational constraint spectrum quantified from variation in 141,456 humans*

**Code repository:** https://github.com/broadinstitute/gnomad_lof

**Reviewer:** Samira Salimiiyan
**Course:** BIOL 7180 — Scripting for Biologists
**Institution:** Auburn University
**Review Date:** April 2026

---

## Summary

Karczewski et al. (2020) present gnomAD v2.1.1, aggregating exome and genome sequencing data from 141,456 individuals to quantify gene-level constraint against loss-of-function (LoF) and missense variation. The authors introduce three metrics — pLI, observed/expected LoF ratio (o/e), and missense Z-score — that have since become standard tools for prioritizing candidate disease genes and interpreting variant pathogenicity. As requested by the editor, my review below focuses on the code associated with this manuscript, available at https://github.com/broadinstitute/gnomad_lof.

**Overall reproducibility:** The repository provides partial reproducibility. Figure generation scripts work well with pre-computed data, but the core constraint calculation pipeline cannot be independently reproduced due to missing internal functions and infrastructure barriers detailed below.

---

## High-Level Suggestions

### 1. Provide a complete, standalone constraint calculator

The most critical gap in the repository is the absence of `fit_lof_model()`, the internal helper function called by `calculate_constraint()` in `constraint_functions.R`. Running this function produces the error:

```
could not find function 'fit_lof_model'
```

Without it, users cannot independently compute pLI or o/e LoF from raw variant data. The core Bayesian model appears to reside in an internal library not released publicly. Bundling this into a standalone, installable R package with minimal example input data would be the single highest-impact improvement.

### 2. Add a containerized environment (Docker or Conda)

The repository provides no `requirements.txt`, `environment.yml`, or `Dockerfile`. The Hail pipeline requires a specific version of Apache Spark and the `gnomad` Python package, both of which have breaking changes across versions. Without pinned dependencies, users cannot reliably reproduce the environment. A Conda environment file or Docker image would resolve this.

### 3. Reduce the infrastructure barrier with a small-scale tutorial

The full pipeline was run on Google Cloud with ~500 cores and >1 TB of input data. While this is unavoidable at gnomAD scale, a "Quick Start" tutorial showing how to calculate constraint for a single gene on a local machine (using the pre-computed expected counts) would make the repository substantially more accessible. Currently, most users are forced to use the pre-computed scores without any way to verify or extend the method.

---

## Specific Feedback

- **`constraint_functions.R`, line ~40:** `calculate_constraint()` calls `fit_lof_model()`, which is undefined in the public repository. Either define this function inline or document clearly which package provides it and how to install it.

- **`README.md` (top-level):** The README provides a high-level overview but does not specify which version of Hail was used. Hail's API changed significantly between v0.1 and v0.2; several functions in the pipeline scripts (e.g., `hl.import_vcf()` argument signatures) will fail on mismatched versions. Adding a version badge or a `hail_version.txt` would help.

- **`manuscript_analyses/` scripts:** Variable names are generally expressive (e.g., `obs_lof`, `exp_lof`, `constraint_z`), which is good practice. However, several scripts use undocumented single-letter loop variables (`for i in ...`) over gene lists. Replacing these with descriptive names (e.g., `gene_name`) would improve readability. Inline comments explaining the logic of key steps are also sparse in these scripts and would help readers follow the analysis.

- **`pipeline/` Hail scripts:** Several scripts hardcode Broad Institute internal file paths (e.g., `/broad/...`) that are inaccessible externally. These should either be parameterized as command-line arguments or replaced with publicly accessible `gs://` Cloud Storage paths.

- **No test data or expected outputs:** The repository contains no small example input files and no `tests/` directory. Adding even a 10-gene example input with expected output values would allow users to validate their installation and catch environment-related failures early.

- **Figure scripts (positive note):** `figure2_constraint_distributions.R` ran without modification using the pre-computed gnomAD constraint file and reproduced the published Figure 2 exactly. This is commendable and relatively rare. The figure scripts as a whole are the strongest part of the repository in terms of usability.
