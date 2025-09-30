# fastQC before cutadapt

```
#!/bin/sh
#SBATCH --job-name=fastQC
#SBATCH --nodes=4
#SBATCH --time=32:00:00
#SBATCH --cpus-per-task=20
#SBATCH --mem=16gb
#SBATCH --array=0-19
#SBATCH --output=fastQC.%J.out
#SBATCH --error=fastQC.%J.err
##SBATCH --mail-user=bohbom1@mcmaster.ca
#SBATCH --mail-type=BEGIN
#SBATCH --mail-type=END
#SBATCH --mail-type=FAIL
#SBATCH --account=def-ben

# run with sbatch fastQC_script.sh /home/froglady/projects/rrg-ben/froglady/2024_allo/2024_allo_RNAseq/


# loading stuff
module load StdEnv/2023
module load fastqc/0.12.1

parallel -j $SLURM_CPUS_PER_TASK fastqc -t 1 -o /home/froglady/projects/rrg-ben/froglady/2024_allo/2024_allo_RNAseq {} ::: /home/froglady/projects/rrg-ben/froglady/2024_allo/2024_allo_RNAseq/*001.fastq.gz
```

# cutadapt
```
#!/bin/sh
#SBATCH --job-name=cutadapt
#SBATCH --nodes=4
#SBATCH --time=32:00:00
##SBATCH --array=0-9
#SBATCH --mem=16gb
#SBATCH --output=cutadapt.%J.out
#SBATCH --error=cutadapt.%J.err
#SBATCH --account=def-ben
#SBATCH --mail-user=bohbom1@mcmaster.ca
#SBATCH --mail-type=BEGIN
#SBATCH --mail-type=END
#SBATCH --mail-type=FAIL

# run with: sbatch ./cutadapt_script.sh ../2024_allo_RNAseq/*L001_R1_001.fastq.gz ../2024_allo_RNAseq/*L001_R2_001.fastq.gz

path=$/home/froglady/projects/rrg-ben/froglady/2024_allo/2024_allo_RNAseq

for_run=${path}${1[$SLURM__ARRAY_TASK_ID]}
rev_run=${path}${2[$SLURM__ARRAY_TASK_ID]}

# NEED MIDDLE BIT TO IDENTIFY SPECIFIC FILE CODES (i.e. BJE3496)
# ^^ I actually think the slurm task id does this

echo "$SLURM_ARRAY_TASK_ID"

# what do the @ and # do?
# i don't think I need this line, think it was for error checking
num_files = ${#for_run[@]}

cutadapt -a AGATCGGAAGAGCACACGTCTGAACTCCAGTCA -A AGATCGGAAGAGCGTCGTGTAGGGAAAGAGTGT -o ${for_run}_cut_trimmed.fastq -p ${rev_run}_cut_trimmed.fastq ${for_run} ${rev_run}

```
# redo all this on nibi



- OKAY transfered RNAseq files, cutadapt.sh and fastQC.sh to cedar because graham not working
```
scp froglady@graham.computecanada.ca:/home/froglady/projects/rrg-ben/froglady/2024_allo/2024_allo_RNAseq/*001.fastq.gz .
```

- running fastQC and cutadapt again because graham not working
```
# fastQC
sbatch ./fastQC.sh /home/froglady/projects/def-ben/froglady/RNAseq_transfer/*001.fastq.gz

# cutadapt
sbatch ./cutadapt.sh *L001_R1_001.fastq.gz *L001_R2_001.fastq.gz
```













# adaptor sequences:

 >NEBNext_3p
AGATCGGAAGAGCACACGTCTGAACTCCAGTCA
 >NEBNext_5p
AGATCGGAAGAGCGTCGTGTAGGGAAAGAGTGT

# next steps
- fastQC again

- assembly with trinity
