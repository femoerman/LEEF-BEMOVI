R Notebook
================

<!-- README.md is generated from README.Rmd. Please edit that file -->

-----

[![Coverage
status](https://codecov.io/gh/rkrug/LEEF.Data/branch/master/graph/badge.svg)](https://codecov.io/github/rkrug/LEEF.Data?branch=master)
[![lifecycle](https://img.shields.io/badge/lifecycle-experimental-orange.svg)](https://www.tidyverse.org/lifecycle/#experimental)

-----

# Info

This repo contains an R package for accessing (and possibly even
storing), checking new to be iported data, as well as the control center
on github (referred to as the **infrastructure** of this repo) to
coordinate the import and processing of data.

It contains an R package with

  - R functions to access all data generated, if it is a **master**
    repo,
  - R functions to access a subset of the data in the master repo,
    e.g. an experiment, if it is a **child** repo

while the data is either stored locally (as sqlite database in
`inst/data`) or remotely.

The repo’s function is to

1.  request the import of new data
2.  request new foreacasts

# Installation of R package

## Prerequisites

As the package is only on github, you need `devtools` to install it
easily as a prerequisite

``` r
install.packages("devtools")
```

## Installation of the package

``` r
devtools::install_github("rkrug/LEEF.Data")
```

## Update of the data

If the repo contains the data (in `inst/data/` folder in sqlite format),
the package needs to be updated when new data is available via

``` r
devtools::install_github("rkrug/LEEF.Data")
```

If the data is stored externally, the data retreival functions will
automatically retreive the newest data.

# Usage of package

## Functions to access the data

This package provides functions for **read-only** acces to the data as
well as functions to check the integrity if the data. The functions are

  - `read_data()`
  - **<span style="color:red">more functions are needed depending on the
    type of the data and will be added later</span>**
  - …

# Infrastructure

## Configuration files of the infrastructure

### `config.yml`

In the `config.yml` file are all configuration options of the
infrastructure. The example one looks as follow:

``` r
default:
  maintainer:
    name: Max Mustermann
    email: Max@musterfabrik.example
  description: This is my fantastic repo
               with lots of data.

master:
  doi: FALSE
  tts: TRUE
  data:
    backend:
      mysql:
        Database: "[database name]"
        UID: "[user id]"
        PWD: "[pasword]"
        Port: 3306

LEEF:
  doi: TRUE
  tts: TRUE
  data:
    backend:
      sqlite:
        folder: inst/extdata
```

The top levels are different repo names (here `master` and `LEEF`) and
default values for all the repos (`default`).

It has the following keywords: - `default` contaiuns self-explanatory
values - `doi` if TRUE, will request DOI after successfull commit -
`tts` if TRUE, will request Trusted Time Stamp of repo after successfull
commit - `data` contains info about the data storage - `backend` the
name of the backend and connection info -
<span style="color:green">others to come</span>

This config file should be the same for all repos in a tree, but it is
not necessary.

### `CONFIG.NAME`

This file contains the name of the configuration for this repo. in this
example `master`. So by changiong the content of this file to `LEEF`,
this repo becomes a `LEEF` repo.

## Import of new data

Before new data can be imported, this repo needs to be forked. Once
forked, all user interaction takes place via this fork and pull requests
to this repo. This fork needs to be cloned to a local computer.

An import of new data follows the following steps:

1.  Create a new branch on the local fork and check it out.
2.  Add all new data files to the directory `inst/new_data/`. The data
    has to follow the following rules:
      - the data has to be in a common format, e.g. `.feather` - r+w
        from R, python , Julia, ( <span style="color:red">we have to
        decide on the format; I would prefer `feather`, but is it
        stable?</span> )
      - the name has to reflect the name of the table the data needs to
        be imported to
      - **<span style="color:red">metadata do be added in additional
        file?</span>**
      - **<span style="color:red">more?</span>**
3.  commit these changes to the local fork and push to github fork
4.  create pull request from this repo
5.  an automatic check of the package will be triggered using Travis-ci,
    which will test the data and not the units if the `inst/newdata`
    directory is not empty
6.  From now on in the `after_success section`
7.  after a succerssfull testhat test, the data will be imported and the
    database updated
8.  delete all imported files; if a file remains, raise error
9.  update version in DESCRIPTION
10. the main vignette will be updated with date and (hopefully)
    timestamp of the data
11. changes will be commited to repo with
12. quit

As soon as this repo receives a pull request, it initiates the import
via a Travis job. This importing is handled within the package
`LEEF.Processing` and can be seen in detail in the package
documantation.

For relevance here are two actions:

1.  `LEEF.Processing` does commit the imported data to this repository
    or to the external database.

# Package structure

![](README_files/figure-gfm/leef.processing.content-1.png)<!-- -->

# **<span style="color:red">TODO</span>**

  - request DOI and TTS only for sqlite database or snapshot of external
    database - how can I do this?
  - Which data needs to be archived from which source?
  - how to store metdata?
  - how to store experimental conditions?
