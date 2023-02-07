# Counting sgRNA abundance

## Tools Needed

For this step of the analysis we'll need two different tools:

1. [fxtools](https://github.com/noamteyssier/fxtools)
2. [sgcount](https://github.com/noamteyssier/sgcount)

Each of these tools has their own documentation
([fxtools](https://github.com/noamteyssier/fxtools) and
[sgcount](https://noamteyssier.github.io/sgcount/))
for more detailed usage - but I will describe everything you need
for your analysis here.

### Installation

If you've never used any rust tools before - you will need to install `cargo`
the rust package manager.

```bash
# installs `cargo`
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
```

Then we can install these tools easily with `cargo`

```bash
cargo install fxtools
cargo install sgcount
```

You can validate that they installed with the following:

```bash
fxtools --version
sgcount --version
```
