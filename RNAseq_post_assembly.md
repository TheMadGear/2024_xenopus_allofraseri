
# 2 paths at once

# path 1
## index with bwa-mem
```
screen -S bwa_index -L
bwa index allo_trinity_assembly.Trinity.fasta
```
## create .fai file?
```
 samtools faidx allo_trinity_assembly.Trinity.fasta
```


## map to transcriptome with bwa-mem
# bwa_mem.sh is in transcriptome directory

currently going off of these instructions: https://angus.readthedocs.io/en/2013/rnaseq_bwa.html

## map cutadapt-trimmed RNAseq files to transcriptome with bwa-mem
```
#!/bin/sh
#SBATCH --job-name=bwa_align
#SBATCH --nodes=4
#SBATCH --ntasks-per-node=4
#SBATCH --time=24:00:00
#SBATCH --mem=32gb
#SBATCH --output=bwa_align.%J.out
#SBATCH --error=bwa_align.%J.err
#SBATCH --account=rrg-ben

# run with sbatch ./bwa_mem.sh 

module load StdEnv/2023 bwa/0.7.18
module load StdEnv/2023 gcc/12.3 samtools/1.20

reference=/home/froglady/projects/rrg-ben/froglady/2024_allo/transcriptome/allo_trinity_assembly.Trinity.fasta
echo ${reference}
R1=/home/froglady/projects/rrg-ben/froglady/2024_allo/transcriptome
R2=/home/froglady/projects/rrg-ben/froglady/2024_allo/transcriptome



# check number of threads on info114 (-t flag)
for file in ${R1}/*R1.fastq; do
    #echo ${file::-8}
    bwa mem ${reference} ${file::-6}R1.fastq ${file::-6}R2.fastq -t 16 | samtools view -Shu - | samtools sort -m 12G - -o ${file::-6}_bwa_sort.bam
        samtools index ${file::-6}_bwa_sort.bam
done         

```

## then.....
```

```

# path 2
## index with STAR

## map cutadapt-trimmed RNAseq files to genome with STAR

## featurecounts (maybe part of above step)


