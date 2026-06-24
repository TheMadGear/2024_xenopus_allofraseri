
# import up/downregulated tx. to unix
# remove header
```
sed -i'' -e '1d' downregulated_overlap_allo.csv
sed -i'' -e '1d' upregulated_overlap_allo.csv
```
# match up to RNA_to_genome.gtf  
```
grep -f upregulated_overlap_allo.csv RNA_to_genome.gtf  > upreg_info.txt
grep -f downregulated_overlap_allo.csv RNA_to_genome.gtf  > downreg_info.txt
```

# blast against laevis or allo annotation?










