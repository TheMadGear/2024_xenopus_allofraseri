# from redo_laev_hum_allo



# parse laevis information into columns
# only using largest exon per gene to get annotation for each gene because length-wise this will be the best match
# for each UNIQUE laevis gene, extract largest exon
```
column -s "_" -t human_laev_CDS_depth_pos.bed > laev_hum_large_exon.bed

column -s ":" -t laev_hum_large_exon.bed > laev_hum_large.bed

```

# YEEHOO it replaces the "-" in column 8 (position on chromosome) into 2 columns (start & end- 8 & 9)
```
awk '{gsub("-","	",$8)}1' laev_hum_large.bed > LH_test.bed
```

# this almost does what I want but only includes col 1-6
```
awk 'BEGIN{OFS="_"} {print $1, $2, $3, $4, $5, $6}' LH_test.bed > LH_gene.bed
```


# blast XL against human

# exon 1 and exon 2 from 
# pick longest one
# one exon per gene (longest)
# hit to LAEVIS that has longest size

# just for annotation- grab biggest exon from each gene > new file







