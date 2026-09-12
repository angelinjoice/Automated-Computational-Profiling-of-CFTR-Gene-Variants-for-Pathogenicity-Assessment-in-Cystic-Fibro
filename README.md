# Automated Computational Profiling of CFTR Gene Variants for Pathogenicity Assessment in Cystic Fibrosis

A bioinformatics pipeline designed to parse, annotate, filter, and prioritize *CFTR* (Cystic Fibrosis Transmembrane Conductance Regulator) genomic variants from VCF datasets for clinical pathogenicity assessment.

## Objective

The primary objective of this project is to build an automated, reproducible computational pipeline that extracts *CFTR* gene variants from high-throughput genomic data, enriches them with multi-database annotations (ClinVar, gnomAD, CFTR2), predicts functional impact using *in silico* scores (CADD, SIFT, PolyPhen-2), and prioritizes high-confidence pathogenic mutations for clinical interpretation and targeted therapeutic mapping.

## Genomic Domain & Coordinates
* **Target Gene:** *CFTR* (Cystic Fibrosis Transmembrane Conductance Regulator)
* **Organism:** *Homo sapiens*
* **Chromosome Region:** `chr7:117,480,025 - 117,668,665`
* **Genome Assembly:** GRCh38 / hg38 (`NC_000007.14`)

## Workflow & Methodology

The pipeline follows a structured 5-step computational workflow:

### Step 1: Data Acquisition
* Coordinates: Human *CFTR* gene on Chromosome 7 (GRCh38: `NC_000007.14:g.117465784-117682387`).
* Tools: Built using `cyvcf2` to extract high-confidence variant records.
* Input: `clinvar.vcf.gz` raw variant call dataset.
* Output: `acquired_data/cftr_clinvar.vcf` targeted gene slice.

### Step 2: Variant Annotation
* Tools: `SnpEff` integration using `GRCh38.86` database models.
* Added Context: Structural annotations (exon, intron, UTRs), mutation types (missense, nonsense, frameshift), and protein-level HGVS alterations.
* Output: `annotated_data/cftr_clinvar_annotated.vcf`.

### Step 3: Database Integration (Cross referencing)
Executed via `Scripts/Step3.py`:
* ClinVar: Extract reported clinical significance (`Pathogenic`, `Likely Pathogenic`, `Benign`, `VUS`).
* CFTR2: Cross-reference known variants against disease-causing clinical database entries.
* gnomAD: Retrieve global Allele Frequencies (`AF`) to identify rare variants vs. common polymorphisms (`AF < 0.01`).
* In Silico Predictors: Fetch pathogenicity scores from CADD PHRED (`CADD_PHRED >= 20.0`), SIFT, and PolyPhen-2.
* Output: `Reports/scored_annotations.csv`.

### Step 4: Scoring Python Data Processing & Prioritization
Executed via `Scripts/Step4.py`:
1. Quality & Frequency Filtering: Retain high-confidence calls and exclude common benign variants (`AF >= 0.01`).
2. Multi-Factorial Rank Scoring: Assign priority rank scores based on `SnpEff` impact (`HIGH` = 4, `MODERATE` = 3), ClinVar/CFTR2 pathogenic confirmation (+3), CADD cutoff (+2), and SIFT/PolyPhen predictions (+1).
3. Precision Drug Therapy Mapping: Match variants to targeted modulators including Trikafta (`F508del`), Kalydeco (`G551D`, `R117H`), and read-through therapies for nonsense stop-gain mutations (`G542X`).
4. Output: `Reports/ranked_cftr_variants_final.csv`.

### Step 5: Visualizations & Clinical Summary Export
Executed via `Scripts/Step5.py`:
1. Mutation Distribution: Generate `figures/1_mutation_types_barchart.png` showing proportions of missense, nonsense, indels, and splice variants.
2. Domain Mapping: Generate `figures/2_cftr_domain_lollipop.png` mapping variant rank scores across *CFTR* protein domains (TMD1, NBD1, R Domain, TMD2, NBD2).

## Repository Structure

Variant-annotation-and-pipeline/
├── acquired_data/
│   ├── cftr_clinvar.vcf
│   └── cftr_test.vcf
├── annotated_data/
│   ├── cftr_clinvar_annotated.vcf
│   ├── cftr_testannotated.vcf
│   ├── summary
│   ├── summary.genes.txt
│   ├── testresults
│   └── testresults.genes.txt
├── figures/
│   ├── 1_mutation_types_barchart.png
│   ├── 2_cftr_domain_lollipop.png
│   └── 3_cadd_score_distribution.png
├── Reports/
│   ├── final_ranked_cftr_summary.csv
│   ├── ranked_cftr_variants_final.csv
│   └── scored_annotations.csv
├── Scripts/
│   ├── Step1.py
│   ├── Step2.txt
│   ├── Step3.py
│   ├── Step4.py
│   └── Step5.py
├── snpEff/
├── .gitignore
├── CFTR.py
└── README.md

