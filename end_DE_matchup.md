
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

# read in to unix, remove quotes, including annotation file
```
cat upreg_parse.bed | tr -d '"' > upreg_p.bed
cat downreg_parse.bed | tr -d '"' > downreg_p.bed
cat exon_annotation_final.txt | tr -d '"' > exon_annotation_final_NQ.txt
```

# remove header
```
tail -n +2 exon_annotation_final_NQ.txt > exon_annotation_final_NQ1.txt
```

# check intersection
```
intersectBed -wa -wb -a exon_annotation_final_DONE.txt -b upreg_p1.bed downreg_p1.bed -names up down -wo
```








