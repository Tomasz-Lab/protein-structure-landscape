# Protein Structure Landscape

This repository contains data, scripts and notebooks for the analysis of the protein structure landscape described in:

Szczerbiak P, Szydlowski L, Wydmański W, Renfrew PD, Koehler Leman J, Kosciolek T. Large protein databases reveal structural complementarity and functional locality. bioRxiv, https://doi.org/10.1101/2024.08.14.607935 (2024).

## Novel methods
The source code for new methods and tools developed during the research is available in dedicated data repositories:

- [go2cog](https://github.com/Tomasz-Lab/go2cog)
- [deepFRI v1.1](https://github.com/bioinf-mcb/DeepFRI)
- [web server](https://github.com/wwydmanski/2dPointVis)

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
