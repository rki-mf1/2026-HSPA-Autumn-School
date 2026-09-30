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

In this practical, we will inspect the MPXV amplicon reads from **barcode08** before genome reconstruction with `artic-mpxv-nf`. Afterwards, you will repeat the same steps on your own sample.

`NanoPlot` is used here for **inspection only**. We will not filter the reads before running `artic-mpxv-nf`, because the ARTIC workflow performs amplicon-specific read filtering during the analysis.

The input data are located in:

```text
~/Documents/data/testrun_mpox_amplicon_minion_yale-mpox-2000/fastq_pass
```

---

## 1. Setup the working environment
Navigate to your project folder:
```bash
cd ~/2026-HSPA-Autumn-School
```

Create a Conda environment and install `NanoPlot`:
```bash
conda create -p envs/nanoplot nanoplot -y
```

Activate the environment:
```bash
conda activate envs/nanoplot
```

Check that `NanoPlot` is available:
```bash
NanoPlot --version
```

Copy both FASTQ files (barcode 07 & 08):
```bash
cp ~/Documents/data/testrun_mpox_amplicon_minion_yale-mpox-2000/fastq_pass/*.fastq.gz data/raw
```

Create a Conda environment and install `NanoPlot`:
```bash
conda create -p envs/nanoplot nanoplot -y
```

Create your output directory:
```bash
mkdir -p analysis/01_nanoplot/barcode08
```

---

## 2. Inspect the data
View the whole file without cutting lines:
```bash
less -S data/raw/barcode08.excluding_human.fastq.gz
```

View the whole with lines cut:
```bash
less data/raw/barcode08.excluding_human.fastq.gz
```

---

## 3. Run `NanoPlot`
Navigate to your project folder:
```bash
cd ~/2026-HSPA-Autumn-School
```

Run `NanoPlot` directly on the FASTQ files from **barcode08**:
```bash
NanoPlot \
    --fastq data/testrun_mpox_amplicon_minion_yale-mpox-2000/fastq_pass/barcode08.excluding_human.fastq.gz \
    --title "Barcode 08 (raw)" \
    --prefix barcode08_raw_ \
    --N50 \
    --threads 4 \
    --outdir analysis/01_nanoplot/barcode08
```

Important options:

| Option | Meaning |
|---|---|
| `--fastq` | Input ONT FASTQ file(s) |
| `--title` | Title shown on top of every plot, useful to tell samples apart |
| `--prefix` | Prefix added to all output file names, so results from different runs don't overwrite each other |
| `--N50` | Marks the N50 read length in the read length histograms |
| `--threads` | Number of CPU threads to use |
| `--outdir` | Directory for the NanoPlot results |

---

## 4. Inspect the output files
List the generated files:
```bash
ls -lh analysis/01_nanoplot/barcode08/
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

The raw reads will be used directly as input for `artic-mpxv-nf`.

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
