
# create blast database from laevis exons?
```
makeblastdb -in /home/froglady/projects/rrg-ben/froglady/2024_allo/2021_XL_v10_refgenome/XL_exons_filtered.fasta -dbtype nucl -out /home/froglady/projects/rrg-ben/froglady/2024_allo/2021_XL_v10_refgenome/laevis_exon_blastable
```

# find laevis exon positions in allofraseri contigs 
```
blastn -query /home/froglady/projects/rrg-ben/for_jade/Adam_allo_genome_assembly_with_bubbles/allo.fasta.contigs.fasta.fai -subject  /home/froglady/projects/rrg-ben/froglady/2024_allo/2021_XL_v10_refgenome/XL_exons_filtered.fasta -outfmt 6 -out allo_depth_pos.txt
```

# 





