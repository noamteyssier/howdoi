# PCR Alignment

Now we will move onto the alignment of the enrichment PCR sequences to
the sgRNA library.

We will also be using `kb` for this alignment, but we first need to create
a new custom index for the sgRNA library.

## sgRNA Index

We will be creating the index from the sgRNA library fasta file that we
included in the [Metadata](./metadata.md) section of the tutorial: `cropseq.fa`.

As a reminder this file is under: `project_name/meta/pcr_index` and
has the following structure:

### Filename: `cropseq.fa`

``` fasta
>rs17057051_i1
TTGTCCAGCATTCTGCTTCAATGGTTTAAGAGC
>rs17057051_i2
TTGGTCTCCATTGAAGATGTGTTGTTTAAGAGC
              ...
>rs1532277_i1
TTGCAGAACTCTAGCAAGACGTGGTTTAAGAGC
>rs1532277_i2
TTGGAATCTGGGCATTAGGCCCTGTTTAAGAGC
```

We will create our `kallisto` index from these sequences with the following command:

``` bash
kallisto index -k 15 -i cropseq.idx cropseq.fa
```

> **Note**:
>
> I have set the kmer size to 15 here because I have found this to be
> the optimal size in my own data. Feel free to adjust this value if
> you have strong opinions on why a different value would be best.

## Project Directory

After creating this file we will also create a new directory to hold our
alignment results:

``` bash
mkdir -p alignment/pcr
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
        ├── cropseq.fa
        └── cropseq.idx
└── sequence/
    └── 10X/
        ├── sampleX_R1.fastq.gz
        ├── sampleX_R2.fastq.gz
        :        ...
        ├── sampleY_R1.fastq.gz
        └── sampleY_R2.fastq.gz
    └── pcr/
        ├── sampleX_R1.fastq.gz
        ├── sampleX_R2.fastq.gz
        :        ...
        ├── sampleY_R1.fastq.gz
        └── sampleY_R2.fastq.gz
└── alignment/
    ├── 10X/
    └── pcr/

```

## Alignment

Now we align our PCR sequences to the newly created index using `kb` similar to
the previous alignment we did for the 10X sequences.

``` bash
kb count \
  --verbose \
  --h5ad \
  -t 4 \
  -x 10xv3 \
  -w meta/whitelist/3M-february-2018.txt.gz \
  -g meta/pcr_index/t2g.txt \
  -i meta/pcr_index/index.idx \
  --filter bustools \
  -o alignment/pcr/SampleX \
  SampleX_R1.fastq.gz SampleX_R2.fastq.gz
```

### Running multiple sequencing datasets as one sample

Sometimes you have the same sample split across multiple sequencing files.
`kb` can handle this easily, and the only change to the above command is
to include the path of all files under the same sample name at the end.

``` bash
kb count \
  --verbose \
  --h5ad \
  -t 4 \
  -x 10xv3 \
  -w meta/whitelist/3M-february-2018.txt.gz \
  -g meta/pcr_index/t2g.txt \
  -i meta/pcr_index/index.idx \
  --filter bustools \
  -o alignment/pcr/SampleX \
  SampleX_1_R1.fastq.gz SampleX_1_R2.fastq.gz \
  SampleX_2_R1.fastq.gz SampleX_2_R2.fastq.gz \
  SampleX_3_R1.fastq.gz SampleX_3_R2.fastq.gz
```

### Running multiple samples at once

Parallel processing of multiple samples at once is outside the scope of this
tutorial, but if you are interested in efficient ways of doing this I
recommend checking out:

- [nextflow](https://www.nextflow.io/)
- [snakemake](https://snakemake.readthedocs.io/en/stable/)

Otherwise, you can take the above command and put it into a for-loop fairly easily:

``` bash
#!/bin/bash

for sample_id in sequence/pcr/*R1.fastq.gz;
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
      -g meta/pcr_index/t2g.txt \
      -i meta/pcr_index/index.idx \
      --filter bustools \
      -o alignment/pcr/SampleX \
      $r1 $r2;
    
done
```

## Results

Once `kb` has finished we can take a look at the data and see
if it is as expected.

If you explore the directory `alignment/pcr/<your_sample_name>` you should see
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
