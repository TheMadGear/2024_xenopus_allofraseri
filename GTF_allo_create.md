## Ben's AI instructions:

```
Step-by-step Guide:
1. Generate the genome index (1st Pass):
Use the --runMode genomeGenerate command with your FASTA file.
Do NOT include a --sjdbGTFfile at this stage, as you do not have one.

2. Run the 1st Pass Alignment for all samples:
Run STAR for each of your samples using the index created in the previous step. 
Specify the --runMode alignReads parameter. 
Collect the SJ.out.tab file from each sample.

3. Combine and filter junction files:
Concatenate all the individual SJ.out.tab files into a single file. 
Apply any necessary filtering (e.g., remove junctions that are too short, or too few reads), though for multiple samples you may not need to filter much. 

4. Generate the 2nd Pass Genome Index:
Create a new index using the genomeGenerate mode, but this time provide the combined SJ.out.tab file using the --sjdbFileChrStartEnd parameter. 

5. Run the 2nd Pass Alignment:
Perform the 2nd pass alignment for all your samples using this new, updated genome index. 
This pass will use the comprehensive set of novel junctions identified in the first pass, improving mapping accuracy for these junctions.
```

## step 1 (STAR index)
```
#!/bin/sh
#SBATCH --job-name=star_index
#SBATCH --nodes=1
#SBATCH --ntasks-per-node=1
#SBATCH --time=24:00:00
#SBATCH --mem=256gb
#SBATCH --output=star_index.%J.out
#SBATCH --error=star_index.%J.err
#SBATCH --account=rrg-ben

module load star/2.7.11b

STAR --runMode genomeGenerate \
--genomeDir /home/froglady/projects/rrg-ben/for_jade/Adam_allo_genome_assembly_with_bubbles/ \
--genomeFastaFiles /home/froglady/projects/rrg-ben/for_jade/Adam_allo_genome_assembly_with_bubbles/allo.fasta.contigs.fasta \
--runThreadN 8 \
--limitGenomeGenerateRAM=124544990592
```

## step 2 (STAR map)
```
#!/bin/sh
#SBATCH --job-name=STAR_map
#SBATCH --nodes=6
#SBATCH --ntasks-per-node=1
#SBATCH --time=16:00:00
#SBATCH --mem=64gb
#SBATCH --output=STAR_map.%J.out
#SBATCH --error=STAR_map.%J.err
#SBATCH --account=rrg-ben


module load star/2.7.11b


# run with sbatch ./STAR_map_GTF.sh /home/froglady/projects/rrg-ben/froglady/2024_allo/transcriptome/ 

for file in $1/*R1.fastq; do
STAR --runMode alignReads \
     --genomeDir /home/froglady/projects/rrg-ben/for_jade/Adam_allo_genome_assembly_with_bubbles/ \
     --runThreadN 6 \
     --readFilesIn ${file::-8}*R1.fastq ${file::-8}*R2.fastq \
     --outFileNamePrefix ${file::-19} \
     --outSAMtype BAM SortedByCoordinate \
     --outSAMunmapped Within \
     --outSAMattributes Standard
done
```

## step 3 (concatenate tab splice junction output)
```
cat *tab > allo_tad_SJ.out.tab
```

## step 4 (STAR index pass 2)
```
#!/bin/sh
#SBATCH --job-name=star_index
#SBATCH --nodes=1
#SBATCH --ntasks-per-node=1
#SBATCH --time=24:00:00
#SBATCH --mem=256gb
#SBATCH --output=star_index.%J.out
#SBATCH --error=star_index.%J.err
#SBATCH --account=rrg-ben


# run with sbatch ./STAR_index_two_GTF.sh

module load star/2.7.11b

STAR --runMode genomeGenerate --genomeDir /home/froglady/projects/rrg-ben/for_jade/Adam_allo_genome_assembly_with_bubbles/ --genomeFastaFiles /home/froglady/projects/rrg-ben/for_jade/Adam_allo_genome_assembly_with_bubbles/allo.fasta.contigs.fasta --runThreadN 8 --sjdbFileChrStartEnd /home/froglady/projects/rrg-ben/froglady/2024_allo/transcriptome/allo_tad_SJ.out.tab --limitGenomeGenerateRAM=124544990592
```

