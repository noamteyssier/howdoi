Installation
============

There are many tools that you will need to use for the purposes of this analysis.
These tools will require you to have a basic understanding of package management
and tool usage at the command line, python, and R.

There are many resources out there for how to install specific packages so I will
not spend too much time on the details of installation.

Mandatory Note
--------------

> I **highly** recommend creating a new conda environment for the purposes of
**only** this analysis.
>
> As you generate new datasets you can copy this environment over to them, but
as you make changes you should leave this one unchanged so that you can always
come back to the same analysis without any unexpected quirks.

Commandline Tools
-----------------

-	[conda](https://docs.conda.io/projects/conda/en/latest/user-guide/install/index.html)
	-	Used for environment management
-	[kallisto bustools](https://www.kallistobus.tools/)
	-	Used for sequence alignment to the transcriptome
-	[kallisto](https://pachterlab.github.io/kallisto/download)
	-	is the core tool for pseudoalignment and pseudoalignment reference building
	-	a version of this will be packaged into kallisto-bustools but if you don't want
    to search for the binary you can just install this separately
-	[bustools](https://bustools.github.io/download)
	-	a tool for interacting with `*.bus` files which are used to organize and interact with single-cell data results.
	-	Some specifications about the [BUS File Format](https://github.com/BUStools/BUS-format)
	-	a version of this will be packaged into kallisto-bustools but if you don't want to search for the binary you can just install this separately
-	[jupyter](https://jupyter.org/install)
	-	Used for generating an interactive environment to explore your data ([overview](https://jupyterlab.readthedocs.io/en/stable/getting_started/overview.html)\)

Python Modules
--------------

-	[scanpy](https://scanpy.readthedocs.io/en/stable/)
	-	Used for single-cell analysis
-	[scvelo](https://scvelo.readthedocs.io/)
	-	Used for single-cell RNA velocity analysis (Optional)
-	[geomux](https://github.com/noamteyssier/geomux.git)
	-	used for cell-guide assignment
-	[hgsig](https://github.com/noamteyssier/hgsig.git)
	-	used for cluster enrichment/depletion and significance testing

R Libraries
-----------

-	[edgeR](https://bioconductor.org/packages/release/bioc/html/edgeR.html)
	-	used for differential expression analysis
