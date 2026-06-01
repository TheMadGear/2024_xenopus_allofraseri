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

```
sbatch ./stringtie.sh /home/froglady/projects/rrg-ben/froglady/2024_allo/transcriptome/DNA_files/
```

```
#!/bin/sh
#SBATCH --job-name=stringtie_gtf
#SBATCH --nodes=1
#SBATCH --ntasks-per-node=1
#SBATCH --time=3:00:00
#SBATCH --mem=8gb
#SBATCH --output=stringtie_gtf.%J.out
#SBATCH --error=stringtie_gtf.%J.err
#SBATCH --account=rrg-ben


# run with
# sbatch ./stringtie.sh dir_name

module load StdEnv/2023 stringtie/3.0.1


for file in ${1}/*secondAligned.sortedByCoord.out.bam_rg.bam
do

	stringtie ${file} -o ${file}.gtf

done
```

```
ls -1 *secondAligned.sortedByCoord.out.bam_rg.bam.gtf > list_gtf.txt
```

### now merge her.....
```
#!/bin/sh
#SBATCH --job-name=stringtie_merge
#SBATCH --nodes=1
#SBATCH --ntasks-per-node=1
#SBATCH --time=3:00:00
#SBATCH --mem=8gb
#SBATCH --output=stringtie_merge.%J.out
#SBATCH --error=stringtie_merge.%J.err
#SBATCH --account=rrg-ben

module load StdEnv/2023 stringtie/3.0.1


stringtie --merge -o RNA_to_genome.gtf /home/froglady/projects/rrg-ben/froglady/2024_allo/transcriptome/DNA_files/list_gtf.txt

```

## and now we COUNT
### runs each file through featureCounts script
```
for file in /home/froglady/projects/rrg-ben/froglady/2024_allo/transcriptome/DNA_files/*secondAligned.sortedByCoord.out.bam_rg.bam
do
	sbatch ./feature_counts.sh ${file} ${file::-28}_COUNTS
done
```

### the script itself
```
#!/bin/sh
#SBATCH --job-name=featureCounts
#SBATCH --nodes=4
#SBATCH --ntasks-per-node=1
#SBATCH --time=3:00:00
#SBATCH --mem=4Gb
#SBATCH --output=featureCounts.%J.out
#SBATCH --error=featureCounts.%J.err
#SBATCH --account=rrg-ben

# sbatch ./feature_counts.sh inputbam output_counts

module load samtools/1.22.1 subread/2.0.6
module load StdEnv/2023 star/2.7.11b

# must use -s 0 because the data are unstranded
# must use -p because the data are paired
# use --countReadPairs to count read pairs instead of reads
# use -C to prevent counting of chimeric reads
# -T is the number of threads
featureCounts -T 4 -s 0 -p --countReadPairs -C \
  -a/home/froglady/projects/rrg-ben/froglady/2024_allo/transcriptome/DNA_files/RNA_to_genome.gtf \
  -o ${2} \
  ${1}
```
