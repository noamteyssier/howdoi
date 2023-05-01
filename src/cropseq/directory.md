# Directory Management

## Creating a project directory

The first thing we will do is make a project directory to organize the
individual blocks of our analysis.

Let's first create a directory skeleton which we will then populate with
some data we need to collect from other sources.

```bash
# create your root directory as well as a meta and sequence directory
mkdir -p project_name/{meta, sequence}

# create a 10X and a PCR directory within your sequence directory
mkdir -p project_name/sequence/{10X,pcr}

# create a some meta directories we will populate later
mkdir -p project_name/meta/{index,whitelist,cropseq}
```

You should now have a directory structure that looks like this:

``` txt
project_name/
└── meta/
    ├── 10X_index/
    ├── pcr_index/
    └── whitelist/
└── sequence/
    ├── 10X/
    └── pcr/
```

At this point you will want to move your 10X files to the `project_name/sequence/10X`
directory and your enrichment PCR files to the `project_name/sequence/pcr`
directory so that you have the following directory structure:

``` txt
project_name/
└── meta/
    ├── 10X_index/
    ├── pcr_index/
    └── whitelist/
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
