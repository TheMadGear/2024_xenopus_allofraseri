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

# run with sbatch ./STAR_index_GTF.sh .

module load star/2.7.11b

STAR --runMode genomeGenerate \
--genomeDir /home/froglady/projects/rrg-ben/for_jade/Adam_allo_genome_assembly_with_bubbles/ \
--genomeFastaFiles /home/froglady/projects/rrg-ben/for_jade/Adam_allo_genome_assembly_with_bubbles/allo.fasta.contigs_nobubbles.fasta \
--runThreadN 8 \
--limitGenomeGenerateRAM=124544990592
```

## step 2 (STAR map)
```
# current error:

EXITING: FATAL INPUT ERROR: empty value for parameter "genomeDir" in input "Command-Line"
SOLUTION: use non-empty value for this parameter

Nov 04 16:22:42 ...... FATAL ERROR, exiting
!!!!! WARNING:  Could not ls *R1.fastq

EXITING: because of fatal INPUT file error: could not open read file: *R1.fastq
SOLUTION: check that this file exists and has read permision.

Nov 04 16:22:42 ...... FATAL ERROR, exiting
```



