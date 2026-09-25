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
- Configure the workflow for execution on the local machine
- Identify the ONT basecalling model from FASTQ headers
- Specify the corresponding model with `--override_model`
- Define separate directories for workflow results and downloaded workflow resources
- Explain the purpose of the main Nextflow options and `artic-mpxv-nf` parameters used in the command

---

## Before you start

The workflow is run with **Nextflow**.

The input for this tutorial is the `fastq_pass` directory generated during basecalling. It contains the ONT FASTQ reads that passed the basecaller quality threshold.

The example analysis uses:

- dataset: `testrun_mpox_amplicon_minion_yale-mpox-2000`
- primer scheme: `yale-mpox/2000/v1.0.0-cladeii`
- MPXV clade: `cladeii`
- ONT basecalling model: Dorado v5.2.0 HAC, corresponding to `r1041_e82_400bps_hac_v520`

---

## Define the project directory

First, define the location of the workshop project:

```bash
PROJECT_DIR="$HOME/scratch/2026-HSPA-Autumn-School"
```

Using a variable makes the commands easier to read and avoids repeatedly typing the full project path.

{: .note }
Do not write the home-directory shortcut `~` inside quotes, for example: `PROJECT_DIR="~/scratch/2026-HSPA-Autumn-School"`
In Bash, `~` is not expanded to your home directory when it is inside quotes. Using `$HOME` avoids this problem.

Check the variable with:

```bash
echo "$PROJECT_DIR"
```

Activate the Nextflow Conda environment:

```bash
conda activate nextflow
```

---

## Identify the basecalling model

`artic-mpxv-nf` needs to know which ONT basecalling model was used to generate the reads. The model is used by the ARTIC workflow when selecting the appropriate variant-calling model.

Recent ONT FASTQ headers normally contain the basecalling model in the `basecall_model_version_id` field. You can inspect the header of the first read in one of the FASTQ files before running the workflow.

```bash
zgrep -m 1 '^@' $PROJECT_DIR/data/yale_testrun1/fastq_pass/barcode08.excluding_human.fastq.gz | grep dna_
```

For `artic-mpxv-nf`, the corresponding model name is:

```text
r1041_e82_400bps_hac_v520
```

Therefore, we will use:

```bash
--override_model r1041_e82_400bps_hac_v520
```

{: .note }
The value passed to `--override_model` is **not necessarily identical to the string in the FASTQ header**. It must correspond to a model supported by the ARTIC/Clair3 installation used by the workflow. For example, Dorado `dna_r10.4.1_e8.2_400bps_hac@v5.2.0` corresponds to the Clair3 model `r1041_e82_400bps_hac_v520`.

{: .warning }
Do not simply copy the model from this tutorial when analysing your own sequencing run. Check the FASTQ headers and select the model matching the chemistry, basecalling mode (FAST/HAC/SUP), and basecaller model version used for your data. Using the wrong model can reduce variant-calling accuracy.

---

## Run `artic-mpxv-nf`

Run the workflow with:

```bash
nextflow run artic-network/artic-mpxv-nf \
    -r 2.1.0 \
    -process.executor local \
    --fastq "$PROJECT_DIR/data/testrun_mpox_amplicon_minion_yale-mpox-2000/fastq_pass" \
    --scheme_version "yale-mpox/2000/v1.0.0-cladeii" \
    --out_dir "$PROJECT_DIR/analysis/02_artic-mpxv-nf/testrun_mpox_amplicon_minion_yale-mpox-2000_cladeii" \
    --store_dir "$PROJECT_DIR/analysis/02_artic-mpxv-nf/store_dir_cladeii_testrun_mpox_amplicon_minion_yale-mpox-2000" \
    --override_model r1041_e82_400bps_hac_v520 \
    --validate_params false \
    --clade cladeii
```

The backslash (`\`) at the end of each line tells Bash that the command continues on the next line.

### What do the main parameters mean?

| Parameter | Purpose |
| --- | --- |
| `-r 2.1.0` | Runs a fixed version of the workflow, improving reproducibility. |
| `-process.executor local` | Runs Nextflow processes directly on the local computer. |
| `--fastq` | Specifies the directory containing the ONT FASTQ reads. |
| `--scheme_version` | Specifies the primer scheme and reference used for genome reconstruction. |
| `--out_dir` | Directory in which the final workflow results are written. |
| `--store_dir` | Directory used to store workflow resources such as downloaded primer schemes. |
| `--override_model` | Specifies the ONT/Clair3 model corresponding to the model used for basecalling. |
| `--validate_params false` | Disables Nextflow parameter-schema validation for this run. |
| `--clade` | Specifies the MPXV clade used by downstream MPXV-specific analysis. |

---

## Check the output directory

After the workflow completes, inspect the output directory:

```bash
ls "$PROJECT_DIR/analysis/02_artic-mpxv-nf/testrun_mpox_amplicon_minion_yale-mpox-2000_cladeii"
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

The most important files for the next steps are:

- **`<sample>.consensus.fasta`** — reconstructed MPXV consensus genome
- **`<sample>.amplicon_depths.tsv`** — sequencing depth for individual amplicons; useful for identifying low-coverage or failed amplicons
- **`<sample>.primertrimmed.rg.sorted.bam`** — mapped reads after primer trimming
- **`<sample>.normalised.named.vcf.gz`** — called sequence variants used during consensus reconstruction

---

## 💡 Questions

1. Why is it useful to specify a fixed workflow version with `-r`?
2. What is the difference between `--out_dir` and `--store_dir`?
3. Where can you find information about the basecalling model in an ONT FASTQ file?
4. How would you translate `dna_r10.4.1_e8.2_400bps_hac@v5.2.0` into the `--override_model` value used in this tutorial?
5. Why must `--override_model` match the model used to basecall the reads?
6. What could happen if the wrong primer scheme or reference clade were selected?
7. Why might one amplicon have substantially lower depth than neighboring amplicons?
8. Which output file contains the reconstructed genome sequence?
9. Which output would you inspect to identify potential amplicon dropouts?

---

## Summary

In this tutorial, you:

- ran `artic-mpxv-nf` using Nextflow
- selected the appropriate primer scheme and MPXV clade
- identified the ONT basecalling model from FASTQ headers
- selected the corresponding `--override_model` value
- defined separate result and workflow-resource directories
- learned how the major Nextflow options and pipeline parameters affect the analysis
- identified key output files for downstream genome quality assessment

The reconstructed consensus genomes can subsequently be used for **quality assessment, lineage assignment, comparative genomics, and phylogenetic analysis**.
