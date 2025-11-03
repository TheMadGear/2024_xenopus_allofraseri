
# 2 paths at once

# path 1
## index with bwa-mem
```
screen -S bwa_index -L
bwa index allo_trinity_assembly.Trinity.fasta
```
## map to transcriptome with bwa-mem

currently going off of these instructions: https://angus.readthedocs.io/en/2013/rnaseq_bwa.html

## map cutadapt-trimmed RNAseq files to transcriptome with bwa-mem




# path 2
## index with STAR

## map cutadapt-trimmed RNAseq files to genome with STAR

## featurecounts (maybe part of above step)


