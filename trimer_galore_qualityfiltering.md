# Using trim_galore to do quality filtering of RNASeq data
[Trim_galore](https://www.bioinformatics.babraham.ac.uk/projects/trim_galore/) is a wrapper tool around **Cutadapt** and **FastQC** to apply quality and adapter trimming to FastQ files, with some extra functionality for RRBS-type libraries. I used Trim galore for adapter trimming and general quality filtering for paired-end RNASeq reads. I first ran FastQC on raw sequences and checked for adapter contamination. Once, I had an idea of the adapter sequences, I used Trim galore for the Illumina standard adapter trimming ('AGATCGGAAGAGC').
I used Trim_galore version 0.4.4. This version is installed globally on the UofU CHPC.

#### Software prerequisites to be loaded:
1. [cutadapt](https://github.com/marcelm/cutadapt/) locally installed in the trim_galore working directory
2. [FastQC](https://www.bioinformatics.babraham.ac.uk/projects/fastqc/) locally installed on UofU CHPC.

Here is a general command to run trim_galore on paired end reads:

```bash
trim_galore --fastqc --dont_gzip --paired read1.fastq read2.fastq
```
Here is gave the option --fastqc because I wanted the program to run fastqc after trimming. I gave the option --dont_gzip because I did not want the program to zip the output files. 

Here is a bash script I used to run the program (change the file names and path as required):

~~~
#!/bin/bash
#SBATCH -n 12 
#SBATCH -N 1
#SBATCH -t 300:00:00
#SBATCH -p usubio-kp
#SBATCH -A usubio-kp
#SBATCH -J trimgalore

module load cutadapt
module load fastqc

cd /uufs/chpc.utah.edu/common/home/gompert-group1/data/lycaeides/matt_transcriptome/trim_galore/

/uufs/chpc.utah.edu/sys/pkg/trim_galore/trim_galore --fastqc --dont_gzip --paired ../rawdata/fastq/KS001_S71_L008_R1_001.fastq ../rawdata/fastq/KS001_S71_L008_R2_001.fastq ../rawdata/fastq/KS002_S72_L008_R1_001.fastq ../rawdata/fastq/KS002_S72_L008_R2_001.fastq ../rawdata/fastq/KS003_S73_L008_R1_001.fastq ../rawdata/fastq/KS003_S73_L008_R2_001.fastq ../rawdata/fastq/KS004_S74_L008_R1_001.fastq ../rawdata/fastq/KS004_S74_L008_R2_001.fastq ../rawdata/fastq/PMKS001_S109_L007_R1_001.fastq ../rawdata/fastq/PMKS001_S109_L007_R2_001.fastq ../rawdata/fastq/PMKS002_S110_L007_R1_001.fastq ../rawdata/fastq/PMKS002_S110_L007_R2_001.fastq ../rawdata/fastq/PMKS003_S111_L007_R1_001.fastq ../rawdata/fastq/PMKS003_S111_L007_R2_001.fastq ../rawdata/fastq/PMKS004_S112_L007_R1_001.fastq ../rawdata/fastq/PMKS004_S112_L007_R2_001.fastq ../rawdata/fastq/PMKS005_S113_L007_R1_001.fastq ../rawdata/fastq/PMKS005_S113_L007_R2_001.fastq ../rawdata/fastq/PMKS006_S114_L007_R1_001.fastq ../rawdata/fastq/PMKS006_S114_L007_R2_001.fastq ../rawdata/fastq/PMKS007_S115_L007_R1_001.fastq ../rawdata/fastq/PMKS007_S115_L007_R2_001.fastq ../rawdata/fastq/PMKS008_S116_L007_R1_001.fastq ../rawdata/fastq/PMKS008_S116_L007_R2_001.fastq
~~~
