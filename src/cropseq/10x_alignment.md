# 10X Alignment

This section will cover the sequence alignment steps for the 10X sequences.
It assumes you have a similar directory structure to the one described in
the previous sections.

## Installation

We are going to use `kb-python` for sequencing alignment, which is a wrapper
of the `kallisto` and `bustools` commandline tools.

``` bash
# enter our conda environment (can skip if already in environment)
conda activate my_cropseq

# install kb-python via pip
pip install kb-python

# check if installation worked
kb --version
```

## Creating your results directory

`kb` will create an output directory **for each of the samples**.
This quickly gets unwieldy, so I recommend creating a new directory
in your project to hold these data for easy access later.

From the **root of your project directory** run:

``` bash
mkdir -p alignment/10X
```

Your project directory should now look like the following:

``` txt
project_name/
└── meta/
    └── whitelist/
        └── 3M-february-2018.txt.gz
    └── 10X_index/
        ├── index.idx 
        ├── t2g.txt
        ├── spliced_t2c.txt
        └── unspliced_t2c.txt
    └── pcr_index/
└── sequence/
    └── 10X/
        ├── sampleX_R1.fastq.gz
        ├── sampleX_R2.fastq.gz
        :        ...
        ├── sampleY_R1.fastq.gz
        └── sampleY_R2.fastq.gz
    └── pcr/
└── alignment/
    └── 10X/
```

## Running `kb count`

We can now run the alignment step which will create the `cell x gene`
count matrix.

We will do this using the `kb count` submodule of `kb`.

``` bash
kb count \
  --verbose \
  --h5ad \
  -t 4 \
  -x 10xv3 \
  -w meta/whitelist/3M-february-2018.txt.gz \
  -g meta/10X_index/t2g.txt \
  -i meta/10X_index/index.idx \
  --workflow lamanno \
  --filter bustools \
  -c1 meta/10X_index/spliced_t2c.txt \
  -c2 meta/10X_index/unspliced_t2c.txt \
  -o alignment/10X/SampleX \
  SampleX_R1.fastq.gz SampleX_R2.fastq.gz
```

## Running multiple sequencing datasets as one sample

Sometimes you have the same sample split across multiple sequencing files.
`kb` can handle this easily, and the only change to the above command is to
include the path of all files under the same sample name at the end.

``` bash
kb count \
  --verbose \
  --h5ad \
  -t 4 \
  -x 10xv3 \
  -w meta/whitelist/3M-february-2018.txt.gz \
  -g meta/10X_index/t2g.txt \
  -i meta/10X_index/index.idx \
  --workflow lamanno \
  --filter bustools \
  -c1 meta/10X_index/spliced_t2c.txt \
  -c2 meta/10X_index/unspliced_t2c.txt \
  -o alignment/10X/SampleX \
  SampleX_1_R1.fastq.gz SampleX_1_R2.fastq.gz \
  SampleX_2_R1.fastq.gz SampleX_2_R2.fastq.gz \
  SampleX_3_R1.fastq.gz SampleX_3_R2.fastq.gz
```

## Running multiple samples at once

Parallel processing of multiple samples at once is outside the scope of this
tutorial, but if you are interested in efficient ways of doing this I
recommend checking out:

- [nextflow](https://www.nextflow.io/)
- [snakemake](https://snakemake.readthedocs.io/en/stable/)

Otherwise, you can take the above command and put it into a for-loop fairly easily:

``` bash
#!/bin/bash

for sample_id in sequence/10X/*R1.fastq.gz;
do
    basename=$(basename -s ".fastq.gz" $sample_id);
    r1=$sample_id;
    r2=${r1/R1/R2};

    kb count \
      --verbose \
      --h5ad \
      -t 4 \
      -x 10xv3 \
      -w meta/whitelist/3M-february-2018.txt.gz \
      -g meta/10X_index/t2g.txt \
      -i meta/10X_index/index.idx \
      --workflow lamanno \
      --filter bustools \
      -c1 meta/10X_index/spliced_t2c.txt \
      -c2 meta/10X_index/unspliced_t2c.txt \
      -o alignment/10X/${basename} \
      $r1 $r2
    
done
```

## Results

Once `kb` has finished we can take a look at the data and see
if it is as expected.

If you explore the directory `alignment/10X/<your_sample_name>` you should see
the following:

``` txt
.
├── 10xv3_whitelist.txt
├── counts_filtered
│   ├── adata.h5ad
│   ├── spliced.barcodes.txt
│   ├── spliced.genes.txt
│   ├── spliced.mtx
│   ├── unspliced.barcodes.txt
│   ├── unspliced.genes.txt
│   └── unspliced.mtx
├── counts_unfiltered
│   ├── adata.h5ad
│   ├── spliced.barcodes.txt
│   ├── spliced.genes.txt
│   ├── spliced.mtx
│   ├── unspliced.barcodes.txt
│   ├── unspliced.genes.txt
│   └── unspliced.mtx
├── filter_barcodes.txt
├── inspect.json
├── inspect.spliced.json
├── inspect.unspliced.json
├── kb_info.json
├── matrix.ec
├── output.bus
├── output.filtered.bus
├── output.unfiltered.bus
├── run_info.json
├── spliced.filtered.bus
├── spliced.unfiltered.bus
├── transcripts.txt
├── unspliced.filtered.bus
└── unspliced.unfiltered.bus
```

The `cell x gene` matrix will be under `counts_filtered/adata.h5ad` or `counts_unfiltered/adata.h5ad`.

You can decide which of these you will use downstream, though I
recommend using the `counts_filtered` version.
