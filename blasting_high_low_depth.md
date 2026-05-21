# Ben Solution


# make dictionary file
```
#!/bin/sh
#SBATCH --job-name=dictionary
#SBATCH --nodes=1
#SBATCH --ntasks-per-node=1
#SBATCH --time=24:00:00
#SBATCH --mem=8gb
#SBATCH --account=rrg-ben


module load java
module load StdEnv/2023 picard/3.1.0


# code to run: sbatch make_dict.sh allo.fasta.contigs.fasta 

java -jar $EBROOTPICARD/picard.jar CreateSequenceDictionary REFERENCE=${1} OUTPUT=allo_reference_contigs.dict
```

# grep location (SN) from .dict file and manually make .bed file
``` 
 grep "SN:tig00001724" allo_reference_contigs.dict
```
# grab fasta from allo multifasta file
```
bedtools getfasta -bed tig1724.bed -fi allo.fasta.contigs.fasta  -fo tig1724.fa
```
# blast allo fasta file against laevis reference (NOT WORKING PROPERLY ....hmmm no yes it is)
```
blastn -query /home/froglady/projects/rrg-ben/for_jade/Adam_allo_genome_assembly_with_bubbles/tig1724.fa -db /home/froglady/projects/rrg-ben/froglady/2024_allo/XENLA_v10.1/laevis_reference_genome.fa_blastable -outfmt 6 -task dc-megablast -out tig1724.out
```

# WRITE MORE CODE that specifically pulls out the area of high depth from the contig
```
# RUN IN R
small2 <- subset(c6146, M_F_dif >= 2000, select = c(start, M_F_dif)) # 3300000
```

```
#!/bin/sh
#SBATCH --job-name=allo_v_laev
#SBATCH --nodes=1
#SBATCH --ntasks-per-node=1
#SBATCH --time=24:00:00
#SBATCH --mem=8gb
#SBATCH --output=allo_v_laev.%J.out
#SBATCH --error=allo_v_laev.%J.err
#SBATCH --account=rrg-ben

# run by passing: sbatch allo_v_laev.sh tigName NO EXTENSION !!!

module load StdEnv/2023
module load gcc/12.3 blast+/2.14.1

blastn -query /home/froglady/projects/rrg-ben/for_jade/Adam_allo_genome_assembly_with_bubbles/${1}.fa -db /home/froglady/projects/rrg-ben/froglady/2024_allo/XENLA_v10.1/laevis_reference_genome.fa_blastable -outfmt 6 -task dc-megablast -out ${1}.out
                                                                                       
```
- filter blast result by top 20 by percent id
# the sort code works!!!!
```
sort -r -n -k 4 tig1764.out > tig1764_sorted.out
```



