# Reference Library

## Reference library format

You will need to identify what your reference library is.
In most cases this is a either a `csv` or a `fasta` file of sgrna names and sequences.

The input we expect should be a `fasta` file of the sgrna library.
Here is an example of what that looks like:

```text
>lib.0
ATAGCCCGGCGGTCTGCTGG
>lib.1
TAAGGCACTATAGCAATGAG
>lib.2
GTAGATAAAACGTGTGGCCC
>lib.3
AAGGCGACCATCTACCCTTG
```

## Processing reference library

There are a few processing steps that we will perform to validate that the
reference library is in the expected format and without errors.
Then we will generate an `sgrna -> gene` mapping file.

### Uppercase

We will first convert the fasta to fully uppercase

```bash
fxtools upper -i <your_fasta.fa> -o upper.fa
```

### Unique

We will then identify any duplicate sequences and remove them from the analysis.

```bash
fxtools unique -i upper.fa -o uniq.fa -n ambiguous.fa
```

### sgRNA to gene mapping

If your sgrna library is named predictably of the form: `<gene>_<information>...`
we can automatically generate the sgrna to gene mapping with `fxtools`

```bash
fxtools sgrna-table -i uniq.fa -o g2s.txt
```

### Variable regions

This next step will not apply to everyone - and it depends on whether your reference
library is purely the variable region of your guides or if it also contains the constant
adapter sequences.

Generally if your sequences are longer than about 23bp then your adapater sequence
is still on.
You can validate this by seeing if all your sequences share either the same prefix
or suffix.

If so - we will need to remove those constant regions and just operate with the
variable region.

We can use `fxtools` for this:

```bash
fxtools extract-variable -i uniq.fa -o uniq.var.fa
```
