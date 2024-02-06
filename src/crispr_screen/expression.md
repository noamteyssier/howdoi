# Differential Expression

Now that we have finished [mapping our reads](./counting.md) we are ready to
perform differential expression.

The following step applies for **simple screens** or **1-dimensional screens**
which are generally of the form:

```text
sample_low_1
sample_low_2
sample_high_1
sample_high_2
```

> Note: More complicated schemas
> For more complicated schemas I recommend using [DESeq2](https://bioconductor.org/packages/release/bioc/html/DESeq2.html)
> for sgRNA expression then aggregating gene-level information with [sgagg](https://bioconductor.org/packages/release/bioc/html/DESeq2.html).

## Installation

For this analysis we will need to use `crispr_screen`.
We can install it easily with the rust package manager `cargo`

```bash
cargo install crispr_screen
```

## Usage

To run our analysis we just use the following command:

```bash
crispr_screen test \
  -i mapping.tab \
  -c sample_low_1 sample_low_2 \
  -t sample_high_1 sample_high_2
```

This will create 2 output files `results.gene_results.tab` and `results.sgrna_results.tab`
which reflect the differential expression at the gene and sgrna level respectively.

## More Information

This will perform the default enrichment analysis - but for more details or alternative
analysis methods check out the [crispr_screen documentation](https://noamteyssier.github.io/crispr_screen).
