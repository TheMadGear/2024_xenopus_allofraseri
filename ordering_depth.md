## query: XL exons (7-8, G-H)
## db: allo assembly (9-10, I-J)

## re-make *proper* blast database from allofraseri assembly (db was probably fine, just length of time)
```
makeblastdb -in /home/froglady/projects/rrg-ben/for_jade/Adam_allo_genome_assembly_with_bubbles/allo.fasta.contigs.fasta-dbtype nucl -out /home/froglady/projects/rrg-ben/for_jade/Adam_allo_genome_assembly_with_bubbles/allo_redone_blastable
```

## find laevis exon positions in allofraseri contigs 
```
# original
blastn -query /home/froglady/projects/rrg-ben/froglady/2024_allo/2021_XL_v10_refgenome/XL_exons_filtered.fasta -db /home/froglady/projects/rrg-ben/for_jade/Adam_allo_genome_assembly_with_bubbles/allo_redone_blastable -outfmt 6 -out allo_depth_pos.txt

# with gene names (Ben file)
blastn -query /home/froglady/projects/rrg-ben/for_jade/XL_CDS_only.fasta -db /home/froglady/projects/rrg-ben/for_jade/Adam_allo_genome_assembly_with_bubbles/allo.fasta.contigs.fasta_blastable -outfmt 6 -out allo_CDS_depth_pos.txt
```

## filter based on length (remove match < 200 bp)
```
# original
 awk  '{ if ($4 > 200) print $0}' allo_depth_pos.txt > allo_depth_pos_filter200.txt

# with gene names (Ben file)
 awk  '{ if ($4 > 200) print $0}' allo_CDS_depth_pos.txt > allo_CDS_depth_pos_filter200.txt
```

## switch negative strands in bed file ALSO include gene name
```
 awk '{print $2, $9, $10, $1}' allo_depth_pos_filter200.txt > awk_bed_allo_ordered_contigs.bed

# switch start/end
../jade_scripts/sam_switch_bed.sh awk_bed_allo_ordered_contigs.bed true_switched_allo_ordered_contigs
head true_switched_allo_ordered_contigs.bed
```



# export to excel with scp
```
cd /Users/jadebohbot/Downloads/Masters_Documents/Depth
scp froglady@nibi.alliancecan.ca:/home/froglady/projects/rrg-ben/froglady/2024_allo/2021_XL_v10_refgenome/true_switched_allo_ordered_contigs.bed .
```


# at the very end I think can probably use laevis GTF file to get gene names and/pr gene IDs

