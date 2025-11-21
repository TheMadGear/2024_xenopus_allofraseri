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
## samtools index
## add readgroups (picard)
## samtools index
## haplotype caller


# last step
## step 6 (haplotype caller)

