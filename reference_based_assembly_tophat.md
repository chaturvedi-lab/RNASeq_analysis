# Reference based transcriptomic assembly using TopHat and Cufflinks
This is to assemble and identify the transcripts, and identifying the number of hits for each transcript and the genes they represent. For this, we need a reference genome. To do this, I map the reads to the reference genome using `Tophat` (http://ccb.jhu.edu/software/tophat/manual.shtml), then I assemble the overlapping and spaced mapped patterns in the BAM files, into exons and transcripts with `Cufflinks`. `Cufflinks` outputs a file to lay out the transcripts in reference to the genome. These giles can be merged into a single consolidated file and matched up with the existing genome annotation with Cuffmerge. Then, CuffDiff can be used to determine differential expression between different conditions represented by different group files. Tophat and Cufflinks do the main transcriptome assembly, but Cuffmerge contributes to the final arrangement of the output sequences.
Here is a basic flowchart of how this process works:
![Reference based transcriptome assembly](nprot.2012.016-F2.jpg)

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
