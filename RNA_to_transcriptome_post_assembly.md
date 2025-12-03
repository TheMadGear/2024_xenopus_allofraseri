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

## then use picard to create .dict file
```
#!/bin/sh
#SBATCH --job-name=picard_dict_fai
#SBATCH --nodes=1
#SBATCH --ntasks-per-node=1
#SBATCH --time=24:00:00
#SBATCH --mem=8gb
#SBATCH --output=samtools_fai_picard_dict.%J.out
#SBATCH --error=samtools_fai_picard_dict.%J.err
#SBATCH --account=rrg-ben

# run by passing an argument like this
# sbatch ./dict_picard.sh /home/froglady/projects/rrg-ben/froglady/2024_allo/transcriptome/allo_trinity_assembly.Trinity (without the `.fa` suffix)

module load StdEnv/2023 gcc/12.3 picard/3.1.0 samtools/1.20

# make a .fai file
samtools faidx ${1}.fa

# make a .dict file
java -jar $EBROOTPICARD/picard.jar CreateSequenceDictionary REFERENCE=${1}.fa.gz OUTPUT=${1}.dict
```

## use picard to add readgroups
```

# run with sbatch ./picard_readgroups.sh /home/froglady/projects/rrg-ben/froglady/2024_allo/transcriptome/

#!/bin/sh
#SBATCH --job-name=picard_readgroups
#SBATCH --nodes=1
#SBATCH --ntasks-per-node=1
#SBATCH --time=24:00:00
#SBATCH --mem=8gb
#SBATCH --output=readgroups.%J.out
#SBATCH --error=readgroups.%J.err
#SBATCH --account=rrg-ben

# run with sbatch ./picard_readgroups.sh /home/froglady/projects/rrg-ben/froglady/2024_allo/transcriptome/


module load StdEnv/2023  picard/3.1.0

for file in ${1}*_bwa_sort.bam
do
    java -jar $EBROOTPICARD/picard.jar AddOrReplaceReadGroups I=${file} O=${file}_rg.bam RGID=4 RGLB=$(basename $file) RGPL=ILLUMINA RGPU=$(basename $file) RGSM=$(basename $file)
done
```

## DO NOT deduplicate 

## haplotype caller
```
#!/bin/sh
#SBATCH --job-name=HaplotypeCaller
#SBATCH --nodes=1
#SBATCH --ntasks-per-node=1
#SBATCH --time=12:00:00
#SBATCH --mem=32gb
#SBATCH --output=HaplotypeCaller.%J.out
#SBATCH --error=HaplotypeCaller.%J.err
#SBATCH --account=rrg-ben


# This script will read in the *_sorted.bam file names in a directory, and 
# make and execute the GATK command "RealignerTargetCreator" on these files. 

# execute like this:
#  sbatch haplotype_caller.sh /home/froglady/projects/rrg-ben/froglady/2024_allo/transcriptome/allo_trinity_assembly.Trinity.fasta /home/froglady/projects/rrg-ben/froglady/2024_allo/transcriptome

module load nixpkgs/16.09 gatk/4.1.0.0

for file in ${2}*_bwa_sort.bam_rg.bam
do
    gatk --java-options -Xmx24G HaplotypeCaller  -I ${file} -R ${1} -O ${file}.g.vcf -ERC GVCF
done
```


## combine GVCFs using DBI or GATK
## first, create bed file from genome dictionary file for contigs & contig length
```
cut -f2,3 allo_trinity_assembly.Trinity.dict > allo_transcript_contigs.bed
sed -i -e 's/SN://g'  allo_transcript_contigs.bed
sed -i -e 's/ LN:/ 1 /g' allo_transcript_contigs.bed
vi allo_transcript_contigs.bed # remove first line
```

# split into for so combine/genotype works
```
split -n 4 -d allo_transcript_contigs.bed
```

# run combine GVCFs on each bed file (named x00, x01, x02, x03)
```

```

## genotype GVCFs, using one of 2 GATK versions based on whether DBI or GATK were used



## variant filtration



## select variants












