
# occurs in /transcriptome/DNA_files/

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

# make bed file
```
awk -F "\t" '{print $1, $4, $5, $9}' upreg_info.txt > upreg_info.bed
awk -F "\t" '{print $1, $4, $5, $9}' downreg_info.txt > downreg_info.bed
```

# fix tabs
```
sed 's/ / /g' upreg_info.bed > upreg_info1.bed
sed 's/ / /g' downreg_info.bed > downreg_info1.bed
```

```
awk -F "\t" '{print $1, $2, $3}' upreg_info1.bed > upreg_info2.bed
awk -F "\t" '{print $1, $2, $3}' downreg_info1.bed > downreg_info2.bed
```

```
sed 's/ / /g' upreg_info2.bed > upreg_info3.bed
sed 's/ / /g' downreg_info2.bed > downreg_info3.bed
```

# bedtools grab fasta from allo assembly
```
bedtools getfasta -fi /home/froglady/projects/rrg-ben/for_jade/Adam_allo_genome_assembly_with_bubbles/allo.fasta.contigs.fasta -bed upreg_info3.bed -fo upreg.fasta

bedtools getfasta -fi /home/froglady/projects/rrg-ben/for_jade/Adam_allo_genome_assembly_with_bubbles/allo.fasta.contigs.fasta -bed downreg_info3.bed -fo downreg.fasta\
```

# blast against laevis (using sbatch)
```
sbatch ./blast.sh
```

```
blastn -query downreg.fasta  -db /home/froglady/projects/rrg-ben/froglady/2024_allo/XENLA_v10.1/laevis_reference_genome.fa_blastable -outfmt 6 -out downreg_blast_laev.out

blastn -query upreg.fasta  -db /home/froglady/projects/rrg-ben/froglady/2024_allo/XENLA_v10.1/laevis_reference_genome.fa_blastable -outfmt 6 -out upreg_blast_laev.out
```
# PARSE IN R to create bed file with extra columns AND filter by PIDENT so each unique allo location has the best pident match

```{r}
# for loop here to filter by pident

unq_allo <- unique(upreg$V1)

fill_frame <- data.frame(V1 = c(), V2 = c(), pident = c(), length = c(), V5 = c(), V6 = c(), V7 = c(),
                         V8 = c(), V9 = c(), V10 = c(), V11 = c(), V12 = c())
colnames(upreg) <- c("V1", "V2", "pident", "length", "V5", "V6", "V7", "V8", "V9", "V10", "V11", "V12")

# set colnames for pident and length

for(a in 1:length(unq_allo))
{
  sub <- subset(upreg, V1 == unq_allo[a])
  max_pid <- subset(sub, pident == max(pident))
  max_len <- subset(max_pid, length == max(length))   
  
  fill_frame <- rbind(fill_frame, max_len)
}

upp <- fill_frame

```

```{r}
unq_allo1 <- unique(downreg$V1)

fill_frame1 <- data.frame(V1 = c(), V2 = c(), pident = c(), length = c(), V5 = c(), V6 = c(), V7 = c(),
                         V8 = c(), V9 = c(), V10 = c(), V11 = c(), V12 = c())
colnames(downreg) <- c("V1", "V2", "pident", "length", "V5", "V6", "V7", "V8", "V9", "V10", "V11", "V12")

# set colnames for pident and length

for(a in 1:length(unq_allo1))
{
  sub <- subset(downreg, V1 == unq_allo1[a])
  max_pid <- subset(sub, pident == max(pident))
  max_len <- subset(max_pid, length == max(length))   
  
  fill_frame1 <- rbind(fill_frame1, max_len)
}

nrow(fill_frame1)
nrow(downreg)
length(unq_allo1)

downn <- fill_frame1

```


```{r}

now_split <- as.data.frame(str_split_fixed(upp$V1, ":", 2))

now_split1 <- as.data.frame(str_split_fixed(now_split$V2, "-", 2))


pos <- cbind(now_split$V1, now_split1$V1, now_split1$V2)

up1 <- cbind(pos, upp)

colnames(up1) <- c("tig", "start", "stop", "pos", "Chr_laev", "pident", "length", "mismatch", "gapopen", "qstart", "qend", "eval", "bitscore")

write.table(up1, file = "upreg_parse.bed", sep = "\t", row.names = FALSE, col.names = F)

```


```{r}
now_split <- as.data.frame(str_split_fixed(downn$V1, ":", 2))

now_split1 <- as.data.frame(str_split_fixed(now_split$V2, "-", 2))


pos <- cbind(now_split$V1, now_split1$V1, now_split1$V2)

down1 <- cbind(pos, downn)

colnames(up1) <- c("tig", "start", "stop", "pos", "Chr_laev", "pident", "length", "mismatch", "gapopen", "qstart", "qend", "eval", "bitscore")

write.table(down1, file = "downreg_parse.bed", sep = "\t", row.names = FALSE, col.names = F)
```

# remove header NOT NEEDED, NO ANN
```
tail -n +2 flipped_annotation.bed > flipped_annotation1.bed
```

# read in to unix, remove quotes, including annotation file
```
cat upreg_parse.bed | tr -d '"' > upreg_p.bed
cat downreg_parse.bed | tr -d '"' > downreg_p.bed
cat flipped_annotation1.bed | tr -d '"' > flipped_annotation2.bed
```

# fixing LAEVIS GTF here
# use awk to turn upreg_p.bed and downreg_p.bed into bed files with additional allo info
```
awk -F "\t" '{print $5, $12, $13, $4}' downreg_p.bed > downreg_p1x.bed
awk -F "\t" '{print $5, $12, $13, $4}' upreg_p.bed > upreg_p1x.bed
```

# fix tab delimit
```
sed 's/ / /g' downreg_p1x.bed > downreg_x.bed
sed 's/ / /g' upreg_p1x.bed > upreg_x.bed
```







# turn laevis full gtf file into a bed file PLUS column with gene names to do bedIntersect on

# check intersection HERE USE LAEVIS GTF INSTEAD
```
intersectBed -wa -wb -a flipped_annotation2.bed -b upreg_p1.bed downreg_p1.bed -names up down 
```

# grab only gene names (also direction of expression but this will not matter- can pull out every copy of gene of interest and see expression direction)
```
awk -F "\t" '{print $5, $7}' up_down_genes.txt > DE_genes_only.txt
```

# grab only unique genes
```
cat DE_genes_only.txt | sort -u -k1,1 > unq_DE.txt
```

# unique gene names
```
cat DE_genes_only.txt | sort -u -k1,1 > unq_DE.txt
```







