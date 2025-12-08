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

# make .dict and .fai files for human tx. and laevis tx.
```
sbatch ./2021_picard_dict.sh gencode.v42.transcripts
sbatch ../humans/2021_picard_dict.sh XENLA_10.1_Xenbase.transcripts
```




# not run yet

# fixing bed file 
# pull out name and length from dict with egrep



# creation of a beautiful human tx. bed file
```
# try for human
cut -f2,3 gencode.v42.transcripts.dict > hum_laev_allo_transcripts.bed
sed -i -e 's/SN://g' hum_laev_allo_transcripts.bed
sed -i -e 's/ LN:/ 1 /g' hum_laev_allo_transcripts.bed

# now THIS is a bed file
 sed -e 's/|.*|//' hum_laev_allo_transcripts.bed > test1.bed
sed -i -e 's/ LN:/ 1 /g' hum_laev_allo_transcripts.bed




vi hum_laev_allo_transcripts.bed # remove first line

# try for laevis
cut -f2,3 XENLA_10.1_Xenbase.transcripts.dict > laev_allo_transcripts.bed
sed -i -e 's/SN://g' laev_allo_transcripts.bed
sed -i -e 's/ LN:/ 1 /g' laev_allo_transcripts.bed
vi laev_allo_transcripts.bed # remove first line

```

```

# getfasta of human seqs that match laevis header
```

```







########################################################################################
# using getfasta instead of grep......
```
bedtools getfasta -fi  /home/froglady/projects/rrg-ben/for_jade/XENLA_10.1_Xenbase.transcripts/XENLA_10.1_Xenbase.transcripts.fa  -bed FINAL_annotation_depth_exons.bed -fo gene_fasta_laevis.fasta
```









# grab fasta seqs from laevis transcriptome
# right now it just grabs header- need fasta
# add -10 stuff back from ben's code
```
grep -f top3_allo_genes_depth.txt /home/froglady/projects/rrg-ben/for_jade/XENLA_10.1_Xenbase.transcripts/XENLA_10.1_Xenbase.transcripts.fa > top3_windows_xenLaev.fasta
```

# blast fasta seqs against human tx.














