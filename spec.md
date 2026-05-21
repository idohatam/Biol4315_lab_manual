# Biol 4315 Lab Manual - Technical Specifications (spec.md)

## 1. Rigid Folder Structure

To ensure students can seamlessly replicate the code on their local machines (M4 Macs) and to maintain isolation between independent topics, the Quarto book project will mirror the student directory structure. Since Labs 2 & 3 share data, they will share a directory. Labs 4 and 5 are independent.

```text
/ (Project Root)
├── _quarto.yml
├── index.qmd             # Title page with course info
├── PR.md                 # Pull Request audit file
├── assets/               # Book-level assets (e.g., cover.png)
├── lab1/
│   ├── 01-lab1.qmd
│   ├── assets/           # Images for Lab 1
│   └── outputs/          # Generated output files (Gitignored)
├── lab2_3/
│   ├── 02-lab2.qmd
│   ├── 03-lab3.qmd
│   ├── assets/           # Images for Lab 2 & 3
│   ├── data/             # FASTQ and reference files (Gitignored)
│   └── outputs/          # Generated output files (Gitignored)
├── lab4/
│   ├── 04-lab4.qmd
│   ├── data/             # Transcriptomics data (Gitignored)
│   └── outputs/          # Generated output files (Gitignored)
└── lab5/
    ├── 05-lab5.qmd
    ├── data/             # Metagenomics data (Gitignored)
    └── outputs/          # Generated output files (Gitignored)
```

**Relative Pathing Strategy:**
By placing the `.qmd` files in these subdirectories, Quarto's working directory during rendering will naturally be the subdirectory itself. We will use the `here::here()` package in R chunks to establish robust pathing, or standard relative paths (`./data/...`, `./outputs/...`) in bash chunks. All student-facing instructions will explicitly direct them to create an RStudio Project in their respective lab folders with `data/` and `outputs/` subdirectories.

## 2. Asset Manifest

The following external image dependencies were identified in the `/2025_lab` `.Rmd` files. The instructor has provided these images, and they will be placed in their respective `assets/` subdirectories.

**Lab 1:**
- `Picture1.png`
- `Picture2.png`
- `Picture3.png`

**Lab 2:**
- `fastqPic.png`
- `CycleQualityBoxPlot-1.png`
- `baseCallFreq-1.png`
- `ReadFrequencyPlot-1.png`

## 3. Data Manifest

The following data files are required. All directories containing these files (e.g., `data/`) MUST be added to `.gitignore`.

**Lab 2 & 3 (in `lab2_3/data/`):**
- *Downloads (Bash/wget):*
  - `ERR1539006.fastq.gz` (Nanopore reads)
  - `ERR11203340_1.fastq.gz`, `ERR11203340_2.fastq.gz` (Illumina short reads)
  - `GCF_000146045.2_R64_genomic.fna.gz` (Yeast Reference)
  - `GCF_000146045.2_R64_genomic.gff.gz` (Yeast Annotation)
  - `GCF_000146045.2_R64_cds_from_genomic.fna.gz` (Yeast CDS)
- *Internal Package Data:*
  - `E-MTAB-1147` (From `ShortRead` package)

**Lab 4 (in `lab4/data/`):**
- *Downloads (Bash/wget):*
  - `GCF_000001735.4_TAIR10.1_genomic.fna.gz`
  - `GCF_000001735.4_TAIR10.1_genomic.gtf.gz`
- *Internal Package Data:*
  - 36 FASTQ files from `systemPipeRdata` that are copied over to the student's local directory.

**Lab 5 (in `lab5/data/`):**
- *Downloads (R download.file):*
  - `miseqsopdata.zip` (Mothur MiSeq SOP data, extracts to `MiSeq_SOP/`)
  - `silva_nr99_v138.1_wSpecies_train_set.fa.gz` (Taxonomic Reference)

## 4. Infrastructure Specs (ARM64 / M4 Mac Compatibility)

All Conda and Docker environments must be tested for ARM64 compatibility.

- **Lab 1 & 2:**
  - Conda: `sequali`, `filtlong`
  - Docker: `ezlabgva/busco`, `funannotate` (Need to verify ARM64 images for these on Docker Hub)
- **Lab 3:**
  - Conda: `flye`, `minimap2`
  - Docker: `quay.io/biocontainers/quast:5.3.0--py313pl5321h5ca1c30_2`, `ezlabgva/busco`
- **Lab 4:**
  - Conda: `hisat2`
- **Lab 5:**
  - Native R Bioconductor (`dada2`, `phyloseq`)

**Note on Environments:** Most containers and conda environments are already set up on the host computer (e.g., `sequali_env`, `busco_env`, `ezlabgva/busco:v6.0.0_cv1`). Verify their existence locally during migration. If present, set `eval: false` for code chunks that set them up in the manual so they don't redundantly install during rendering.

**Note on Klippy:** The `klippy` package used in the legacy `.Rmd` files is deprecated in favor of Quarto's built-in code copy buttons. Do not port `klippy` configurations.

## 5. Computationally Intensive Steps

The following steps have been flagged as computationally intensive. During the rendering process, these will be executed as background processes to prevent timeout and system resource exhaustion:

- **Lab 3:** `flye` de-novo genome assembly, `minimap2` alignments against reference genomes, and `quast`/`busco` evaluations.
- **Lab 4:** `hisat2` index generation, splice-aware mapping of PE reads, and `featureCounts` assignment.
- **Lab 5:** `DADA2` core algorithm (`learnErrors`, `dada` sample inference) and taxonomy assignment (`assignTaxonomy`).

## 6. General Styling & Structure

- **Header Consistency:** All section and subsection headers must use sentence case (e.g., 'Quality control of reads' rather than 'Quality Control of Reads').
- **Modern Quarto Syntax:** All R and Bash code chunks must use the modern `#|` syntax for chunk options (e.g., `#| label: chunk_label` and `#| eval: false`).

## 7. The Code Execution Standard

- **Verification Protocol:** Every code chunk must be executed and verified before pushing. A successful `quarto render` is not sufficient validation when `error: true` is set globally. Time-consuming steps must be extracted and run via an independent background `.sh` or `.R` script. Any failed code chunks must be explicitly reported to the instructor, even if the document successfully renders.
- **Output File Verification:** Before executing any tool that writes to disk (e.g., Flye, Medaka, BUSCO, QUAST), output directories must be explicitly created via `mkdir -p`. You must verify that the expected output files exist after each run. Students must also be instructed to create necessary output directories to ensure their code runs properly.
- **Loading Background Results:** To ensure the rendered Quarto document reflects reality without redundantly executing heavy tasks, the background script must generate the physical outputs. For R visualizations, save outputs (e.g., via `save.image()` to `.RData`), and import these R objects back using an invisible code chunk (e.g., `#| include: false`). For bash command outputs (like QUAST HTML reports or BAM files), the generated files must be physically present in the `outputs/` directory.
- **Eval Constraints:** Use `eval: true` for quick setup, QC, and data visualization chunks to ensure the student environment is functional. Heavy background-dependent chunks (assembly, polishing, containerized QC) should have `eval: false`, but their outputs must still be verifiable by the instructor via the generated files on disk.