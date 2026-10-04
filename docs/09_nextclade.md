---
title: Nextclade
nav_order: 9
nav_exclude: false
has_children: false
has_toc: true
permalink: /nextclade/
---
# Mpox genome analysis with Nextclade

{: .objectives }
By the end of this tutorial, you will be able to:
- Analyze MPXV consensus genomes with Nextclade Web using the `nextstrain/mpox/all-clades` dataset.
- Interpret key Nextclade results, including clade, outbreak lineage, sequence quality, mutations, and missing data.
- **Optional**: Run the same analysis with Nextclade CLI and locate the tabular results file.

---

`Nextclade` compares viral consensus genomes with a reference dataset. It can assign clades and lineages, identify mutations, check sequence quality, and place genomes in a phylogenetic tree. For mpox virus (MPXV), this tutorial uses the `nextstrain/mpox/all-clades` dataset.

---

## 1. Analyze genomes with Nextclade Web

1. Open [Nextclade Web](https://clades.nextstrain.org/).
2. Drag and drop one or more `.consensus.fasta` files from the previous `artic-mpxv-nf` tutorial into the input area. You can also select the files with **Select files**.
3. Select the MPXV all-clades dataset (`nextstrain/mpox/all-clades`), then click **Run**.
4. Review the results table, especially the clade, outbreak lineage, and overall quality status. Download the table if you need to keep the results.

{: .note }
The analysis runs in your browser, so sequence data stay on your computer. Internet access is needed to load Nextclade and its dataset.

---

## 2. Optional: Run Nextclade CLI

For larger or repeatable analyses, run the command-line version from the workshop project directory. Update the input directory if your `artic-mpxv-nf` results are stored elsewhere.

```bash
nextclade run \
  --dataset-name "nextstrain/mpox/all-clades" \
  --output-all "analysis/03_nextclade" \
  analysis/02_artic-mpxv-nf/testrun_mpox_amplicon_minion_yale-mpox-2000_cladeii/*.consensus.fasta
```

Nextclade downloads the dataset automatically. The main tabular result is `analysis/03_nextclade/nextclade.tsv`.
