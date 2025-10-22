# query: XL exons (7-8, G-H)
# db: allo assembly (9-10, I-J)

# re-make *proper* blast database from allofraseri assembly (db was probably fine, just length of time)
```
makeblastdb -in /home/froglady/projects/rrg-ben/for_jade/Adam_allo_genome_assembly_with_bubbles/allo.fasta.contigs.fasta-dbtype nucl -out /home/froglady/projects/rrg-ben/for_jade/Adam_allo_genome_assembly_with_bubbles/allo_redone_blastable
```

# find laevis exon positions in allofraseri contigs 
```
blastn -query /home/froglady/projects/rrg-ben/froglady/2024_allo/2021_XL_v10_refgenome/XL_exons_filtered.fasta -db /home/froglady/projects/rrg-ben/for_jade/Adam_allo_genome_assembly_with_bubbles/allo_redone_blastable -outfmt 6 -out allo_depth_pos.txt
```

# export to excel with scp
```
scp froglady@nibi.alliancecan.ca:/home/froglady/projects/rrg-ben/for_jade/Adam_allo_genome_assembly_with_bubbles/allo_depth_pos.txt .
```

# next steps:
* automate using GTF file:
* use laevis GTF file to create allo GTF file with ONLY exons




