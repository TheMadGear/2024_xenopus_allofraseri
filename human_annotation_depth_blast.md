# scp file to compute canada
# rows: laev, laev, laev, laev, both, depth, allo, allo, allo
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



# THIS is the XL ref for the allo/laev file
## /home/froglady/projects/rrg-ben/for_jade/XL_CDS_only.fasta 
## >XBXL10_1g1_LOC108704873_XBmRNA2_XM_041586392.1_Chr1L:46948-47000
## TGAGGTGGCTCATACTTTTACTCTGGGGGGCAATAGGCCAAGGACTAGAACG

# this is the XL ref AND dict for laev/human
## /home/froglady/projects/rrg-ben/for_jade/XENLA_10.1_Xenbase.transcripts/XENLA_10.1_GCF_XBmodels.transcripts.fa
## >gnl|XBXL10_1g34611|XBmRNA65388| rabgap1.L
## TCACATGGCTATGACATCACAACCTGCCGAAAGCTACTCTATTCCAGTCGTTCATGGCCTGGATCTGGG

## /home/froglady/projects/rrg-ben/for_jade/XENLA_10.1_Xenbase.transcripts/XENLA_10.1_GCF_XBmodels.transcripts.dict
## @HD	VN:1.6
## @SQ	SN:gnl|XBXL10_1g34611|XBmRNA65388|	LN:4241	M5:74eeaa403596d49019c9ae7763444240	UR:file:/project/6019307/ben/2021_XL_ko_tad_RNAseq/XL_v10_transcriptome/XENLA_10.1_GCF_XBmodels.transcripts.fa


# first link XL .fa and .dict

```
# check which rows of .fa & .dict match
# make hash faSeq=dictSeq & faRow=dictRow

# nested loop- loop through each row of fa and check if it matches the dict entry

grep "gnl|XBXL10_1g34611|XBmRNA65388|" XENLA_10.1_GCF_XBmodels.transcripts.dict
grep "gnl|XBXL10_1g34611|XBmRNA65388|" XENLA_10.1_GCF_XBmodels.transcripts.fa

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


# hehehe redoing some stuff- space in fa file
# make new fa file and remove the space
# create dictionary file from this (NOW it will work)
```
cp XENLA_10.1_GCF_XBmodels.transcripts.fa XENLA_tab_rem.transcripts.fa
sed 's/ //' XENLA_tab_rem.transcripts.fa > XENLA_no_tab_rem.transcripts.fa
sbatch ./make_dict.sh /home/froglady/projects/rrg-ben/for_jade/XENLA_10.1_Xenbase.transcripts/XENLA_no_tab_rem.transcripts.fa
```

# this is the dict file
```
/home/froglady/projects/rrg-ben/froglady/2024_allo/jade_scripts/allo_reference_contigs.dict
```

# make bed file with sed
```
cut -f2,3 allo_reference_contigs.dict > laevis_all_bed.bed
sed -i -e 's/ LN:/ 1 /g' laevis_all_bed.bed
sed -i -e 's/SN://g' laevis_all_bed.bed
vi laevis_all_bed.bed # remove first line
```

# blast against no-space laev fasta
```
bedtools getfasta -fi XENLA_no_tab_rem.transcripts.fa -bed /home/froglady/projects/rrg-ben/froglady/2024_allo/jade_scripts/laevis_all_bed.bed -fo all_genes_XL_fasta.fa
```

```
split -n 10 -d all_genes_XL_fasta.fa all_genes_XL_fasta


mv all_genes_XL_fasta09 all_XL_fasta09.fa
mv all_genes_XL_fasta08 all_XL_fasta08.fa
mv all_genes_XL_fasta07 all_XL_fasta07.fa
mv all_genes_XL_fasta06 all_XL_fasta06.fa
mv all_genes_XL_fasta05 all_XL_fasta05.fa
mv all_genes_XL_fasta04 all_XL_fasta04.fa
mv all_genes_XL_fasta03 all_XL_fasta03.fa
mv all_genes_XL_fasta02 all_XL_fasta02.fa
mv all_genes_XL_fasta01 all_XL_fasta01.fa
mv all_genes_XL_fasta00 all_XL_fasta00.fa 
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

# ideal end file:
# laev name
# laev pos
# allo name
# allo pos
# human name

2: laev
3: human
7: laev start align
8: laev stop align
```
cut -f 1,2,7,8,12 merged_laev_hum_ann.out > laev_hum_bed.bed
```

