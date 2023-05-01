# Alignment

This section will cover the sequence alignment steps for the 10X sequences.
It assumes you have a similar directory structure to the one described in the
`Processing` section of this book.

## Running an alignment

We are going to use `kb-python` for sequencing alignment, which is a wrapper
of the `kallisto` and `bustools` commandline tools.

The `kb` command and it's downstream files can all be generated via `kallisto`
and `bustools` separately and I will attempt to explain the different steps of
pipeline as they are important to understand when troubleshooting any errors
or irregularities that come up after this step.

### Validating the Installation

Before continuing please check that you have `kb-python` installed correctly
by running the following command

``` bash
kb --version
```

You should see a response similar to the following:

``` txt
usage: kb [-h] [--list] <CMD> ...

kb_python 0.27.0

positional arguments:
  <CMD>
    info      Display package and citation information
    compile   Compile `kallisto` and `bustools` binaries from source
    ref       Build a kallisto index and transcript-to-gene mapping
    count     Generate count matrices from a set of single-cell FASTQ files

options:
  -h, --help  Show this help message and exit
  --list      Display list of supported single-cell technologies
```

If you don't please go back to the `Installation` section of this book and
install `kb-python`

### Creating your results directory

`kb` will create an output directory for each of the samples you are running it on.
I recommend you create a new directory under your project to hold these data
for easy access later.

From the root of your project directory run:

```bash
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

### Running `kb count`

To perform the counting step (i.e. building the cell x gene count matrix)
we will use the `kb count` sub module of `kb` python

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

This will create an output directory `alignment/10X/SampleX` which will
contain the results of your alignment.

### Argument Descriptions

There are many arguments to this command which you could read the
documentation to learn more about if you are interested in the details.
In a nutshell though

#### General Arguments to `kb count`

``` txt
-o
  name for the output directory created

-t 
  number of threads to use in alignment

-x 
  provides the sequencing technology

-w
  provides a specified cell barcode whitelist

-g
  provides the transcript to gene mapping (this will sum the counts for the transcripts to their parent gene)

-i
  provides the index file to map sequences against

--filter
  creates a filtered dataframe that attempts to remove the near empty droplets

--verbose
  tell me what you are doing as you are doing it

--h5ad
  create a *.h5ad formatted cell x gene matrix
```

#### RNA Velocity Specific Arguments to `kb count`

``` txt
--workflow
  provides the type of workflow. for velocity you should provide `lamanno` 

-c1
  provides the cDNA transcripts-to-capture

-c2
  provides the intron transcripts-to-capture
```

