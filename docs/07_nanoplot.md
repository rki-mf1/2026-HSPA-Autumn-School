---
title: NanoPlot
nav_order: 7
nav_exclude: false
has_children: true
has_toc: false
permalink: /nanoplot/
---

# Quality control of ONT reads with `NanoPlot`

## 🎯 Learning goals

By the end of this practical, you should be able to:

- run `NanoPlot` on Oxford Nanopore FASTQ files
- inspect read length and read quality distributions
- interpret basic ONT read quality statistics
- decide whether the reads are suitable for downstream analysis

---

## Overview

`NanoPlot` is a quality-control and visualization tool for Oxford Nanopore sequencing data.

In this practical, we will inspect the mpox amplicon reads from **barcode08** before genome reconstruction with `artic-mpxv-nf`.

`NanoPlot` is used here for **inspection only**. We will not filter the reads before running `artic-mpxv-nf`, because the ARTIC workflow performs amplicon-specific read filtering during the analysis.

The input data are located in:

```text
~/2026-HSPA-Autumn-School/data/testrun_mpox_amplicon_minion_yale-mpox-2000/fastq_pass/barcode08/
```

---

## 1. Activate the Conda environment

Create and activate a Conda environment containing `NanoPlot`:

```bash
conda create -n nanoplot -c conda-forge -c bioconda nanoplot -y

conda activate nanoplot
```

Check that `NanoPlot` is available:

```bash
NanoPlot --version
```

---

## 2. Create an output directory

Move to the workshop directory:

```bash
cd ~/2026-HSPA-Autumn-School
```

Create an output directory:

```bash
mkdir -p analysis/nanoplot/barcode08
```

---

## 3. Run `NanoPlot`

Run `NanoPlot` directly on the FASTQ files from **barcode08**:

```bash
NanoPlot \
    --fastq data/testrun_mpox_amplicon_minion_yale-mpox-2000/fastq_pass/barcode08.excluding_human.fastq.gz \
    --outdir analysis/nanoplot/barcode08 \
    --threads 4
```

Important options:

| Option | Meaning |
|---|---|
| `--fastq` | Input ONT FASTQ file(s) |
| `--outdir` | Directory for the NanoPlot results |
| `--threads` | Number of CPU threads to use |

`NanoPlot` accepts multiple FASTQ files, so the `*.fastq.gz` wildcard combines all FASTQ files belonging to `barcode08` for the QC summary.

---

## 4. Inspect the results

List the generated files:

```bash
ls -lh analysis/nanoplot/barcode08/
```

Important outputs include:

```text
NanoPlot-report.html
NanoStats.txt
```

Open `NanoPlot-report.html` in a web browser and inspect the plots and statistics.

Pay particular attention to:

- number of reads
- total number of bases
- mean and median read length
- read N50
- mean and median read quality
- read length distribution
- relationship between read length and read quality

{: .note }
The Yale mpox primer scheme produces amplicons of approximately **2 kb**. Therefore, many reads are expected to cluster around the approximate amplicon length. Very short reads may represent incomplete products, while unusually long reads may represent chimeric or concatenated molecules.

---

## 💬 Discussion

Use the NanoPlot report to answer:

1. How many reads were generated for `barcode08`?
2. What is the median read quality?
3. What is the median read length?
4. Is the read-length distribution consistent with approximately 2-kb amplicons?
5. Are there many very short or unusually long reads?
6. Based on the QC results, would you proceed with genome reconstruction?

---

## 5. Run `NanoPlot` on your own sequencing data 

Adapt the commands above and run `NanoPlot` on the FASTQ files you generated during the previous week of the HSPA Autumn School.

---

## 6. Continue with `artic-mpxv-nf`

For this workflow, we will **not create a filtered FASTQ file**.

The original reads from:

```text
/dataset/testrun_mpox_amplicon_minion_yale-mpox-2000/fastq_pass/
```

will be used directly as input for `artic-mpxv-nf`.

The ARTIC workflow performs amplicon-aware filtering based on the selected primer scheme before genome reconstruction.

---

## 📌 Summary

In this practical, you used `NanoPlot` to inspect the quality of ONT mpox amplicon sequencing reads.

You examined:

- read quality
- read length
- read N50
- read-length distribution
- whether the reads match the expected amplicon size

The reads will next be used directly for genome reconstruction with `artic-mpxv-nf`.
