# sv-gen

[![Build Status](https://travis-ci.org/GooglingTheCancerGenome/sv-gen.svg?branch=master)](https://travis-ci.org/GooglingTheCancerGenome/sv-gen)

Structural variants (SVs) are an important class of genetic variation implicated in a wide array of genetic diseases. _sv-gen_ is a Snakemake-based workflow to generate artificial short-read alignments based on a reference genome with(out) SVs. The workflow is easy to use and deploy on any Linux-based machine. In particular, the workflow supports automated software deployment, easy configuration and addition of new analysis tools as well as enables to scale from a single computer to different HPC clusters with minimal effort.

## Dependencies

-   [Python 3](https://www.python.org/)
-   [Conda](https://conda.io/)
-   [Snakemake](https://snakemake.readthedocs.io/) (5.10.0)
-   [Xenon CLI](https://github.com/NLeSC/xenon-cli) (3.0.4)
-   [jq](https://stedolan.github.io/jq/) (optional) - command-line JSON processor to parse job accounting info (see `xenon --json ...`)

The [workflow](/doc/sv-gen.svg) includes the following tools:

-   [SURVIVOR](https://github.com/fritzsedlazeck/SURVIVOR) (1.0.6)
-   [ART](https://www.niehs.nih.gov/research/resources/software/biostatistics/art/) (2016-06-05)
-   [BWA](https://github.com/lh3/bwa) (0.7.17)
-   [Samtools](https://github.com/samtools/samtools) (1.9)

**1. Clone this repo.**

```bash
git clone https://github.com/GooglingTheCancerGenome/sv-gen.git
cd sv-gen
```

**2. Install dependencies.**

```bash
# download Miniconda3 installer
wget https://repo.continuum.io/miniconda/Miniconda3-latest-Linux-x86_64.sh -O miniconda.sh
# install Conda (respond by 'yes')
bash miniconda.sh
# update Conda
conda update -y conda
# create & activate new env with installed deps
conda env create -n wf -f environment.yaml
conda activate wf
cd snakemake
```

**3. Configure the workflow.**

-   **config files**:
    -   [`analysis.yaml`](/snakemake/analysis.yaml) - analysis-specific settings
    -   [`environment.yaml`](/snakemake/environment.yaml) - software dependencies and versions

**4. Execute the workflow.**

```bash
# 'dry' run only checks I/O files
snakemake -np

# run the workflow locally
snakemake --use-conda
```

_Submit jobs to Grid Engine-based cluster_

```bash
snakemake --use-conda --latency-wait 30 --jobs \
--cluster 'xenon scheduler gridengine --location local:// submit --name smk.{rule} --inherit-env --max-run-time 5 --working-directory . --stderr stderr-%j.log --stdout stdout-%j.log' &>smk.log&
```

_Submit jobs to Slurm-based cluster_

```bash
snakemake --use-conda --latency-wait 30 --jobs \
--cluster 'xenon scheduler slurm --location local:// submit --name smk.{rule} --inherit-env --max-run-time 5 --working-directory . --stderr stderr-%j.log --stdout stdout-%j.log' &>smk.log&
```
