[![Travis-CI Build Status](https://travis-ci.org/terraref/tutorials.svg?branch=master)](https://travis-ci.org/terraref/tutorials)

# Tutorials


## An introduction to the use of TERRA REF data and software

This repository provides a set of tutorials that are divided by data types and use cases. 

In the repository, you will find three folders that contain examples of how to access data:

* traits
* sensors
* plantCV

Within each folder there are both R markdown and Jupyter notebooks. These describe different approaches to accessing data. These are intended to cover diverse use cases, and you will find information about accessing data from web interfaces but the primary focus is on accessing data using R, Python, SQL, and REST APIs. These are intended to provide quick-start introductions to access data along with computing environments required for further exploration. They are not intended to teach analyses, although some illustrative visualizations and statistical models are provided.

This is a work in progress, and an open source community that welcomes contributions in many forms. Please feel welcome to ask questions, provide suggestions or share analyses that may be of interest to others.


## Getting Started

### Requirements

All of the tutorials have been designed to work in the cloud and can be accessed using a web browser. Therefore, the _only technical requirements_ are:
* Web browser
* Internet connection

In addition, you will need to:
* Sign up as as a TERRA REF [Beta User by filling out this application](http://terraref.org/beta).
* Sign up for an account on the [TERRA REF Workbench](https://www.workbench.terraref.org), and wait for approval.

### Using the Workbench

The Workbench uses the National Data Service Labs Workbench (NDS Labs Workbench) software. The Workbench is a cloud analysis environment that minimizes and standardizes the software requirements so users can get started quickly. It also provides access to large files and databases that would otherwise be slow and impractical for users to download and store. 

Although we provide a few pre-configured computing environments, Workbench is designed to support any Docker container - that is, anything you can install on a linux computer can be used in the workbench - for an idea of what is possible, see the large number of options on [Docker Hub](https://hub.docker.com/explore/).

**To get started**, follow the [Workbench Quick Start](https://htmlpreview.github.io/?https://github.com/terraref/tutorials/blob/master/workbench/ndslabs_workbench_intro.html).

This will walk you through the process of getting started with the first tutorials on how to access data.


## To generate this documentation locally

If you would like to contribute to this documentation you can preview your 
changes by running the following command:

```bash
Rscript -e 'bookdown::render_book("index.Rmd")'
```

The output will be in the `docs` folder.

There is also a Dockerfile for convenience. It requires installing Docker on 
your computer:

- [Docker for Mac](https://download.docker.com/mac/stable/Docker.dmg)
- [Docker for Windows](https://download.docker.com/win/stable/Docker%20for%20Windows%20Installer.exe) 

Build the image:

```bash
docker build -t terraref-tutorials:local .
```

Run the container:

```bash
docker run --rm -p 3000:3000 --name tutorial-preview terraref-tutorials:local
```

This will generate the documentation and start a local web server to preview
your changes. Open the preview URL in your browser: <http://localhost:3000/>

Run `docker kill tutorial-preview` to kill the web server container. 