
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
## bwa_mem.sh is in transcriptome directory

currently going off of these instructions: https://angus.readthedocs.io/en/2013/rnaseq_bwa.html

## map cutadapt-trimmed RNAseq files to transcriptome with bwa-mem
```
#!/bin/sh

# run with: ./bwa_mem.sh
         
reference=allo_trinity_assembly.Trinity.fasta

R1=/home/jade/rnaseq/*R1.fastq 
R2=/home/jade/rnaseq/*R2.fastq

output=RNAseq_bwa_out

bwa mem ${reference} ${R1} ${R2} > ${output}.sam

```
## then.....
```

```

# path 2
## index with STAR

## map cutadapt-trimmed RNAseq files to genome with STAR

## featurecounts (maybe part of above step)


