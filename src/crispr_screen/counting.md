# Guide Counting

Now that we have [prepared our reference library](./reference.md) we are ready
to map our records with `sgcount`.

## Running `sgcount`

```bash
sgcount \
  -l uniq.fa \
  -i sample_a.fq.gz sample_b.fq.gz sample_c.fq.gz sample_d.fq.gz \
  -n a b c d \
  -t 4 \
  -g g2s.txt \
  -o mapping.tab
```

In the above command we are:
- providing our reference library (`uniq.fa` or `uniq.var.fa`) generated previously.
- providing our `fastq` files representing our sgrna library to the `-i` flag,
- providing an optional custom renaming of those files with the `-n` flag,
- stating we will be using 4 threads with the `-t` flag,
- providing our gene to sgrna mapping with the `-g` flag,
- and writing our results to the output file `mapping.tab` with the `-o` flag.

Thats it! Once it finishes we're done with this step.

## More information

For more detailed information on optional arguments to this check out the
[sgcount documentation](https://noamteyssier.github.io/sgcount/usage/)
or for information on how it works at
[sgcount implementation](https://noamteyssier.github.io/sgcount/implementation/)
