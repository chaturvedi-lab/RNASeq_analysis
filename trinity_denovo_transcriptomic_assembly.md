# De novo transcriptomic assembly using Trinity
[TRINITY](https://github.com/trinityrnaseq/trinityrnaseq/wiki) is a great tool for building a *de novo* transcriptome assembly RNASeq data. The linked webpage has several tutorials to install, run and troubleshoot trinity. However, these were quite simplified examples and it took a bit of effort to wrap my head completely around everything. In addition, there are pre-processing steps which need to be followed before working on the TRINITY pipeline. Here I is my description of a *de novo* transcriptomic assembly for *Lycaeides melissa* in detail, so that there is a record and that it is easy to use this as a guide to build an assembly using RNASeq data in the future. In addition, this can be referred to as a reseource for finding great resources for tutorials on installations and running sample datasets. 

## Software & Data

#### Installation Software prerequisites:
1. [samtools](http://samtools.sourceforge.net/) open-version 1.5 installed on the UofU CHPC. 
2. [bowtie2](http://bowtie-bio.sourceforge.net/bowtie2/index.shtml) version open-2.2.9 installed on the UofU CHPC.
2. [jellyfish](http://www.genome.umd.edu/jellyfish.html) CHPC version 2.2.9 locally installed on UofU.
3. [salmon](https://salmon.readthedocs.io/en/latest/salmon.html) version 0.9.1 locally installed on UofU.

#### Programs for post-processing of Trinity output files
1. [SWISSPROT](https://www.uniprot.org/) to run blastx on the trinity output files.

# Reference based transcriptomic assembly using Trinity
