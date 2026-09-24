---
title: Dataset
nav_order: 5
nav_exclude: false
has_children: false
has_toc: true
permalink: /dataset/
---

# For participants using workshop laptops

You do not need to download the toy dataset. It is already available on your laptop at:

```text
~/Documents/data/testrun_mpox_amplicon_minion_yale-mpox-2000
```

# For participants using their own laptops

The toy dataset used for this training is available from [Zenodo](https://zenodo.org/records/22815134).

This deposit contains a subset of `fastq_pass` reads from Oxford Nanopore MinION sequencing of two cell-culture-derived mpox virus samples: clade I (`barcode07`) and clade II (`barcode08`). It is provided for reproducibility, pipeline testing, and hands-on tutorial use. It is not the complete sequencing run or a comprehensive research dataset.

Amplicons were generated using the [`yale-mpox/2000/v1.0.0`](https://labs.primalscheme.com/detail/yale-mpox/2000/v1.0.0/?q=mpox) primer scheme described by [Chen et al. (2023)](https://journals.plos.org/plosbiology/article?id=10.1371/journal.pbio.3002151).

Sequencing was performed on an FLO-MIN114 flow cell using **high-accuracy basecalling**, with barcode trimming enabled and a minimum quality score of Q9.

The reads were taxonomically classified with [Kraken2](https://github.com/DerrickWood/kraken2) using the `PlusPF` database. Reads classified as human were removed using [`extract_kraken_reads.py`](https://github.com/jenniferlu717/KrakenTools/blob/master/extract_kraken_reads.py). The deposited files therefore represent the non-human read subset.

Download the dataset to the workshop directory:

```bash
cd ~/2026-HSPA-Autumn-School/data

wget https://zenodo.org/records/22815134/files/testrun_mpox_amplicon_minion_yale-mpox-2000.tar.gz
```

Extract the archive:

```bash
tar -xvzf testrun_mpox_amplicon_minion_yale-mpox-2000.tar.gz
```

After extraction, the directory should contain one `fastq_pass` directory with two compressed FASTQ files:

```text
testrun_mpox_amplicon_minion_yale-mpox-2000/
└── fastq_pass/
    ├── barcode07.excluding_human.fastq.gz
    └── barcode08.excluding_human.fastq.gz
```