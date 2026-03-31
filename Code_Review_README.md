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





--- 
