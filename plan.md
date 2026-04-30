# Biol 4315 Lab Manual - Operational Roadmap (plan.md)

## 1. Migration Sequence

The labs will be migrated in the following order to respect both pedagogical progression and data dependencies:
1. **Lab 1:** Computational Environment Setup
2. **Lab 2:** QC of Long and Short Read
3. **Lab 3:** Assembly (Depends on Lab 2)
4. **Lab 4:** Intro to Transcriptomics
5. **Lab 5:** Intro to Metagenomics

## 2. Phases for Migration

### Phase 1: Project Initialization & Infrastructure Setup
- **Objective:** Establish the Quarto book skeleton, ensure proper Git branching, and create the necessary folder structures.
- **Tasks:**
  - Verify and checkout the `dev` branch.
  - Create the directory structures (`lab1/`, `lab2_3/`, `lab4/`, `lab5/`, `assets/`, `data/`, `outputs/`).
  - Create `.gitignore` to explicitly ignore all `data/` and `outputs/` subdirectories, intermediate build files, and session continuity files (`log.txt`, `self_reflection.md`, `feedback.md`).
  - Set up `_quarto.yml` for an HTML-only book using Flatly (Light) and Darkly (Dark) themes.
  - Create `index.qmd` with title page placeholders.
- **Agent Assignment:** 
  - Git Specialist (Branching & Gitignore)
  - Generalist (Quarto skeleton & Folder structure)
- **Success Criteria:** `quarto render` successfully builds a blank book with `index.qmd` with zero errors.

### Phase 2: Migration of Lab 1
- **Objective:** Port `.Rmd` to `.qmd`, format text and syntax.
- **Tasks:**
  - Migrate content to `lab1/01-lab1.qmd`.
  - Fix LaTeX math, update Quarto call-outs (notes, quotes).
  - Remove any `klippy` package usage.
  - Set `eval: false` for `conda_whats_installed()` and git configuration chunks.
  - Verify Docker/Conda setup instructions, setting `eval: false` for installation steps if already present on host.
- **Agent Assignment:** 
  - Generalist (Syntax, layout, formatting)
  - Omics Specialist (Verify bioinformatics concept accuracy)
- **Success Criteria:** `quarto render` passes; text format looks correct; all code chunks evaluated.

### Phase 3: Migration of Labs 2 & 3
- **Objective:** Consolidate Labs 2 & 3 as sequential chapters sharing a project environment.
- **Tasks:**
  - Migrate content to `lab2_3/02-lab2.qmd` and `lab2_3/03-lab3.qmd`.
  - Setup explicit data download (`wget`) instructions (including the CDS FASTA).
  - Run computationally intensive genome assembly and mapping in the background.
  - Fill missing gaps (e.g. medaka polishing from course 2).
- **Agent Assignment:** 
  - Generalist (Migration and Markdown translation)
  - Omics Specialist (Data sanity checks, debugging computational pipelines)
- **Success Criteria:** `quarto render` passes; genome assembly completes; mapping outputs exist.

### Phase 4: Migration of Lab 4
- **Objective:** Port transcriptomics workflow.
- **Tasks:**
  - Migrate content to `lab4/04-lab4.qmd`.
  - Setup dataset from `systemPipeRdata` and TAIR10.1 references.
  - Execute splice-aware alignment and feature counts in the background.
- **Agent Assignment:**
  - Generalist (Formatting, syntax)
  - Omics Specialist (Verification of RNA-Seq statistical pipeline)
- **Success Criteria:** `quarto render` passes; SAM/BAM outputs verified; DGE objects initialized.

### Phase 5: Migration of Lab 5
- **Objective:** Port metagenomics workflow.
- **Tasks:**
  - Migrate content to `lab5/05-lab5.qmd`.
  - Execute DADA2 pipeline in the background.
- **Agent Assignment:**
  - Generalist (Syntax translation)
  - Omics Specialist (Verification of taxonomic annotation and DADA2 modeling)
- **Success Criteria:** `quarto render` passes; Phyloseq objects correctly plotted.

### Phase 6: Final Review & PR Generation
- **Objective:** Complete the `PR.md` audit and prepare for merge.
- **Tasks:**
  - Conduct Technical Audit and Pedagogical Audit.
  - Ensure all labs are perfectly integrated and `quarto render` runs cleanly from start to finish.
  - Generate `PR.md` for human review.
- **Agent Assignment:** 
  - Generalist (Drafts `PR.md`)
  - Omics Specialist (Owns pedagogical content for Audit)
  - Git Specialist (Technical Audit & creates the actual PR)
- **Success Criteria:** Human instructor approves `PR.md`.

### Phase 7: Publishing (Post-PR)
- **Objective:** Publish the book via GitHub Pages.
- **Tasks:**
  - Merge PR from `dev` into `main`.
  - Ask human for approval before deleting the `dev` branch.
  - Human turns repository Public.
  - Setup GitHub Actions to render and deploy automatically on push to `main` (instead of committing to a `gh-pages` branch).
- **Agent Assignment:**
  - Git Specialist (Branch operations, Actions setup, Pages config)
- **Success Criteria:** The HTML book is live and accessible via the GitHub Pages URL.

---

## 3. Global Agent Responsibilities

| Sub-Agent | Primary Responsibilities |
| :--- | :--- |
| **Generalist** | - Establishing directory structures and `_quarto.yml`<br>- Migrating `.Rmd` to `.qmd` and updating Quarto syntax<br>- Converting `>` to call-outs, enforcing formatting spacing |
| **Omics Specialist** | - Pedagogical review of scientific concepts and text<br>- Debugging intensive background pipelines (Assembly, HISAT2, DADA2)<br>- Identifying missing data/references and auditing methodology |
| **Git Specialist** | - Branch creation, commits, and `.gitignore` management<br>- Generating `PR.md` for final review<br>- Setting up GitHub Actions and GitHub Pages publishing workflows |

## 4. Validation Gates

Before moving from one Phase to the next, the following criteria **must** be met:
1. **Zero Render Errors:** The command `quarto render` executed from the project root finishes successfully.
2. **Missing Dependencies Handled:** All required images are identified, and all data dependencies (FASTQs, fasta, annotations) are properly `wget`'d or flagged.
3. **Execution Integrity:** Background processes for computationally intensive chunks return zero exit statuses.
4. **Git Discipline:** Changes are committed to the `dev` branch with clear messages, and large data files are rigorously excluded via `.gitignore`.
5. **Human Approval:** At Phase 6, `PR.md` must be manually approved by the instructor before any PR is generated or code is pushed to `main`.