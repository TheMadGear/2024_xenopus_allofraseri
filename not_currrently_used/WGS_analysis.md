# run trimmomatic

```
sbatch ../jade_scripts/2020_trimmomatic.sh ./
```

# bwa align files 

```
sbatch ../../jade_scripts/bwa_align_script.sh /home/froglady/projects/rrg-ben/froglady/2024_allo/2021_XL_v10_refgenome/XL_v10.1_concatenatedscaffolds.fa ./
```

# merge bam files

```
sbatch ../../jade_scripts/2024_bamtools_merge.sh BJE3486_merged_sorted.bam  BJE3486_S12_L001_sorted.bam BJE3486_S12_L002_sorted.bam
```

# add read groups

```
sbatch ../../jade_scripts/2024_add_readgroups.sh ./
```
