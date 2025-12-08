# scp file to compute canada
```
scp /Users/jadebohbot/FINAL_annotation_depth_exons.bed.txt froglady@nibi.alliancecan.ca:/home/froglady/projects/rrg-ben/froglady/2024_allo/jade_scripts/
```

# cut out gene names in laevis
```
cut -f4 FINAL_annotation_depth_exons.bed.txt > all_allo_genes_depth.txt
```

# change name to bed file
```
mv FINAL_annotation_depth_exons.bed.txt FINAL_annotation_depth_exons.bed
```

# using getfasta instead of grep......
```
bedtools getfasta -fi  /home/froglady/projects/rrg-ben/for_jade/XENLA_10.1_Xenbase.transcripts/XENLA_10.1_Xenbase.transcripts.fa  -bed FINAL_annotation_depth_exons.bed -fo gene_fasta_laevis.fasta
```





###############################################################################
# cat to new file so can replace newlines for grep to search correctly
```
cat top3_windows_xenLaev.fasta > newline_top3_windows_xenLaev.fasta
```

# make sure delimiter is correct for grep
```
awk '/^>/ {printf("\n%s\n",$0);next; } { printf("%s",$0);}  END {printf("\n");}' < newline_top3_windows_xenLaev.fasta > grep_fixed_top3_windows_xenLaev.fasta
```

###############################################################################


# grab fasta seqs from laevis transcriptome
# right now it just grabs header- need fasta
# add -10 stuff back from ben's code
```
grep -f top3_allo_genes_depth.txt /home/froglady/projects/rrg-ben/for_jade/XENLA_10.1_Xenbase.transcripts/XENLA_10.1_Xenbase.transcripts.fa > top3_windows_xenLaev.fasta
```

# blast fasta seqs against human tx.





# blast her
```
bedtools getfasta -bed top3_allo_depth_annotation.bed -fi /home/froglady/projects/rrg-ben/froglady/2024_allo/2021_XL_v10_refgenome/XENLA_10.1_genome.fa.gz  -fo high_depth_output_laev.fasta
```













