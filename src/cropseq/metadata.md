# Metadata

## Whitelist

The [whitelist](https://kb.10xgenomics.com/hc/en-us/articles/115004506263-What-is-a-barcode-whitelist)
is a set of cell barcodes you expect your reads to come from.
This is usually specified by the technology and can be found online.

Here you can find the [10Xv3 cell barcodes](https://github.com/noamteyssier/10x_whitelist_mirror/blob/0055bd325b5115731b34c7d7e75d422704489ba8/3M-february-2018.txt.gz)
in my github mirror.
But you can also find them in your installation of CellRanger or kallisto.

> __Note__:
>
> You will use the same whitelist for both the 10X sequences and the PCR
sequences as they come from the same set of cells.
This step is not required if you are aligning with kallisto because it can
provide you with the same whitelist.
But it will save space to just provide this file as otherwise it will write
to disk the same whitelist _for each sample you process_

```bash
# Download the 10X v3 Whitelist from my github mirror
wget \
  https://github.com/noamteyssier/10x_whitelist_mirror/raw/main/3M-february-2018.txt.gz \
  project_name/meta/whitelist/3M-february-2018.txt.gz
```

## Kallisto Index

The index is a kmer de-bruijn graph representation of the transcriptomic sequences
you will be aligning your 10X sequences to.

> __Note__:
>
> If you are interested in learning more about the kallisto index I
highly recommend reading the kallisto paper which describes the
pseudo-alignment algorithm and the index. {{#cite bray_near-optimal_2016}}

If you are interested in building your own reference index please check out
the [kb tutorial](https://www.kallistobus.tools/tutorials/kb_custom_index/python/kb_transcriptome_index/)

For the purposes of this tutorial we will be using a [prebuilt index](https://www.kallistobus.tools/tutorials/kb_velocity/python/kb_velocity/#download-a-pre-built-human-rna-velocity-index)
generated for RNA velocity analyses.

We will also be building our own index in an upcoming section so you can look
at how to do that there as well.

## RNA Velocity Transcriptome

> Even if you are not interested in doing an RNA velocity analysis - it doesn't
hurt you to align to a spliced/unspliced reference.
>
> The counts are aggregated between the spliced/unspliced records in the
downstream data, but you have an added option of observing the splice/unsplice
ratio

```bash
# moving to the planned directory
cd project_name/meta/index/

# Downloading the Reference Index
kb ref \
  -d linnarsson \
  -i index.idx \
  -g t2g.txt \
  -c1 spliced_t2c.txt \
  -c2 unspliced_t2c.txt
```

The above command will download the reference index and create the following files:

  1. `project_name/meta/10X_index/index.idx`
  2. `project_name/meta/10X_index/t2g.txt`
  3. `project_name/meta/10X_index/spliced_t2c.txt`
  4. `project_name/meta/10X_index/unspliced_t2c.txt`

These will be used in the next section when we run our 10X alignment.

## Target Sequences

The `cropseq.fa` file here is a stand-in for the fasta formatted sequences of
your expected guide sequences.

You will want to place this under `project_name/meta/pcr_index/`

### Filename: `cropseq.fa`

``` txt
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

## Final Directory Structure

I __highly__ recommend you have a directory structure like the following
for your project.

> Downstream code will be expecting this directory structure but if you feel
strongly about your own structure then it can be easily modified for your own interests.

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
        └── cropseq.fa
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
```
