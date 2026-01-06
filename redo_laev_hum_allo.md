


# redo blast to make sure laevis names in each file (laev_hum & laev_allo) match
#  XENLA_10.1_Xenbase.transcripts.fa file for XL transcripts matches CDS only fasta

## allo & laevis
```
blastn -query /home/froglady/projects/rrg-ben/for_jade/XL_CDS_only.fasta -db /home/froglady/projects/rrg-ben/for_jade/Adam_allo_genome_assembly_with_bubbles/allo.fasta.contigs.fasta_blastable -outfmt 6 -out allo_CDS_depth_pos.txt
# change to bed file
mv allo_CDS_depth_pos.txt allo_CDS_depth_pos.bed
```

# human & laevis
```
blastn -query /home/froglady/projects/rrg-ben/for_jade/XL_CDS_only.fasta -db /home/froglady/projects/rrg-ben/for_jade/human_transcriptome/gencode.v42.transcripts.fa_blastable -outfmt 6 -out human_laev_CDS_depth_pos.txt
# change to bed file
mv human_laev_CDS_depth_pos.txt human_laev_CDS_depth_pos.bed
```

# ok
```
# copy tx. files to MY folder

cp /home/froglady/projects/rrg-ben/for_jade/XENLA_10.1_Xenbase.transcripts/XENLA_10.1_Xenbase.transcripts* .

```

# parse laevis information into columns
# only using largest exon per gene to get annotation for each gene because length-wise this will be the best match
# for each UNIQUE laevis gene, extract largest exon
```
column -s "_" -t human_laev_CDS_depth_pos.bed > laev_hum_large_exon.bed

column -s ":" -t laev_hum_large_exon.bed > laev_hum_large.bed

```
# blast XL against human

# exon 1 and exon 2 from 
# pick longest one
# one exon per gene (longest)
# hit to LAEVIS that has longest size

# just for annotation- grab biggest exon from each gene > new file



