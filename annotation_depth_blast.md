# scp file to compute canada
```
scp ./top3_allo_depth_annotation.bed
 froglady@nibi.alliancecan.ca:/home/froglady/projects/rrg-ben/froglady/2024_allo/jade_scripts/
```

# blast her
```
bedtools getfasta -bed top3_allo_depth_annotation.bed -fi /home/froglady/projects/rrg-ben/froglady/2024_allo/2021_XL_v10_refgenome/XENLA_10.1_genome.fa.gz  -fo high_depth_output_laev.fasta
```

# blast her AGAIN with human genome....
```

```













