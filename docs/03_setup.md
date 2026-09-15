---
title: Setup
nav_order: 3
nav_exclude: false
has_children: false
has_toc: false
permalink: /setup/
---

# Recommended setup

## Install Miniforge

[Miniforge](https://github.com/conda-forge/miniforge) provides `conda` and `mamba` through the community-maintained `conda-forge` channel. It is a fully open-source distribution and avoids reliance on Anaconda's default package repositories, whose use may be subject to commercial licensing terms.

Download and run the installer:

```bash
wget -O Miniforge3.sh \
  "https://github.com/conda-forge/miniforge/releases/latest/download/Miniforge3-$(uname)-$(uname -m).sh"

bash Miniforge3.sh
```

Follow the prompts, allow Conda to be initialised, and reopen the terminal.

Check the installation:

```bash
conda --version
mamba --version
```