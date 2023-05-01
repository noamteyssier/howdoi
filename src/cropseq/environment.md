# Environment

As mentioned previously, we will create our conda environment now
and add in our modules as we need them.

## Installing Conda

I recommend you install [Miniconda](https://docs.conda.io/en/latest/miniconda.html)
which is a *minimal* installer conda.
It has a very small footprint and works great.

See the installation instructions [here](https://docs.conda.io/en/latest/miniconda.html#latest-miniconda-installer-links).

Follow the above instructions and check if everything works:

``` bash
conda --version
```

This should give you a version number of your installation.

## Create Conda Environment

Now we are ready to create our conda environment for our analysis.

> Make sure you **only** use this environment for a single analysis.
> Do no reuse environments between analyses since this may lead to
> possible versioning issues and your analysis may not be reproducible
> when you come back to it several months later!

``` bash
conda create -n my_cropseq python=3.8
```

Here we are creating an environment called `my_cropseq` and stating that
the python version is `3.8`.

> You can use alternative python versioning if you want the latest and
greatest features, but I found this version plays best with older modules.

## Moving into and out of the environment

We can now step into our environment as follows:

``` bash
conda activate my_cropseq
```

And we can step out as follows:

``` bash
conda deactivate
```
