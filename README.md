An R package for inferring the subclonal architecture of tumors

> This is a fork of the original [sciClone github repository](https://github.com/genome/sciclone)

Major Changes

* Tidying of code to conform with [Google's R Style Guide](https://google.github.io/styleguide/Rguide.xml)
* Formatting of README
* Switching to [roxygen2](https://cran.r-project.org/web/packages/roxygen2/index.html) for function documentation
* Removing need to explicitly specify an output file for plotting

## Installation Instructions

Both the `sciClone` package and its `bmm` dependency can be installed by doing the following:

```{r}
# Install IRanges from bioconductor
source("http://bioconductor.org/biocLite.R")
biocLite("IRanges")

devtools::install_github("genome/bmm")
devtools::install_github("tinyheero/sciClone")
```

If you prefer to build the package by hand, follow these steps:

* Make sure that you have the dependencies from the CRAN and BioConductor repos:
	+ IRanges
	+ rgl
	+ RColorBrewer
	+ ggplot2
	+ grid
	+ plotrix
	+ methods

* Install the `bmm` package from [https://github.com/genome/bmm](https://github.com/genome/bmm)

* Download and build from source:

    ```
    git clone git@github.com:genome/sciclone.git
    cd sciclone/
    R CMD build sciClone
    R CMD INSTALL sciClone_1.0.7.tar.gz
    ```

## Usage

```{r}
library("sciClone")

# Read in vaf data from three related tumors
# Format is 5 column, tab delimited: 
# chr, pos, ref_reads, var_reads, vaf
# vaf should be between 0-100 for plotting reasons

v1 <- read.table("data/vafs.tumor1.dat", header = TRUE)
v2 <- read.table("data/vafs.tumor2.dat", header = TRUE)
v3 <- read.table("data/vafs.tumor3.dat", header = TRUE)

# Read in regions to exclude (commonly LOH)
# Format is 3-col bed (chr, start, end)
regions <- read.table("data/exclude.loh")

# read in segmented copy number data
# 4 columns - chr, start, stop, segment_mean   
cn1 <- read.table("data/copy_number_tum1")
cn2 <- read.table("data/copy_number_tum2")
cn3 <- read.table("data/copy_number_tum3")

# Set sample names
names <- c("Sample1","Sample2","Sample3")


# Examples:
#------------------------------------
# 1d clustering on just one sample
sc <- sciClone(vafs = v1, copyNumberCalls = cn1, sampleNames = names[1], 
               regionsToExclude = reg1)

# Cluster results
head(sc@vafs.merged)

# Create Output
writeClusterTable(sc, "results/clusters1") # same as sc@vafs.merged
sc.plot1d(sc)

#------------------------------------
#2d clustering using two samples:
sc = sciClone(vafs = list(v1,v2), copyNumberCalls = list(cn1,cn2),
              sampleNames = names[1:2], regionsToExclude = regions)
              
#create output
writeClusterTable(sc, "results/clusters2")
sc.plot1d(sc)
sc.plot2d(sc,"results/clusters2.2d.pdf")


#------------------------------------
#3d clustering using three samples:
sc = sciClone(vafs=list(v1,v2,v3),
          copyNumberCalls=list(cn1,cn2,cn3),
          sampleNames=names[1:3],
           regionsToExclude=regions)
#create output
writeClusterTable(sc, "results/clusters2")
sc.plot1d(sc, "results/clusters2.1d.pdf")
sc.plot2d(sc, "results/clusters2.2d.pdf")
sc.plot3d(sc, sc@sampleNames, size=700, outputFile="results/clusters3.3d.gif")

#This pattern generalizes up to N samples, except for plotting, which caps out at 3d for obvious reasons.
```

## Visualization

### single-tumor plot
![1d plot](http://i.imgur.com/n4JNs9t.png)

### 2d comparison plot
![2d plot](http://i.imgur.com/8h0qAWx.png)

### 3d comparison plot
![3d plot](http://i.imgur.com/iM0V1kq.gif)

## Notes

- Requires host system to have imagemagick installed before it can produce animated gif output of 3d plots.

- Input formats described in more detail in the R documentation (see `?sciClone`)

## Accessory Scripts and Data
The [sciClone-meta](https://github.com/genome/sciclone-meta) repo contains all data and scripts used to create the figures in the manuscript. It also contains a small suite of tests that demonstrate the capabilities of sciClone and verify that it is installed correctly.

## Reference
Manuscript published at [PLoS Computational Biology (doi:10.1371/journal.pcbi.1003665)](http://www.ploscompbiol.org/article/info%3Adoi%2F10.1371%2Fjournal.pcbi.1003665)

SciClone: Inferring clonal architecture and tracking the spatial and temporal patterns of tumor evolution

Christopher A. Miller<sup>1*</sup>, Brian S. White<sup>2*</sup>, Nathan D. Dees<sup>1</sup>, John S. Welch<sup>2,3</sup>, Malachi Griffith<sup>1</sup>, Obi Griffith<sup>1</sup>, Ravi Vij<sup>2,3</sup>, Michael H. Tomasson<sup>2,3</sup>, Timothy A. Graubert<sup>2,3</sup>, Matthew J. Walter<sup>2,3</sup>, William Schierding<sup>1</sup>, Timothy J. Ley<sup>1,2,3</sup>, John F. DiPersio<sup>2,3</sup>, Elaine R. Mardis<sup>1,3,4</sup>, Richard K. Wilson<sup>1,3,4</sup>, and Li Ding<sup>1,2,3,4</sup>

<sup>1</sup>The Genome Institute

<sup>2</sup>Department of Medicine

<sup>3</sup>Siteman Cancer Center

<sup>4</sup>Department of Genetics Washington University, St. Louis, MO 63110, USA

<sup>*</sup> These authors contributed equally to this work
