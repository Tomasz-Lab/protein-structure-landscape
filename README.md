# Protein Structure Landscape

This is a companion repository to the paper:

Szczerbiak P, Szydlowski LM, Wydmański W, Renfrew PD, Koehler Leman J, Kosciolek T. Large protein databases reveal structural complementarity and functional locality. Nat Commun 16, 7925 (2025). https://doi.org/10.1038/s41467-025-63250-3

The repository contains data, scripts and notebooks to reproduce main-text Figures and most Supplementary Figures. 

## Content

- `data`
     - Contains Parquet files used to reproduce the results presented in the paper. These can be accessed using the `pandas.read_parquet('{filename}.parquet')` function from the Pandas Python library.
     - Also includes intermediate files (CSV, Pickle) used by the notebooks (see below).
- `notebooks`
    - `Figure_*.ipynb`: Reproduces the figures from the paper.
    - `data-loader.ipynb`: Describes the contents of each Parquet file and demonstrates how to load them.
    - `commands.md`: A collection of commands used for clustering, generating embeddings, and performing dimensionality reduction.
    - `embeddings.ipynb`: Generates Geometricus embeddings for the ProtGPT and BFVD datasets. The notebook can be easily adapted for other datasets.
    - `dimensionality_reduction.ipynb`: Performs dimensionality reduction on the ProtGPT and BFVD embeddings. Also adaptable for other datasets.

- `plots` 
    - Contains an interactive Sankey diagram (`sankey_diagram.html`), referenced in the `Cluster heterogeneity` section of the supplementary materials as well as interactive version of Figure 6B (`esm_biomes.html`).

- `zconda_environment_setup.info`: Provides instructions for setting up the Python Conda environment required to run the notebooks.

**Note:** To run `embeddings.ipynb` and `dimensionality_reduction.ipynb` you must first download the `embeddings.zip` file from the Figshare data repository (https://dx.doi.org/10.6084/m9.figshare.27203073), unzip it, and place the contents in the `data/` folder.

## Novel methods
The source code for new methods and tools developed during the research is available in dedicated data repositories:

- [go2cog](https://github.com/Tomasz-Lab/go2cog)
- [deepFRI v1.1](https://github.com/bioinf-mcb/DeepFRI)
- [web server](https://github.com/wwydmanski/2dPointVis)

## Data sources

We analyzed datasets coming from:
- AFDB: Based on the lists of light and dark proteins stored in https://afdb-cluster.steineggerlab.workers.dev/, we downloaded AlphaFold models using gsutil API directly from the AFDB (see details here: https://github.com/google-deepmind/alphafold/tree/main/afdb) (last access: Feb 14  2024).
- ESMAtlas: We downloaded `foldcomp` files (regular expression: `highquality_clust30.*`) directly from https://foldcomp.steineggerlab.workers.dev/ (last access:  Jan 23 2024).
- MIP: We downloaded Zenodo archive from https://zenodo.org/records/6611431 (last access:  Jan 26 2024).

## Web server

The web server is available at: https://protein-structure-landscape.sano.science. 

It currently supports the following features:
- Structure search by ID or Gene Ontology (GO) function names
- Interactive structure visualization using PyMOL
- Download of structures in PDB format
- Filtering options, including:
    - taxonomy
    - source database (AFDB, ESMAtlas, MIP)
    - superCOG functional category
    - protein length
    - pLDDT (for AFDB models only)

When a protein is selected or searched, the server displays:
- Detailed information about the representative structure
- Location of the representative structure in the structural landscape
- A 3D visualization of the structure
- DeepFRI v1.0 function predictions

The web server will be continuously improved.
