# Code Review: Karczewski et al. (2020)

**Reviewer:** Samira Salimiyan  
**Course:** BIOL 7180 (Scripting for Biologists)  
**Institution:** Auburn University  

**Title:** The mutational constraint spectrum quantified from variation in 141,456 humans
**DOI:** https://doi.org/10.1038/s41586-020-2308-7

### Summary
Karczewski et al. (2020) present gnomAD v2.1.1, aggregating exome and genome
sequencing data from 141,456 individuals to quantify gene-level constraint
against loss-of-function (LoF) and missense variation. The authors introduce
three metrics — pLI, observed/expected LoF ratio (o/e), and missense Z-score —
that have since become standard tools for prioritizing candidate disease genes
and interpreting variant pathogenicity. As requested by the editor, my review
below focuses on the code associated with this manuscript, available at
https://github.com/broadinstitute/gnomad_lof. **Authors:** Karczewski, K.J., Francioli, L.C., Tiao, G., et al. (Genome Aggregation Database Consortium)

### Suggestions:

Provide a complete, standalone constraint calculator.
The most critical gap in the repository is the absence of fit_lof_model(),
the internal helper function called by calculate_constraint() in
constraint_functions.R. Running this function produces the error:
could not find function 'fit_lof_model'. Without it, users cannot
independently compute pLI or o/e LoF from raw variant data. The core
Bayesian model appears to reside in an internal library not released
publicly. Bundling this into a standalone, installable R package with
minimal example input data would be the single highest-impact improvement.
Add a containerized environment (Docker or Conda).
The repository provides no requirements.txt, environment.yml, or
Dockerfile. The Hail pipeline requires a specific version of Apache Spark
and the gnomad Python package, both of which have breaking changes across
versions. Without pinned dependencies, users cannot reliably reproduce the
environment. A Conda environment file or Docker image would resolve this.
Reduce the infrastructure barrier with a small-scale tutorial.
The full pipeline was run on Google Cloud with ~500 cores and >1 TB of input
data. While this is unavoidable at gnomAD scale, a "Quick Start" tutorial
showing how to calculate constraint for a single gene on a local machine
(using the pre-computed expected counts) would make the repository
substantially more accessible. Currently, most users are forced to use the
pre-computed scores without any way to verify or extend the method.



--- 
