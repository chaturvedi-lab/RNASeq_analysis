# De novo transcriptomic assembly using Trinity
[TRINITY](https://github.com/trinityrnaseq/trinityrnaseq/wiki) is a great tool for building a *de novo* transcriptome assembly RNASeq data. The linked webpage has several tutorials to install, run and troubleshoot trinity. However, these were quite simplified examples and it took a bit of effort to wrap my head completely around everything. In addition, there are pre-processing steps which need to be followed before working on the TRINITY pipeline. Here I is my description of a *de novo* transcriptomic assembly for *Lycaeides melissa* in detail, so that there is a record and that it is easy to use this as a guide to build an assembly using RNASeq data in the future. In addition, this can be referred to as a reseource for finding great resources for tutorials on installations and running sample datasets. 

**Installation Software prerequisites:**
1. [samtools](http://samtools.sourceforge.net/) open-version 1.5 installed on the UofU CHPC. 
2. [bowtie2](http://bowtie-bio.sourceforge.net/bowtie2/index.shtml) version open-2.2.9 installed on the UofU CHPC.
2. [jellyfish](http://www.genome.umd.edu/jellyfish.html) CHPC version 2.2.9 locally installed on UofU.
3. [salmon](https://salmon.readthedocs.io/en/latest/salmon.html) version 0.9.1 locally installed on UofU.

Programs for post-processing of Trinity output files
1. [SWISSPROT](https://www.uniprot.org/) to run blastx on the trinity output files.

I then ran some intial stats assessment for the assembly using the `TrinityStats.pl` script.

Usage:
```bash
TrinityStats.pl Trinity.fasta
```
Here are the summaries generated using this script:

*Counts of transcripts, etc.*

~~~
Total trinity 'genes':	167323
Total trinity transcripts:	323046
Percent GC: 40.91

~~~

*Stats based on ALL transcript contigs:*

~~~
	Contig N10: 4240
	Contig N20: 2998
	Contig N30: 2311
	Contig N40: 1807
	Contig N50: 1386

	Median contig length: 362
	Average contig: 729.78
	Total assembled bases: 235752177

~~~

*Stats based on ONLY LONGEST ISOFORM per 'GENE':*

~~~
	Contig N10: 3960
	Contig N20: 2607
	Contig N30: 1848
	Contig N40: 1263
	Contig N50: 819

	Median contig length: 271
	Average contig: 517.68
	Total assembled bases: 86619651

~~~

# Reference based transcriptomic assembly using TopHat and Cufflinks
This is to assemble and identify the transcripts, and identifying the number of hits for each transcript and the genes they represent. For this, we need a reference genome. To do this, I map the reads to the reference genome using `Tophat` (http://ccb.jhu.edu/software/tophat/manual.shtml), then I assemble the overlapping and spaced mapped patterns in the BAM files, into exons and transcripts with `Cufflinks`. `Cufflinks` outputs a file to lay out the transcripts in reference to the genome. These giles can be merged into a single consolidated file and matched up with the existing genome annotation with Cuffmerge. Then, CuffDiff can be used to determine differential expression between different conditions represented by different group files. Tophat and Cufflinks do the main transcriptome assembly, but Cuffmerge contributes to the final arrangement of the output sequences.

To build this assembly, we first need to index the genome using bowtie. 

Here is the command I used to do this:

```bash
module load bowtie2
bowtie2-build melissa_ref.fa melissa
```
*Note we need to provide a name like "melissa" which is a prefix for all output files*

Now we can do alignments using `Tophat`. I used Tophat version 2.6.6. Here is an example bash script I used to run tophat:

```bash
#!/bin/bash
#SBATCH --job-name=tophat
#SBATCH --time=120:00:00 #walltime
#SBATCH --nodes=1 #number of cluster nodes
#SBATCH --account=usubio-kp #PI account
#SBATCH --partition=usubio-kp #specify computer cluster, other option is kinspeak

cd /uufs/chpc.utah.edu/common/home/gompert-group1/data/lycaeides/matt_transcriptome/tophat/

module load bowtie2
module load bowtie

./tophat-2.1.1.Linux_x86_64/tophat -I 1000 -i 20 -o outdir/ ../reference/melissa ../trim_galore/KS001_S71_L008_R1_001_val_1.fq,../trim_galore/KS002_S72_L008_R1_001_val_1.fq,../trim_galore/KS003_S73_L008_R1_001_val_1.fq,../trim_galore/KS001_S71_L008_R2_001_val_2.fq,../trim_galore/KS002_S72_L008_R2_001_val_2.fq,../trim_galore/KS003_S73_L008_R2_001_val_2.fq
```
This script will create output files in the out directory (outdir).

These options created an assembly with the following stats found in the `align_summary.txt` created in the out directory.

~~~
Left reads:
          Input     : 514505845
           Mapped   : 249010252 (48.4% of input)
            of these:   5058177 ( 2.0%) have multiple alignments (6696 have >20)
Right reads:
          Input     : 514505845
           Mapped   : 237320992 (46.1% of input)
            of these:   4959247 ( 2.1%) have multiple alignments (6727 have >20)
47.3% overall read mapping rate.

Aligned pairs: 216218873
     of these:   4518181 ( 2.1%) have multiple alignments
                  482421 ( 0.2%) are discordant alignments
41.9% concordant pair alignment rate.
~~~

I then modified the options for tophat and reran the command as follows:

```bash
./tophat-2.1.1.Linux_x86_64/tophat --read-edit-dist 5 --fusion-read-mismatches 3 --segment-mismatches 3 --read-mismatches 4 --read-gap-length 4 --read-realign-edit-dist 0 -p 10 -I 1000 -i 20 -o outdir1/ ../reference/melissa ../trim_galore/KS001_S71_L008_R1_001_val_1.fq,../trim_galore/KS002_S72_L008_R1_001_val_1.fq,../trim_galore/KS003_S73_L008_R1_001_val_1.fq,../trim_galore/KS001_S71_L008_R2_001_val_2.fq,../trim_galore/KS002_S72_L008_R2_001_val_2.fq,../trim_galore/KS003_S73_L008_R2_001_val_2.fq
```
These options increased the number of reads mapped as seen below:

~~~
Left reads:
          Input     : 514505845
           Mapped   : 272314312 (52.9% of input)
            of these:  19301854 ( 7.1%) have multiple alignments (88535 have >20)
Right reads:
          Input     : 514505845
           Mapped   : 263444728 (51.2% of input)
            of these:  18947381 ( 7.2%) have multiple alignments (88503 have >20)
52.1% overall read mapping rate.

Aligned pairs: 244327894
     of these:  18115644 ( 7.4%) have multiple alignments
                  965147 ( 0.4%) are discordant alignments
47.3% concordant pair alignment rate.
~~~
