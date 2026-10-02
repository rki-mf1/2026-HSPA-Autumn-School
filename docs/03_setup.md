---
title: Setup
nav_order: 3
nav_exclude: false
has_children: false
has_toc: false
permalink: /setup/
---

# Setup

*Using a workshop laptop?*
Your laptop has already been set up for the workshop. Before we start, please check that the required software and data are available. After the workshop, you can use this guide to repeat the workflow on your own machine.

*Using your own laptop?*
Please follow this guide to set up your working environment.

**Tip**: To paste into the terminal, press Ctrl + Shift + V (Linux) or Cmd + V (macOS).

## Wifi
Please connect to the Wifi. 

* Wifi name week 1: ZIG4_Lab
* Wifi name week 2: Public

The Wifi password will be communicated by your facilitators.

## Workshop directory
Open a terminal and create the workshop directory:
```bash
mkdir 2026-HSPA-Autumn-School
```

## Editor
[VSCodium](https://vscodium.com/) is a free and open-source code editor. It is built from the same source code as Microsoft's Visual Studio Code, but without Microsoft's branding and telemetry. You can use it to browse and edit files, view scripts and configuration files, and run commands in a built-in terminal, all in one window.

In this workshop, we use VSCodium to open the workshop repository, look at the pipeline files and inspect results. You can use another text editor if you prefer, but VSCodium makes it easier to follow along.

Add the GPG key of the VSCodium repository:
```bash
wget -qO - https://gitlab.com/paulcarroty/vscodium-deb-rpm-repo/raw/master/pub.gpg \
    | gpg --dearmor \
    | sudo dd of=/usr/share/keyrings/vscodium-archive-keyring.gpg
```

Add the repository:
```bash
echo -e 'Types: deb\nURIs: https://download.vscodium.com/debs\nSuites: vscodium\nComponents: main\nArchitectures: amd64 arm64\nSigned-by: /usr/share/keyrings/vscodium-archive-keyring.gpg' \
    | sudo tee /etc/apt/sources.list.d/vscodium.sources
```

On Ubuntu 23.10 or older, use this command instead:
```bash
echo 'deb [arch=amd64,arm64 signed-by=/usr/share/keyrings/vscodium-archive-keyring.gpg] https://download.vscodium.com/debs vscodium main' \
    | sudo tee /etc/apt/sources.list.d/vscodium.list
```

Install VSCodium:
```bash
sudo apt update
sudo apt install -y codium
```

Open VSCodium, open the directory `2026-HSPA-Autumn-School` in VSCodium (Open Folder...) and continue the set up there.

To open a terminal in VSCodium, select Terminal -> New Terminal in the top bar.

## Data
From now on, we work in the **VSCodium terminal**.

Create target directory:
```bash
mkdir ~/Documents/data
```

Go to directory:
```bash
cd ~/Documents/data
```

The toy dataset used for this training is available from [Zenodo](https://zenodo.org/records/22815134).

This deposit contains a subset of `fastq_pass` reads from Oxford Nanopore MinION sequencing of two cell-culture-derived mpox virus samples: clade I (`barcode07`) and clade II (`barcode08`). It is provided for reproducibility, pipeline testing, and hands-on tutorial use. It is not the complete sequencing run or a comprehensive research dataset.

Amplicons were generated using the [`yale-mpox/2000/v1.0.0`](https://labs.primalscheme.com/detail/yale-mpox/2000/v1.0.0/?q=mpox) primer scheme described by [Chen et al. (2023)](https://journals.plos.org/plosbiology/article?id=10.1371/journal.pbio.3002151).

Sequencing was performed on an FLO-MIN114 flow cell using **high-accuracy basecalling**, with barcode trimming enabled and a minimum quality score of Q9.

The reads were taxonomically classified with [Kraken2](https://github.com/DerrickWood/kraken2) using the `PlusPF` database. Reads classified as human were removed using [`extract_kraken_reads.py`](https://github.com/jenniferlu717/KrakenTools/blob/master/extract_kraken_reads.py). The deposited files therefore represent the non-human read subset.

Download data from Zenodo:
```bash
wget https://zenodo.org/records/22815134/files/testrun_mpox_amplicon_minion_yale-mpox-2000.tar.gz
```
While the data is being downloaded, you can continue with the tutorial in a separate terminal.

Once the download is finished, extract the archive:
```bash
tar -xvzf testrun_mpox_amplicon_minion_yale-mpox-2000.tar.gz
```

After extraction, the directory should contain one fastq_pass directory with two compressed FASTQ files:
```bash
testrun_mpox_amplicon_minion_yale-mpox-2000/
└── fastq_pass/
    ├── barcode07.excluding_human.fastq.gz
    └── barcode08.excluding_human.fastq.gz
```

Make sure you are in the right directory:
```bash
cd ~/Documents/data
```

Verify data download:
```bash
ls testrun_mpox_amplicon_minion_yale-mpox-2000/fastq_pass
```

## Docker
[Docker](https://www.docker.com/) is a tool for running software in *containers*. A container bundles a program together with everything it needs to run, such as libraries, dependencies and the right versions of each. This means a tool behaves the same way on every computer, regardless of what else is installed.

In this workshop, we use Docker together with Nextflow. Each step of the pipeline runs inside its own container, which Nextflow downloads and starts automatically. You don't need to install the individual bioinformatics tools yourself.

Check if you have docker installed on your machine:
```bash
docker run hello-world
```

Remove any conflicting old packages. It is fine if `apt` reports that none of them are installed.
```bash
for pkg in docker.io docker-doc docker-compose docker-compose-v2 podman-docker containerd runc; do
  sudo apt remove -y $pkg
done
```

Install the prerequisites and add Docker's official GPG key:
```bash
sudo apt update
sudo apt install -y ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc
```

Add the Docker repository:
```bash
sudo tee /etc/apt/sources.list.d/docker.sources <<EOF
Types: deb
URIs: https://download.docker.com/linux/ubuntu
Suites: $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}")
Components: stable
Signed-By: /etc/apt/keyrings/docker.asc
EOF
```

Install Docker Engine:
```bash
sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

Start Docker and enable it at boot:
```bash
sudo systemctl enable --now docker
```

Allow your user to run Docker without `sudo`. Nextflow calls Docker as your normal user, so this step is required.
```bash
sudo usermod -aG docker $USER
```

**Log out and log back in** for the group change to take effect.

Verify the installation:
```bash
docker run hello-world
```

If you see `Hello from Docker!`, Docker is working.

## Git
[Git](https://git-scm.com/) is a version control system. It keeps track of changes to files over time, so you can see what changed, when, and by whom, and go back to earlier versions if needed. Git is widely used to share code, and platforms such as [GitHub](https://github.com/) host Git repositories online.

Check if you have Git installed:
```bash
git --version
```

Install Git with `apt`:
```bash
sudo apt update
sudo apt install -y git
```

Verify the installation:
```bash
git --version
```

## Miniforge
[Miniforge](https://github.com/conda-forge/miniforge) provides `conda` and `mamba` through the community-maintained `conda-forge` channel. It is a fully open-source distribution and avoids reliance on Anaconda's default package repositories, whose use may be subject to commercial licensing terms.

Check if you already have conda or mamba:
```bash
conda --version
mamba --version
```

If you do not have it installed, you can install miniforge.

Download the installer:
```bash
wget -O Miniforge3.sh \
  "https://github.com/conda-forge/miniforge/releases/latest/download/Miniforge3-$(uname)-$(uname -m).sh"
```

Run the installer:
```bash
bash Miniforge3.sh
```

Follow the prompts and answer `yes` when asked to initialize conda. 

**Close and reopen the terminal** for the changes to take effect.

Remove the installer file:
```bash
rm Miniforge3.sh
```

Verify the installation:
```bash
conda --version
```

Add the `bioconda` channel after `conda-forge`. The `--append` option places a channel at the bottom of the list, so `conda-forge` stays the first priority.
```bash
conda config --append channels bioconda
```

Verify the channel order:
```bash
conda config --show channels
```

The output should list `conda-forge` first and `bioconda` second:
```
channels:
  - conda-forge
  - bioconda
```
