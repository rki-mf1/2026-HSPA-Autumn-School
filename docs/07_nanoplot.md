---
title: NanoPlot
nav_order: 7
nav_exclude: false
has_children: true
has_toc: false
permalink: /nanoplot/
---

# Quality control of ONT reads with `NanoPlot`

{: .objectives }
> By the end of this practical, you should be able to:
>
> - run `NanoPlot` on Oxford Nanopore FASTQ files
> - inspect read length and read quality distributions
> - interpret basic ONT read quality statistics
> - decide whether the reads are suitable for downstream analysis

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
We are now working in the project folder, not in `scratch/` anymore.

Navigate to your project folder:
```bash
cd ~/2026-HSPA-Autumn-School
```

Create directories:
```bash
mkdir -p data/raw data/metadata
```

Activate the `NanoPlot` environment:
```bash
conda activate nanoplot
```

Copy both toy FASTQ files (barcode 07 & 08):
```bash
cp ~/Documents/data/testrun_mpox_amplicon_minion_yale-mpox-2000/fastq_pass/*.fastq.gz data/raw
```

Create your output directory:
```bash
mkdir -p analyses/01_nanoplot/barcode08
```

---

## 2. Inspect the data

View the whole file content without wrapped lines:
```bash
less -S data/raw/barcode08.excluding_human.fastq.gz
```

... with lines wrapped:
```bash
less data/raw/barcode08.excluding_human.fastq.gz
```

---

## 3. Run `NanoPlot` on toy data
Make sure you are in your project folder:
```bash
cd ~/2026-HSPA-Autumn-School
```

Run `NanoPlot` on the FASTQ file from **barcode08**:
```bash
NanoPlot \
    --fastq data/raw/barcode08.excluding_human.fastq.gz \
    --title "Barcode 08 (raw)" \
    --prefix barcode08_raw_ \
    --threads 8 \
    --outdir analyses/01_nanoplot/barcode08
```

{: .note }
The command is split into individual lines per option and argument for readability reasons. Alternatively, you can run a one-liner.

Run `NanoPlot` directly on the FASTQ file from **barcode08**:

```bash
NanoPlot --fastq data/raw/barcode08.excluding_human.fastq.gz --title "Barcode 08 (raw)" --prefix barcode08_raw_ --N50 --threads 4 --outdir analyses/01_nanoplot/barcode08
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
ls -lh analyses/01_nanoplot/barcode08/
```

Important outputs include:

```text
barcode08_raw_NanoPlot-report.html
barcode08_raw_NanoStats.txt
```

### 5. Interpret the report
Open the `NanoPlot` report in VScodium.

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

{: .discussion }
> Use the NanoPlot report to answer:
>
> 1. How many reads were generated for `barcode08`?
> 2. What is the median read quality?
> 3. What is the median read length?
> 4. Is the read-length distribution consistent with approximately 2-kb amplicons?
> 5. Are there many very short or unusually long reads?
> 6. Based on the QC results, would you proceed with genome reconstruction?

---

## 5. Copy FASTQ files

Copy the FASTQ files from the USB drive to your laptop>
```bash
# Group A - barcode19
cp -r /media/$USER/F0EE-30D9/HSPA\ Autumn\ School\ 2026/ONT\ Data/HSPA_MPXV_GROUP_A/clade1_2/20261007_1823_MD-103422_FBH77133_714d4c23/fastq_pass/barcode19 data/raw/
Group A - barcode 20
cp -r /media/pluto/F0EE-30D9/HSPA\ Autumn\ School\ 2026/ONT\ Data/HSPA_MPXV_GROUP_A/clade1_2/20261007_1823_MD-103422_FBH77133_714d4c23/fastq_pass/barcode20 data/raw/

Group B - barcode 2
cp -r /media/$USER/F0EE-30D9/HSPA\ Autumn\ School\ 2026/ONT\ Data/HSPA_MPXV_GROUP_B/clade_1_2/20261008_1744_MD-103422_FBH77133_e9b0c176/fastq_pass/barcode22 data/raw/
Group B - barcode 23
cp -r /media/$USER/F0EE-30D9/HSPA\ Autumn\ School\ 2026/ONT\ Data/HSPA_MPXV_GROUP_B/clade_1_2/20261008_1744_MD-103422_FBH77133_e9b0c176/fastq_pass/barcode23 data/raw/
```

Verify:
```bash
ls -lh data/raw
```

Concatenate the FASTQ files for each barcode to a single FASTQ file:
```bash
cat data/raw/barcode19/*.fastq.gz > data/raw/barcode19_merged.fastq.gz
cat data/raw/barcode20/*.fastq.gz > data/raw/barcode20_merged.fastq.gz
cat data/raw/barcode22/*.fastq.gz > data/raw/barcode22_merged.fastq.gz
cat data/raw/barcode23/*.fastq.gz > data/raw/barcode23_merged.fastq.gz
```

Now, run `NanoPlot` on the barcode you generated during the previous week of the HSPA Autumn School. Adapt the command accordingly.

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
