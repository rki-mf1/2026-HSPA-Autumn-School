---
title: Linux - Mini Challenges
parent: Linux
nav_order: 3
nav_exclude: false
permalink: /linux_mini_challenges/
---

# Mini Challenges: Navigation and File Management

---

These challenges help you practice the commands from [Linux - Navigation and File Management](../linux_navigation/). Try to solve them without looking at the solution first.

---

## 1. Mini challenge: Path explorer
Start in your `scratch` directory and do the following:

1. move into the `data` directory using a **relative** path
2. print your current working directory
3. without leaving `data`, list the contents of the parent directory with details and human-readable file sizes
4. jump back to the directory you were in before
5. go to your home directory
6. go back into `scratch` using an **absolute** path and check where you are

One possible solution:

```bash
# Start
cd ~/2026-HSPA-Autumn-School/scratch

# 1.
cd ../data

# 2.
pwd

# 3.
ls -lh ..

# 4.
cd -

# 5.
cd ~

# 6.
cd /home/$USER/2026-HSPA-Autumn-School/scratch
pwd
```

---

## 2. Mini challenge: Build a project structure
In your `scratch` directory, build the following structure:

```
sequencing_project/
├── README.md
├── data/
│   ├── raw/
│   │   ├── barcode01.fastq
│   │   └── barcode02.fastq
│   └── metadata/
├── results/
│   └── qc/
└── logs/
```

1. create the directory `sequencing_project` and move into it
2. create the directories `data`, `results` and `logs`
3. create the subdirectories `raw` and `metadata` inside `data`, and `qc` inside `results`
4. create two empty files `barcode01.fastq` and `barcode02.fastq` inside `data/raw`
5. write one line into `README.md` describing the project (e.g. "Mpox sequencing run, October 2026")
6. list the contents of `data/raw` and show the content of `README.md`

{: .tip}
> You can give `mkdir` and `touch` more than one argument at once, separated by spaces.

One possible solution:

```bash
# 1.
cd ~/2026-HSPA-Autumn-School/scratch
mkdir sequencing_project
cd sequencing_project

# 2.
mkdir data results logs

# 3.
mkdir data/raw data/metadata results/qc

# 4.
touch data/raw/barcode01.fastq data/raw/barcode02.fastq

# 5.
echo "Mpox sequencing run, October 2026" > README.md

# 6.
ls data/raw
cat README.md
```

---

## 3. Mini challenge: Edit, back up and clean up
Continue in `sequencing_project` from mini challenge 2:

1. create a file `samples.txt` in `data/metadata` with the line `barcode01 sample_A`
2. append two more lines: `barcode02 sample_B` and `barcode03 sample_C`
3. open `samples.txt` in `nano`, add the line `barcode04 sample_D`, save and exit
4. show only the last two lines of `samples.txt`
5. make a copy of the whole `metadata` directory called `metadata_backup` inside `data`
6. move `samples.txt` from `metadata_backup` into `logs` and rename it to `samples_old.txt` in one step
7. remove the `metadata_backup` directory
8. list the contents of `data` and `logs` to check your result

{: .tip}
> For step 4, check `tail --help` to find out how to show a specific number of lines.

One possible solution:

```bash
# Start
cd ~/2026-HSPA-Autumn-School/scratch/sequencing_project

# 1.
echo "barcode01 sample_A" > data/metadata/samples.txt

# 2.
echo "barcode02 sample_B" >> data/metadata/samples.txt
echo "barcode03 sample_C" >> data/metadata/samples.txt

# 3.
nano data/metadata/samples.txt

# 4.
tail -n 2 data/metadata/samples.txt

# 5.
cp -r data/metadata data/metadata_backup

# 6.
mv data/metadata_backup/samples.txt logs/samples_old.txt

# 7.
rm -r data/metadata_backup

# 8.
ls data
ls logs
```
