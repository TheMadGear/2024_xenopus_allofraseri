
# occurs in /transcriptome/DNA_files/

# import up/downregulated tx. to unix
# remove header
```
sed -i'' -e '1d' downregulated_overlap_allo.csv
sed -i'' -e '1d' upregulated_overlap_allo.csv
```
# match up to RNA_to_genome.gtf  
```
grep -f upregulated_overlap_allo.csv RNA_to_genome.gtf  > upreg_info.txt
grep -f downregulated_overlap_allo.csv RNA_to_genome.gtf  > downreg_info.txt
```

# make bed file
```
awk -F "\t" '{print $1, $4, $5, $9}' upreg_info.txt > upreg_info.bed
awk -F "\t" '{print $1, $4, $5, $9}' downreg_info.txt > downreg_info.bed
```

# fix tabs
```
sed 's/ / /g' upreg_info.bed > upreg_info1.bed
sed 's/ / /g' downreg_info.bed > downreg_info1.bed
```

```
awk -F "\t" '{print $1, $2, $3}' upreg_info1.bed > upreg_info2.bed
awk -F "\t" '{print $1, $2, $3}' downreg_info1.bed > downreg_info2.bed
```

```
sed 's/ / /g' upreg_info2.bed > upreg_info3.bed
sed 's/ / /g' downreg_info2.bed > downreg_info3.bed
```

# bedtools grab fasta from allo assembly
```
bedtools getfasta -fi /home/froglady/projects/rrg-ben/for_jade/Adam_allo_genome_assembly_with_bubbles/allo.fasta.contigs.fasta -bed upreg_info3.bed -fo upreg.fasta

bedtools getfasta -fi /home/froglady/projects/rrg-ben/for_jade/Adam_allo_genome_assembly_with_bubbles/allo.fasta.contigs.fasta -bed downreg_info3.bed -fo downreg.fasta\
```

# blast against laevis (using sbatch)
```
sbatch ./blast.sh
```

```
blastn -query downreg.fasta  -db /home/froglady/projects/rrg-ben/froglady/2024_allo/XENLA_v10.1/laevis_reference_genome.fa_blastable -outfmt 6 -out downreg_blast_laev.out

blastn -query upreg.fasta  -db /home/froglady/projects/rrg-ben/froglady/2024_allo/XENLA_v10.1/laevis_reference_genome.fa_blastable -outfmt 6 -out upreg_blast_laev.out
```
# PARSE IN R to create bed file with extra columns

# remove header
```
tail -n +2 flipped_annotation.bed > flipped_annotation1.bed
```

# read in to unix, remove quotes, including annotation file
```
cat upreg_parse.bed | tr -d '"' > upreg_p.bed
cat downreg_parse.bed | tr -d '"' > downreg_p.bed
cat flipped_annotation1.bed | tr -d '"' > flipped_annotation2.bed
```

# FIRST HAVE TO FLIP BED COORDS (done in R)


# check intersection HERE USE LAEVIS GTF INSTEAD
```
intersectBed -wa -wb -a flipped_annotation2.bed -b upreg_p1.bed downreg_p1.bed -names up down 
```

# grab only gene names (also direction of expression but this will not matter- can pull out every copy of gene of interest and see expression direction)
```
awk -F "\t" '{print $5, $7}' up_down_genes.txt > DE_genes_only.txt
```

# grab only unique genes
```
cat DE_genes_only.txt | sort -u -k1,1 > unq_DE.txt
```

# unique gene names
```
cat DE_genes_only.txt | sort -u -k1,1 > unq_DE.txt
```







