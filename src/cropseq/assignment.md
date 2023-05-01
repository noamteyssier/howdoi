# Assignment

Now that we have the PCR sequences aligned to their respective guides
we can perform the assignment step.

## Summary

The assignment step will be done with [`geomux`](https://github.com/noamteyssier/geomux).

This is a tool I created based on a hypergeometric test which can assign cells
to guides with high confidence.

The intuition behind the mathematics is that every cell with an sgRNA will
have a background distribution (or sequencing noise) and a signal distribution
(i.e. its true sgRNAs).
The hypergeometric test is used to identify what sgRNA abundance occurs above
random chance, and then a ratio test is used to select for cells with only a
single signal sgRNA (i.e. filter against high multiplicity of infection).

## Installation

We can install `geomux` directly from its github repo.

``` bash
pip install git+https://github.com/noamteyssier/geomux
```

## Usage

We can use `geomux` directly from the command line, or we can use it from
a jupyter notebook.

I prefer to run it from the commandline and will include those instructions
here.

If you prefer to use it from a jupyter notebook, check out the
[tutorial](https://github.com/noamteyssier/geomux#python-module)
on the repo.

### Creating an assignment directory

First we create a directory to hold our assignment results.

``` bash
mkdir -p meta/assignment/
```

### Running `geomux`

We can now run `geomux` with the following command:

``` bash
geomux \
    -i alignment/pcr/sampleX/counts_filtered/adata.h5ad \
    -o meta/assignment/sampleX.tab
```

This will create a tab-separated file (`*.tab`) which will have the cell-barcode,
associated guide, and statistics about the assignment provided.

## Intersecting the 10X sequencing data and the Assignments

We have now done all the steps necessary to intersect the 10X single-cell sequencing
data with the enrichment PCR.

We can now just load in the 10X sequencing data and merge the dataframes together
to have our cells assigned to their respective sgRNAs.

The next step will assume you have [`scanpy`](https://scanpy.readthedocs.io/)
installed already.

``` python
import pandas as pd
import scanpy as sc

# load assignments
assignments = pd.read_csv(
    "meta/assignment/sampleX.tab", 
    sep="\t", 
    index_col="barcode"
)

# load anndata (10X)
adata = sc.read(
    "alignment/10x/sampleX/counts_filtered/adata.h5ad"
)

# merge the two
adata.obs = adata.obs.merge(
    assignment, 
    left_index=True, 
    right_index=True, 
    how="left"
)

# drop cells that do not have an assignment
adata = adata.obs[~adata.obs.guide.isna()].copy()

# view the merged anndata
adata
```
