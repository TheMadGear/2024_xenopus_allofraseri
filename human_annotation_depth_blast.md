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

# cut out list of high depth gene names
```
cut -f4 FINAL_annotation_depth_exons.bed > FINAL_genes_depth_exons.bed
```

# grabbing all bed positions from laevis dictionary file
```
cut -f2,3 XENLA_10.1_GCF_XBmodels.transcripts.dict > laevis_all_bed.bed
sed -i -e 's/ LN:/ 1 /g' laevis_all_bed.bed
sed -i -e 's/SN://g' laevis_all_bed.bed
sed -i -e 's/ LN:/ 1 /g' laevis_all_bed.bed
vi all_laevis_all_bed.bed # remove first line


#cut -d '|' -f2 laevis_all_bed.bed > laev_allo_transcripts_RNA.bed # sends the mRNA names to another file
#sed -e 's/|.*|//' laevis_all_bed.bed > all_laevis_all_bed.bed # removes middle bit
```

# blast against laevis tx. to get fastas
```
bedtools getfasta -fi XENLA_10.1_GCF_XBmodels.transcripts.fa -bed laevis_all_bed.bed -fo all_XL_fasta.fa
```

# split laevis multifasta so it runs faster & rename as .fa file
```
split -n 10 -d all_XL_fasta.fa all_XL_fasta #(do same for each of 00-09)
mv all_XL_fasta03 all_XL_fasta03.fa
mv all_XL_fasta02 all_XL_fasta02.fa
mv all_XL_fasta01 all_XL_fasta01.fa
mv all_XL_fasta00 all_XL_fasta00.fa 
```

# and now to blast the split laevis fastas
```
blastn -query all_XL_fasta00.fa -db ../human_transcriptome/gencode.v42.transcripts.fa_blastable -outfmt 6 -out 00_all_laevis_human.out -task dc-megablast

blastn -query all_XL_fasta01.fa -db ../human_transcriptome/gencode.v42.transcripts.fa_blastable -outfmt 6 -out 01_all_laevis_human.out -task dc-megablast

blastn -query all_XL_fasta02.fa -db ../human_transcriptome/gencode.v42.transcripts.fa_blastable -outfmt 6 -out 02_all_laevis_human.out -task dc-megablast

blastn -query all_XL_fasta03.fa -db ../human_transcriptome/gencode.v42.transcripts.fa_blastable -outfmt 6 -out 03_all_laevis_human.out -task dc-megablast

blastn -query all_XL_fasta04.fa -db ../human_transcriptome/gencode.v42.transcripts.fa_blastable -outfmt 6 -out 04_all_laevis_human.out -task dc-megablast

blastn -query all_XL_fasta05.fa -db ../human_transcriptome/gencode.v42.transcripts.fa_blastable -outfmt 6 -out 05_all_laevis_human.out -task dc-megablast

blastn -query all_XL_fasta06.fa -db ../human_transcriptome/gencode.v42.transcripts.fa_blastable -outfmt 6 -out 06_all_laevis_human.out -task dc-megablast

blastn -query all_XL_fasta07.fa -db ../human_transcriptome/gencode.v42.transcripts.fa_blastable -outfmt 6 -out 07_all_laevis_human.out -task dc-megablast

blastn -query all_XL_fasta08.fa -db ../human_transcriptome/gencode.v42.transcripts.fa_blastable -outfmt 6 -out 08_all_laevis_human.out -task dc-megablast

blastn -query all_XL_fasta09.fa -db ../human_transcriptome/gencode.v42.transcripts.fa_blastable -outfmt 6 -out 09_all_laevis_human.out -task dc-megablast
```

# now to merge the out files back together
```
cat *all_laevis_human.out > merged_laev_hum_ann.out
```

#
# /home/froglady/projects/rrg-ben/for_jade/XL_CDS_only.fasta THIS is the XL ref for the allo/laev file
# /home/froglady/projects/rrg-ben/for_jade/XENLA_10.1_Xenbase.transcripts/XENLA_10.1_GCF_XBmodels.transcripts.fa
```

```

# now grab human gene annotations and add to allo high depth info
# /home/froglady/projects/rrg-ben/froglady/2024_allo/jade_scripts/FINAL_annotation_depth_exons.bed # allo/laev

## Chr1L	577332	578774	LOC108704856	3835	right overlap	tig00001724	2684931	2686077	XBmRNA28

# /home/froglady/projects/rrg-ben/for_jade/XENLA_10.1_Xenbase.transcripts/merged_laev_hum_ann.out # laev/human

## gnl|XBXL10_1g34611|XBmRNA65388|:1-4241	ENST00000251507.8|ENSG00000152061.24|OTTHUMG00000034899.9|OTTHUMT00000084497.1|RABGAP1L-201|RABGAP1L|2899|protein_coding|	67.814	1976	576	18	718	2681	675	2602	0.0	670


# should cut laev bed file & human gene from
# should find some measure of similarity between allo & human (i.e. blast allo to human for pident or smth)
```
cut -f1,2 merged_laev_hum_ann.out > lha_return.out
sed -i -e 's/|:/  /g' lha_return.out # add tab


|*|*|*|*|*|*|
```



