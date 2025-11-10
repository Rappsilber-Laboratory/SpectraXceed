# MetaXtract Workflow

## Overview

This workflow automates downloading RAW files from the PRIDE
archive and analyzing them with MetaXtract.

## Features

-   Automatically downloads `.raw` files from the PRIDE FTP archive\
-   Selects newest N files per month\
-   Runs MetaXtract on every downloaded file\
-   Stores MetaXtract `_info_*.txt` logs\

## Installation

Create and activate a dedicated environment:

    `python -m venv .snakemake-env`

Activate it:

    `.\.snakemake-env\Scripts\activate`

Install dependencies:

    `pip install "pulp==2.7.0"`

Snakemake will run using this environment.

## Configuration

All parameters are defined in `config.yaml`.

Example:
```sh
    metaxtract_exe: "bin/MetaXtract.v.0.5.1.exe"

    pride:
      url: "ftp://ftp.pride.ebi.ac.uk/pride/data/archive"
      year: 2025
      month: 1
      max_files: 2
      copy_dir: "results/data"

    output_dir: "results"
    metaxtract_config: "metaxtract_config.yaml"

    options:
      file_based_details: true
      ms_method: true
      lc_method: true
      graphical_representation: false
      complete_ms2: false
      complete_ms1: false
```
### MetaXtract Option Mapping

  YAML key                   MetaXtract flag
  -------------------------- ------------------------------
  file_based_details         `--file-based-details`
  ms_method                  `--ms-method`
  lc_method                  `--lc-method`
  graphical_representation   `--graphical-representation`
  complete_ms2               `--complete-ms2`
  complete_ms1               `--complete-ms1`

## Running the Workflow

Execute:

    `snakemake --core 8`

The pipeline will:

1.  Locate the PRIDE folder for the configured year/month\
2.  Download `.raw` files (limit via `max_files`)\
3.  Run MetaXtract for each file\
4.  Save all CSVs + logs\
5.  Move final `_info_*.txt` into `results/log/`\
6.  Delete RAWs from `results/data/`\
7.  Generate timing statistics

## Output Structure
```sh
    results/
      data/              RAWs (deleted after processing)
      metaxtract/        MetaXtract output folders
      log/               *_info_*.txt files
      runtime/           
          _start.timestamp
          benchmark_download.tsv
          benchmark_analyze_<sample>.tsv
          runtime_summary.txt
```
## Runtime Summary

The file `results/runtime/runtime_summary.txt` contains:

-   Total pipeline runtime\
-   Download runtime\
-   Total analysis time\
-   Mean analysis time per file\
-   Number of analyzed files