# Protein Structure Landscape

This is a companion repository to the paper:

Szczerbiak P, Szydlowski L, Wydmański W, Renfrew PD, Koehler Leman J, Kosciolek T. Large protein databases reveal structural complementarity and functional locality. bioRxiv, https://doi.org/10.1101/2024.08.14.607935 (2024).

The repository contains data, scripts and notebooks to reproduce main-text Figures and most Supplementary Figures. 

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

It currently allows for:   
- structure search by ID
- structure visualization with PyMOL
- filtering based on:
    - database (AFDB, ESMAtlas, MIP)
    - superCOG functional category
    - protein length
    - pLDDT (for AFDB models only)

The web server will be continuously improved.
