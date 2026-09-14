---
title: artic-mpxv-nf
nav_order: 8
nav_exclude: false
has_children: false
has_toc: true
permalink: /artic-mpxv-nf/
---

# Mpox genome reconstruction with `artic-mpxv-nf`

In this tutorial, you will use **`artic-mpxv-nf`** to reconstruct mpox virus genomes from Oxford Nanopore Technologies (ONT) amplicon sequencing reads.

The workflow implements the ARTIC field bioinformatics workflow for MPXV and uses Nextflow to coordinate the individual analysis steps.

---

## 🎯 Learning objectives

By the end of this tutorial, you will be able to:

- Run `artic-mpxv-nf` on ONT mpox sequencing reads
- Specify the appropriate mpox primer scheme and reference
- Configure the workflow for execution on the cluster or local machine
- Specify the ONT basecalling model used for the sequencing data
- Define separate directories for workflow results and downloaded workflow resources
- Explain the purpose of the main Nextflow options and `artic-mpxv-nf` parameters used in the command

---

## Before you start

The workflow is run with **Nextflow**.

The input for this tutorial is the `fastq_pass` directory generated during basecalling. It contains the ONT FASTQ reads that passed the basecaller quality threshold.

The example analysis uses:

- dataset: `yale_testrun1`
- primer scheme: `yale-mpox/2000/v1.0.0-cladeii`
- MPXV clade: `cladeii`
- ONT model: `r1041_e82_400bps_hac_v520`

---

## Define the project directory

First, define the location of the workshop project:

```bash
PROJECT_DIR="$HOME/scratch/hspa26"
```

Using a variable makes the command easier to read and avoids repeatedly typing the full project path.

> **Note**
>
> Do not write the home-directory shortcut `~` inside quotes, for example:
>
> ```bash
> PROJECT_DIR="~/scratch/hspa26/"
> ```
>
> In Bash, `~` is not expanded to your home directory when it is inside quotes. Using `$HOME` avoids this problem.

Check the variable with:

```bash
echo "$PROJECT_DIR"
```

---

## Run `artic-mpxv-nf`

```bash
nextflow run artic-network/artic-mpxv-nf \
    -r 2.1.0 \
    -profile rki_mamba,rki_slurm \
    -c "$PROJECT_DIR/1_scripts/rki_profile_with_fixes.config" \
    --fastq "$PROJECT_DIR/0_data/yale_testrun1/fastq_pass" \
    --scheme_version "yale-mpox/2000/v1.0.0-cladeii" \
    --out_dir "$PROJECT_DIR/2_analysis/01_artic-mpxv-nf/yale_testrun1_cladeii" \
    --store_dir "$PROJECT_DIR/2_analysis/01_artic-mpxv-nf/store_dir_yale_testrun1_cladeii" \
    --override_model r1041_e82_400bps_hac_v520 \
    --validate_params false \
    --clade cladeii
```

