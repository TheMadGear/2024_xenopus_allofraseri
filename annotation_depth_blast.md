# scp file to compute canada
```
scp ./male_filtered_blast_depth.bed froglady@nibi.alliancecan.ca:/home/froglady/projects/rrg-ben/for_jade/Adam_allo_genome_assembly_with_bubbles/
```

# blast her
```
bedtools getfasta -bed /home/froglady/projects/rrg-ben/froglady/2024_allo/jade_scripts/male_filtered_blast_depth.bed -fi /home/froglady/projects/rrg-ben/froglady/2024_allo/2021_XL_v10_refgenome/XENLA_10.1_genome.fa.gz -fo high_depth_output_laev.fasta
```