## step 5 (STAR map pass 2)
```
#!/bin/sh
#SBATCH --job-name=STAR_map
#SBATCH --nodes=6
#SBATCH --ntasks-per-node=1
#SBATCH --time=3-12:00:00
#SBATCH --mem=64gb
#SBATCH --output=STAR_map.%J.out
#SBATCH --error=STAR_map.%J.err
#SBATCH --account=rrg-ben


module load star/2.7.11b


# run with sbatch ./STAR_map_two_GTF.sh /home/froglady/projects/rrg-ben/froglady/2024_allo/transcriptome/ 

for file in $1/*R1.fastq; do
STAR --runMode alignReads \
     --genomeDir /home/froglady/projects/rrg-ben/for_jade/Adam_allo_genome_assembly_with_bubbles/ \
     --runThreadN 6 \
     --readFilesIn ${file::-8}*R1.fastq ${file::-8}*R2.fastq \
     --outFileNamePrefix ${file::-19}_second \
     --outSAMtype BAM SortedByCoordinate \
     --outSAMunmapped Within \
     --outSAMattributes Standard
done

```


# fixes
# before samtools index try picard- STAR created .bai files
## samtools index
```
#samtools index *secondAligned.sortedByCoord.out.bam
```


## add readgroups (picard)
```
#!/bin/sh
#SBATCH --job-name=picard_readgroups
#SBATCH --nodes=1
#SBATCH --ntasks-per-node=1
#SBATCH --time=24:00:00
#SBATCH --mem=8gb
#SBATCH --output=readgroups.%J.out
#SBATCH --error=readgroups.%J.err
#SBATCH --account=rrg-ben

# run by passing an argument like this
# sbatch ./STAR_picard_readgroups.sh /home/froglady/projects/rrg-ben/froglady/2024_allo/transcriptome


module load StdEnv/2023  picard/3.1.0

for file in ${1}/*secondAligned.sortedByCoord.out.bam
do
    java -jar $EBROOTPICARD/picard.jar AddOrReplaceReadGroups I=${file} O=${file}_rg.bam RGID=4 RGLB=$(basename $file) RGPL=ILLUMINA RGPU=$(basename $file) RGSM=$(basename $file)
done
```

## samtools index
```
samtools index -M *secondAligned.sortedByCoord.out.bam_rg.bam
```

## haplotype caller
```
#!/bin/sh
#SBATCH --job-name=HaplotypeCaller
#SBATCH --nodes=1
#SBATCH --ntasks-per-node=1
#SBATCH --time=4-12:00:00
#SBATCH --mem=30gb
#SBATCH --output=HaplotypeCaller.%J.out
#SBATCH --error=HaplotypeCaller.%J.err
#SBATCH --account=rrg-ben


# This script will read in the *_sorted.bam file names in a directory, and 
# make and execute the GATK command "RealignerTargetCreator" on these files. 

# execute like this:
# sbatch 2021_HaplotypeCaller.sh ref bam chr
# sbatch 2021_HaplotypeCaller.sh /home/ben/projects/rrg-ben/ben/2021_XL_v10_refgenome/XL_v10.1_concatenatedscaffolds.fa bam


# jade execute:
# sbatch ./STAR_haplotype_caller.sh /path/to/transcriptome path/to/bam/files

# sbatch ./STAR_haplotype_caller.sh /home/froglady/projects/rrg-ben/for_jade/Adam_allo_genome_assembly_with_bubbles/allo.fasta.contigs.fasta /home/froglady/projects/rrg-ben/froglady/2024_allo/transcriptome/

# have to run individually per sample ???

module load StdEnv/2023 gatk/4.4.0.0

for file in $2/*secondAligned.sortedByCoord.out.bam_rg.bam
do
gatk --java-options -Xmx24G HaplotypeCaller  -I ${file} -R ${1} -O ${file::-41}second_rg.bam_allchrs.g.vcf -ERC GVCF --disable-read-filter MappingQualityAvailableReadFilter
done

```

# create bed file from genome assembly to feed into combineGVCFs
```
cut -f2,3 allo.fasta.contigs.dict > allo_genome_contigs.bed
sed -i -e 's/SN://g'  allo_genome_contigs.bed
sed -i -e 's/ LN:/ 1 /g' allo_genome_contigs.bed
vi allo_genome_contigs.bed # remove first line
```

# merge gvcfs
```

```
# run DBI genotype gvcf
```

```