The backslash (`\`) at the end of each line tells Bash that the command continues on the next line.

---

## Command explanation

The command contains two types of options:

1. **Nextflow options**, such as `-r`, `-profile`, and `-c`
2. **Pipeline parameters**, which begin with `--` and are passed to `artic-mpxv-nf`

### `nextflow run artic-network/artic-mpxv-nf`

Starts the Nextflow workflow hosted in the `artic-network/artic-mpxv-nf` GitHub repository.

The workflow is designed to generate consensus sequences from MPXV genomes sequenced using a pooled tiling-amplicon strategy.

### `-r 2.1.0`

Selects workflow version **2.1.0**.

Using a fixed workflow version improves reproducibility because the pipeline code can change over time.

### `-profile rki_mamba,rki_slurm`

Activates two Nextflow configuration profiles.

- `rki_mamba` configures software dependencies through the RKI Mamba/Conda setup.
- `rki_slurm` configures Nextflow to submit tasks to the SLURM workload manager on the RKI HPC cluster.

Multiple profiles are separated by commas.

### `-c "$PROJECT_DIR/1_scripts/rki_profile_with_fixes.config"`

Loads an additional Nextflow configuration file.

In this workshop, `rki_profile_with_fixes.config` contains RKI-specific settings required for execution on the HPC system.

### `--fastq`

```bash
--fastq "$PROJECT_DIR/0_data/yale_testrun1/fastq_pass"
```

Specifies the input ONT FASTQ reads.

The workflow can accept:

- a single FASTQ file,
- a directory containing FASTQ files, or
- a directory containing barcode subdirectories.

### `--scheme_version`

```bash
--scheme_version "yale-mpox/2000/v1.0.0-cladeii"
```

Selects the ARTIC primer scheme and reference context.

| Component | Meaning |
| --- | --- |
| `yale-mpox` | Primer scheme name |
| `2000` | Approximate amplicon length in bp |
| `v1.0.0-cladeii` | Scheme version using the Clade II reference |

The workflow documentation states that the Yale Clade I and Clade II scheme variants use the same primer scheme but separate reference FASTA files.

### `--out_dir`

```bash
--out_dir "$PROJECT_DIR/2_analysis/01_artic-mpxv-nf/yale_testrun1_cladeii"
```

Defines where the final workflow results are written.

### `--store_dir`

```bash
--store_dir "$PROJECT_DIR/2_analysis/01_artic-mpxv-nf/store_dir_yale_testrun1_cladeii"
```

Defines a persistent directory for reusable workflow resources, such as downloaded primer schemes or model files.

| Parameter | Purpose |
| --- | --- |
| `--out_dir` | Final results for the current analysis |
| `--store_dir` | Reusable resources required by the workflow |

### `--override_model`

```bash
--override_model r1041_e82_400bps_hac_v520
```

Overrides automatic model detection and explicitly supplies the ONT model associated with the input reads.

The model string indicates:

| Part | Meaning |
| --- | --- |
| `r1041` | R10.4.1 pore chemistry |
| `e82` | E8.2 motor chemistry |
| `400bps` | 400 bases-per-second configuration |
| `hac` | High Accuracy model |
| `v520` | Model version 5.2.0 |

The pipeline documentation warns that this value should only be overridden when the correct model is known, because an incorrect model can reduce variant-calling quality.

### `--validate_params false`

```bash
--validate_params false
```

Disables validation of supplied parameters against the workflow schema at runtime.

This is useful for the workshop configuration, but it also means that incorrectly specified parameters may not be caught before execution.

### `--clade cladeii`

```bash
--clade cladeii
```

Specifies the MPXV clade context as **Clade II** for downstream sequence analysis.

This is consistent with:

```bash
--scheme_version "yale-mpox/2000/v1.0.0-cladeii"
```

---

## Overview of the complete command

| Option / parameter | Value | Purpose |
| --- | --- | --- |
| `nextflow run` | `artic-network/artic-mpxv-nf` | Run the MPXV ARTIC workflow |
| `-r` | `2.1.0` | Use workflow version 2.1.0 |
| `-profile` | `rki_mamba,rki_slurm` | Use RKI Mamba and SLURM profiles |
| `-c` | `rki_profile_with_fixes.config` | Load additional RKI Nextflow configuration |
| `--fastq` | `yale_testrun1/fastq_pass` | Specify input ONT FASTQ reads |
| `--scheme_version` | `yale-mpox/2000/v1.0.0-cladeii` | Select the Yale MPXV 2-kb Clade II scheme/reference |
| `--out_dir` | `yale_testrun1_cladeii` | Define the final results directory |
| `--store_dir` | `store_dir_yale_testrun1_cladeii` | Store reusable workflow resources |
| `--override_model` | `r1041_e82_400bps_hac_v520` | Specify the ONT model |
| `--validate_params` | `false` | Disable schema validation |
| `--clade` | `cladeii` | Use the MPXV Clade II context |

---

## Monitor the workflow

After starting the command, Nextflow will print information about submitted processes and their status.

Typical statuses include:

```text
SUBMITTED
RUNNING
COMPLETED
```

Because the `rki_slurm` profile is active, computational tasks are submitted to SLURM.

---

## Check the output directory

After the workflow completes:

```bash
ls -lh "$PROJECT_DIR/2_analysis/01_artic-mpxv-nf/yale_testrun1_cladeii"
```

To inspect files recursively:

```bash
find "$PROJECT_DIR/2_analysis/01_artic-mpxv-nf/yale_testrun1_cladeii" -maxdepth 2 -type f
```

Important output files can include:

```text
<sample>.consensus.fasta
<sample>.amplicon_depths.tsv
<sample>.sorted.bam
<sample>.primertrimmed.rg.sorted.bam
<sample>.normalised.named.vcf.gz
```

The exact files present depend on the samples and workflow configuration.

---

## Inspect consensus sequences

Find consensus FASTA files:

```bash
find "$PROJECT_DIR/2_analysis/01_artic-mpxv-nf/yale_testrun1_cladeii" \
    -name "*.consensus.fasta"
```

A consensus FASTA contains the reconstructed MPXV genome for a sample.

---

## Inspect amplicon coverage

Find amplicon-depth files:

```bash
find "$PROJECT_DIR/2_analysis/01_artic-mpxv-nf/yale_testrun1_cladeii" \
    -name "*.amplicon_depths.tsv"
```

These files are useful for identifying:

- well-covered amplicons
- poorly covered amplicons
- potential amplicon dropouts
- uneven coverage across the primer scheme

---

## 💡 Questions

1. Why is it useful to specify a fixed workflow version with `-r`?
2. What is the difference between `--out_dir` and `--store_dir`?
3. Why must `--override_model` match the model used to basecall the reads?
4. What could happen if the wrong primer scheme or reference clade were selected?
5. Why might one amplicon have substantially lower depth than neighboring amplicons?
6. Which output file contains the reconstructed genome sequence?
7. Which output would you inspect to identify potential amplicon dropouts?

---

## Summary

In this tutorial, you:

- ran `artic-mpxv-nf` using Nextflow
- configured execution on the RKI SLURM cluster
- specified the input ONT FASTQ data
- selected the Yale MPXV Clade II primer scheme
- specified the ONT model
- defined separate result and workflow-resource directories
- learned how the major Nextflow options and pipeline parameters affect the analysis
- identified key output files for downstream genome quality assessment

The reconstructed consensus genomes can subsequently be used for **quality assessment, lineage assignment, comparative genomics, and phylogenetic analysis**.
