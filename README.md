# Colorectal-BRAF-ctDNA-Analysis
This project analyzes circulating tumor DNA from a colorectal cancer patient at two time points baseline and post treatment. The objective is to determine whether a new mutation emerged in the BRAF gene after first line chemotherapy combined with anti EGFR therapy.

All work was performed using publicly available sequencing data from NCBI SRA and processed step by step in Google Colab. The repository includes the notebook, scripts, logs, and filtered VCFs needed to reproduce the analysis.

1. Project Overview

Anti EGFR therapies in colorectal cancer commonly select for new mutations in the MAPK pathway. KRAS and NRAS are the most frequent resistance drivers, but BRAF can also acquire mutations that activate downstream signaling and reduce treatment response.

This project focuses specifically on BRAF to evaluate whether the post treatment ctDNA sample contains any new or emerging variants that were not present at baseline.

2. Data

Two paired end ctDNA sequencing samples were used:

Baseline sample SRA accession: SRR13974039

Post treatment sample SRA accession: SRR13974040

Both represent blood derived ctDNA collected at different clinical time points from the same patient.

FASTQ files were downloaded directly from ENA inside the notebook environment.

3. Methods Summary
Quality Control

FastQC was used to inspect raw read quality. Fastp was used for adapter trimming and quality improvement. QC reports confirmed good overall read quality.

Reference Preparation

The analysis used the GRCh38 primary assembly from GENCODE release 45. Indexing was performed for both bwa and samtools. BRAF exon coordinates were extracted from the same annotation to create a BED file for gene restricted variant calling.

Alignment

Trimmed reads were aligned to GRCh38 with bwa mem. BAM files were sorted and indexed with samtools. Alignment quality was assessed using flagstat, and both samples showed high mapping rates and consistent library structure.

Variant Calling

Variants in BRAF were called using FreeBayes with sensitive parameters suitable for ctDNA. Raw calls were normalized and filtered with bcftools to remove low depth, low confidence, and low support alleles.

Baseline vs Post Comparison

Filtered VCFs were compared to identify variants present only in the post treatment sample. A relaxed comparison was also run on raw calls to explore low level signals that may not meet confidence thresholds.

4. Key Results

Both ctDNA samples showed excellent alignment performance, with nearly all reads mapped and strong pairing quality.

The variant caller produced several raw novel allele signals in the post treatment sample. In ctDNA analysis this is expected, because raw calls include noise as well as potential candidates.

A relaxed baseline vs post comparison identified multiple raw post treatment records with at least one allele not observed in the baseline sample.

Raw novel alleles do not automatically represent true biological mutations. They often reflect background sequencing noise, strand bias, or low support signals.

After applying quality filters based on depth, alternate read count, and variant quality, none of the raw novel alleles passed the confidence thresholds.

Final conclusion: there is no high confidence evidence of a new BRAF mutation emerging after anti EGFR therapy in this patient.

5. Repository Structure
ctDNA_project/
    fastq/                   trimmed reads (not tracked in Git)
    reference/               GRCh38 reference, indexes, and BRAF BED
    bam/                     sorted and indexed BAMs (stored externally if large)
    qc/                      FastQC and fastp HTML reports
    logs/                    alignment and variant calling logs
    results/
        BRAF_baseline.raw.vcf
        BRAF_post.raw.vcf
        BRAF_baseline.filtered.vcf
        BRAF_post.filtered.vcf
        BRAF_compare/
            0000.vcf         baseline only variants
            0001.vcf         post only variants
            0002.vcf         shared variants
        BRAF_compare_relaxed/
            0001.vcf         relaxed post only variants for review
pipeline.ipynb               full executed notebook
pipeline.py                  extracted script version
README.md                    project documentation
.gitignore                   excludes large files


Large FASTQ and BAM files should be stored outside Git or handled with Git LFS.

6. How to Reproduce

Clone the repository

Open pipeline.ipynb in Google Colab or Jupyter

Run cells in order starting from environment setup

Add FASTQ files or update SRA accessions

Review output in the results and logs folders

All steps include comments explaining the purpose and expected results.

7. Interpretation

Across all analyses there was no high confidence post treatment BRAF mutation detected.
This suggests that BRAF driven resistance was not present at the time of the post treatment blood draw. Any low level raw allele signals lacked the depth and support required to classify them as true variants.

Because ctDNA sequencing can miss very low allele fraction mutations, orthogonal confirmation with sensitive assays such as ddPCR is recommended for clinical use.

8. Limitations

Only one gene was evaluated. Resistance is more commonly driven by KRAS and NRAS.

Sensitivity is limited by ctDNA depth and caller thresholds. Very low frequency variants may remain undetectable.

No matched normal or tumor tissue was available to refine somatic classification.

