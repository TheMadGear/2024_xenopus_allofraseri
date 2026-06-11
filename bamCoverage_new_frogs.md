# script to move bam files from Ben's folder to /home/froglady/projects/rrg-ben/froglady/2024_allo/new_bam/
```
#!/bin/sh
#SBATCH --job-name=copy_files
#SBATCH --nodes=4
#SBATCH --ntasks-per-node=4
#SBATCH --time=40:00:00
#SBATCH --mem=16gb
#SBATCH --output=copy_files.%J.out
#SBATCH --error=copy_files.%J.err
#SBATCH --account=rrg-ben


cp /home/froglady/projects/rrg-ben/for_jade/2025_allo_bamz/*bam /home/froglady/projects/rrg-ben/for_jade/2025_allo_bamz/*bai /home/froglady/projects/rrg-ben/froglady/2024_allo/new_bam/
```

# use loop to run so many files
```
for f in /home/froglady/projects/rrg-ben/froglady/2024_allo/new_bam/*bam; do
  sbatch /home/froglady/projects/rrg-ben/froglady/2024_allo/jade_scripts/try_bam_coverage.sh ${f} 
done
```

# of course it didn't run them all
```
sbatch /home/froglady/projects/rrg-ben/froglady/2024_allo/jade_scripts/try_bam_coverage.sh male_Z23738_nobubble_sorted_rg.bam   

sbatch /home/froglady/projects/rrg-ben/froglady/2024_allo/jade_scripts/try_bam_coverage.sh female_allo_17A7_nobubble_sorted_rg.bam
```









# with bubbles for depth bc some alleles present only in M
#

sbatch /home/froglady/projects/rrg-ben/froglady/2024_allo/jade_scripts/try_bam_coverage.sh *bam .


# bamCoverage script
```
# run with sbatch /home/froglady/projects/rrg-ben/froglady/2024_allo/jade_scripts/try_bam_coverage.sh *bam

#!/bin/sh
#SBATCH --job-name=bamCoverage
#SBATCH --nodes=1
#SBATCH --ntasks-per-node=1
#SBATCH --time=2:00:00
#SBATCH --mem=8gb
#SBATCH --output=bamCoverage.%J.out
#SBATCH --error=bamCoverage.%J.err
#SBATCH --account=def-ben

module load python/3.11.5
module load StdEnv/2023 python/3.11.5
module load python scipy-stack
import bamCoverage from deepTools

srun --ntasks $SLURM_NNODES --tasks-per-node=1 bash << EOF
virtualenv --no-download $SLURM_TMPDIR/env
source $SLURM_TMPDIR/env/bin/activate
pip install --no-index --upgrade pip
pip install --no-index deepTools

bamCoverage -b ${1} --outFileFormat bedgraph --binSize 100 --ignoreDuplicates --minMappingQuality 30 --normalizeUsing RPKM -o newTig_output_bamCoverage.bw

EOF

```


# better .sh file
```
#!/bin/sh
#SBATCH --job-name=bamCoverage
#SBATCH --nodes=1
#SBATCH --ntasks-per-node=1
#SBATCH --time=24:00:00
#SBATCH --mem=8gb
#SBATCH --output=bamCoverage.%J.out
#SBATCH --error=bamCoverage.%J.err
#SBATCH --account=rrg-ben

module load python/3.11.5
module load StdEnv/2023 python/3.11.5
module load python scipy-stack
# import bamCoverage from deepTools 

srun --ntasks $SLURM_NNODES --tasks-per-node=1 bash << EOF
virtualenv --no-download $SLURM_TMPDIR/env
source $SLURM_TMPDIR/env/bin/activate
pip install --no-index --upgrade pip
pip install --no-index deepTools


bamCoverage -b ${1} --outFileFormat bedgraph --binSize 100000 --ignoreDuplicates --minMappingQuality 30 --normalizeUsing RPKM -o ${1}_bamCoverage.bw

EOF

```



# loop run all files
```
for f in ./*bam; do
  sbatch ./bam_cov.sh ${f} 
done
```






# alternate run:
# in folder /home/froglady/projects/rrg-ben/froglady/2024_allo/depth_bams/ using bam_cov.sh


# the R file:
# save_R_code.rmd has saved code

```{r}
# the girls (blast out)
L_H <- read.table("/Users/jadebohbot/human_laev_CDS_depth_pos.bed", header = F, fill = TRUE)
A_L <- read.table("/Users/jadebohbot/allo_CDS_depth_pos.bed", header = F, fill = TRUE)


```


```{r}
library(ggplot2)
library(MLEcens)
library (ggplot2)
library(tidyverse)
library(reshape2)

```

```{r}
# beautiful and correct annotation file!!!!!!!!
L_H <- read.table("/Users/jadebohbot/mega_human_laev_CDS_depth_pos.txt", header = F, fill = TRUE)
```


# oh goodness new data read-in!!!
```{r}

# ladies first :)

F_17A7 <- read.table("/Users/jadebohbot/female_allo_17A7_sorted_rg.bam_RPKMnorm_bamCoverage_10kb_fixedrowz.bw", header = T)
colnames(F_17A7) <- c("contig","start","stop","F_17A7")

F_17AB <- read.table("/Users/jadebohbot/female_allo_17AB_sorted_rg.bam_RPKMnorm_bamCoverage_10kb_fixedrowz.bw", header = T)
colnames(F_17AB) <- c("contig","start","stop","F_17AB")

F_17C2 <- read.table("/Users/jadebohbot/female_allo_17C2_sorted_rg.bam_RPKMnorm_bamCoverage_10kb_fixedrowz.bw", header = T)
colnames(F_17C2) <- c("contig","start","stop","F_17C2")

F_17E2 <- read.table("/Users/jadebohbot/female_allo_17E2_sorted_rg.bam_RPKMnorm_bamCoverage_10kb_fixedrowz.bw", header = T)
colnames(F_17E2) <- c("contig","start","stop","F_17E2")


F_BJE3487 <- read.table("/Users/jadebohbot/female_BJE3487_sorted_rg.bam_RPKMnorm_bamCoverage_10kb_fixedrowz.bw", header = T)
colnames(F_BJE3487) <- c("contig","start","stop","F_BJE3487")

F_BJE3488 <- read.table("/Users/jadebohbot/female_BJE3488_sorted_rg.bam_RPKMnorm_bamCoverage_10kb_fixedrowz.bw", header = T)
colnames(F_BJE3488) <- c("contig","start","stop","F_BJE3488")


F_BJE3501 <- read.table("/Users/jadebohbot/female_BJE3501_sorted_rg.bam_RPKMnorm_bamCoverage_10kb_fixedrowz.bw", header = T)
colnames(F_BJE3501) <- c("contig","start","stop","F_BJE3501")


F_BJE3502 <- read.table("/Users/jadebohbot/female_BJE3502_sorted_rg.bam_RPKMnorm_bamCoverage_10kb_fixedrowz.bw", header = T)
colnames(F_BJE3502) <- c("contig","start","stop","F_BJE3502")

F_camF1 <- read.table("/Users/jadebohbot/female_camF1_sorted_rg.bam_RPKMnorm_bamCoverage_10kb_fixedrowz.bw", header = T)
colnames(F_camF1) <- c("contig","start","stop", "F_camF1")

F_Z23698 <- read.table("/Users/jadebohbot/female_Z23698_sorted_rg.bam_RPKMnorm_bamCoverage_10kb_fixedrowz.bw", header = T)
colnames(F_Z23698) <- c("contig","start","stop", "F_Z23698")


F_Z23702 <- read.table("/Users/jadebohbot/female_Z23702_sorted_rg.bam_RPKMnorm_bamCoverage_10kb_fixedrowz.bw", header = T)
colnames(F_Z23702) <- c("contig","start","stop", "F_Z23702")


F_Z23721 <- read.table("/Users/jadebohbot/female_Z23721_sorted_rg.bam_RPKMnorm_bamCoverage_10kb_fixedrowz.bw", header = T)
colnames(F_Z23721) <- c("contig","start","stop", "F_Z23721")



F_Z23726 <- read.table("/Users/jadebohbot/female_Z23726_sorted_rg.bam_RPKMnorm_bamCoverage_10kb_fixedrowz.bw", header = T)
colnames(F_Z23726) <- c("contig","start","stop", "F_Z23726")


F_Z23732 <- read.table("/Users/jadebohbot/female_Z23732_sorted_rg.bam_RPKMnorm_bamCoverage_10kb_fixedrowz.bw", header = T)
colnames(F_Z23732) <- c("contig","start","stop", "F_Z23732")

F_Z23733 <- read.table("/Users/jadebohbot/female_Z23733_sorted_rg.bam_RPKMnorm_bamCoverage_10kb_fixedrowz.bw", header = T)
colnames(F_Z23733) <- c("contig","start","stop", "F_Z23733")

F_Z23698 <- read.table("/Users/jadebohbot/female_Z23698_sorted_rg.bam_RPKMnorm_bamCoverage_10kb_fixedrowz.bw", header = T)
colnames(F_Z23698) <- c("contig","start","stop", "F_Z23733")



```

```{r}


# THE BOYSSSSSSS

M_17A1 <- read.table("/Users/jadebohbot/male_allo_17A1_sorted_rg.bam_RPKMnorm_bamCoverage_10kb_fixedrowz.bw", header = T)
colnames(M_17A1) <- c("contig","start","stop", "M_17A1")



M_18A1 <- read.table("/Users/jadebohbot/male_allo_18A1_sorted_rg.bam_RPKMnorm_bamCoverage_10kb_fixedrowz.bw", header = T)
colnames(M_18A1) <- c("contig","start","stop", "M_18A1")

M_18AF <- read.table("/Users/jadebohbot/male_allo_18AF_sorted_rg.bam_RPKMnorm_bamCoverage_10kb_fixedrowz.bw", header = T)
colnames(M_18AF) <- c("contig","start","stop", "M_18AF")


M_190F <- read.table("/Users/jadebohbot/male_allo_190F_sorted_rg.bam_RPKMnorm_bamCoverage_10kb_fixedrowz.bw", header = T)
colnames(M_190F) <- c("contig","start","stop", "M_190F")

M_1873 <- read.table("/Users/jadebohbot/male_allo_1873_sorted_rg.bam_RPKMnorm_bamCoverage_10kb_fixedrowz.bw", header = T)
colnames(M_1873) <- c("contig","start","stop", "M_1873")


M_1876 <- read.table("/Users/jadebohbot/male_allo_1876_sorted_rg.bam_RPKMnorm_bamCoverage_10kb_fixedrowz.bw", header = T)
colnames(M_1876) <- c("contig","start","stop", "M_1876")



M_BJE3485 <- read.table("/Users/jadebohbot/male_BJE3485_sorted_rg.bam_RPKMnorm_bamCoverage_10kb_fixedrowz.bw", header = T)
colnames(M_BJE3485) <- c("contig","start","stop", "M_BJE3485")

M_BJE3486 <- read.table("/Users/jadebohbot/male_BJE3486_sorted_rg.bam_RPKMnorm_bamCoverage_10kb_fixedrowz.bw", header = T)
colnames(M_BJE3486) <- c("contig","start","stop", "M_BJE3486")



M_BJE3495 <- read.table("/Users/jadebohbot/male_BJE3495_sorted_rg.bam_RPKMnorm_bamCoverage_10kb_fixedrowz.bw", header = T)
colnames(M_BJE3495) <- c("contig","start","stop", "M_BJE3495")


M_BJE3496 <- read.table("/Users/jadebohbot/male_BJE3496_sorted_rg.bam_RPKMnorm_bamCoverage_10kb_fixedrowz.bw", header = T)
colnames(M_BJE3496) <- c("contig","start","stop", "M_BJE3496")

M_camM5 <- read.table("/Users/jadebohbot/male_camM5_sorted_rg.bam_RPKMnorm_bamCoverage_10kb_fixedrowz.bw", header = T)
colnames(M_camM5) <- c("contig","start","stop", "M_camM5")

M_Z23701 <- read.table("/Users/jadebohbot/male_Z23701_sorted_rg.bam_RPKMnorm_bamCoverage_10kb_fixedrowz.bw", header = T)
colnames(M_Z23701) <- c("contig","start","stop", "M_Z23701")

M_Z23739 <- read.table("/Users/jadebohbot/male_Z23739_sorted_rg.bam_RPKMnorm_bamCoverage_10kb_fixedrowz.bw", header = T)
colnames(M_Z23739) <- c("contig","start","stop", "M_Z23739")

M_Z23738 <- read.table("/Users/jadebohbot/male_Z23738_sorted_rg.bam_RPKMnorm_bamCoverage_10kb_fixedrowz.bw", header = T)
colnames(M_Z23738) <- c("contig","start","stop", "M_Z23738")



```



```{r}
library(dplyr)

# ladies first!
a <-  full_join(F_17A7, F_17AB, by = c('contig'='contig', 'start'='start', 'stop'='stop')) 

b <- full_join(a, F_17C2, by = c('contig'='contig', 'start'='start', 'stop'='stop'))

c <- full_join(b, F_17E2, by = c('contig'='contig', 'start'='start', 'stop'='stop')) 

d <- full_join(c, F_BJE3487, by = c('contig'='contig', 'start'='start', 'stop'='stop'))

e <- full_join(d, F_BJE3488, by = c('contig'='contig', 'start'='start', 'stop'='stop'))

f <- full_join(e, F_BJE3501, by = c('contig'='contig', 'start'='start', 'stop'='stop'))

g <- full_join(f, F_BJE3502, by = c('contig'='contig', 'start'='start', 'stop'='stop'))

h <- full_join(g, F_camF1, by = c('contig'='contig', 'start'='start', 'stop'='stop'))

i <- full_join(h, F_Z23702, by = c('contig'='contig', 'start'='start', 'stop'='stop'))

j <- full_join(i, F_Z23721, by = c('contig'='contig', 'start'='start', 'stop'='stop'))

k <- full_join(j, F_Z23726, by = c('contig'='contig', 'start'='start', 'stop'='stop'))

l <- full_join(k, F_Z23732, by = c('contig'='contig', 'start'='start', 'stop'='stop'))

m <- full_join(l, F_Z23733, by = c('contig'='contig', 'start'='start', 'stop'='stop'))

n <- full_join(m, F_Z23698, by = c('contig'='contig', 'start'='start', 'stop'='stop'))

all_girls <- n

# now the boys......

a <-  full_join(M_17A1, M_1873, by = c('contig'='contig', 'start'='start', 'stop'='stop')) 

b <- full_join(a, M_1876, by = c('contig'='contig', 'start'='start', 'stop'='stop'))

c <- full_join(b, M_18A1, by = c('contig'='contig', 'start'='start', 'stop'='stop')) 

d <- full_join(c, M_18AF, by = c('contig'='contig', 'start'='start', 'stop'='stop'))

e <- full_join(d, M_190F, by = c('contig'='contig', 'start'='start', 'stop'='stop'))

f <- full_join(e, M_BJE3485, by = c('contig'='contig', 'start'='start', 'stop'='stop'))

g <- full_join(f, M_BJE3486, by = c('contig'='contig', 'start'='start', 'stop'='stop'))

h <- full_join(g, M_BJE3495, by = c('contig'='contig', 'start'='start', 'stop'='stop'))

i <- full_join(h, M_BJE3496, by = c('contig'='contig', 'start'='start', 'stop'='stop'))

j <- full_join(i, M_camM5, by = c('contig'='contig', 'start'='start', 'stop'='stop'))

k <- full_join(j, M_Z23701, by = c('contig'='contig', 'start'='start', 'stop'='stop'))

l <- full_join(k, M_Z23739, by = c('contig'='contig', 'start'='start', 'stop'='stop'))

m <- full_join(l, M_Z23738, by = c('contig'='contig', 'start'='start', 'stop'='stop'))

all_boys <- m



all_frogs <- full_join(all_boys, all_girls, by = c('contig'='contig', 'start'='start', 'stop'='stop'))


```




```{r}
# gets female average, male average and M-F 
# then removes

all_frogs$female_av <- rowMeans(all_frogs[18:32], na.rm = TRUE)
  
all_frogs$male_av <- rowMeans(all_frogs[4:17], na.rm = TRUE)

all_frogs$M_F_dif <- all_frogs$male_av - all_frogs$female_av


# gets averages for male & female per position

all_frogs$female_av <- rowMeans(all_frogs[18:32], na.rm = TRUE)
  
all_frogs$male_av <- rowMeans(all_frogs[4:17], na.rm = TRUE)


```



```{r}
# creates df of only averaged information
frog_av <- all_frogs[,c(1:3,33:35)]


frog_av <- frog_av[complete.cases(frog_av),]

```


```{r}

# grabs significance percentiles (0.1% and 99.9% percentiles)

quantile(frog_av$M_F_dif, probs = c(0.001, 0.999))


```



```{r}
# full scale with very low depth position (XLIM ARE CORRECT, LOW FEM)
ggplot(frog_av, aes(x = M_F_dif)) +
  geom_histogram(bins = 1000) + 
  geom_vline(xintercept = -0.2151942, color = "hotpink") +
  geom_vline(xintercept = 0.2295217 , color = "dodgerblue") 
```

```{r}
# XLIM TO SEE MAIN DATA
ggplot(frog_av, aes(x = M_F_dif)) +
  geom_histogram(bins = 1000) + 
  geom_vline(xintercept = -0.2151942, color = "hotpink") +
  geom_vline(xintercept = 0.2295217 , color = "dodgerblue") +
  xlim(c(-0.25, 0.25))
```



```{r}
# finds male/female positions that surpass threshold


boy_highs <- subset(frog_av, M_F_dif > 0.2295217)

girl_lows <- subset(frog_av, M_F_dif < -0.2151942)

girl_boy_ext <- rbind(boy_highs, girl_lows)


head(girl_boy_ext)

ord_BG_ex <- arrange(girl_boy_ext, desc(M_F_dif))


```









# annotation ONLY for EXON graph:

```{r}
# imports annotation for EXONS ONLY
# reading in and rearranging dataset
ann <- read.table("/Users/jadebohbot/Downloads/Masters_Documents/Depth/CDS_switched_allo_ordered_contigs.bed", header = F, fill = TRUE)


# fix column datatype
ann$V1 <-as.factor(ann$V1)
ann$V2 <- as.numeric(ann$V2)
ann$V5 <- as.factor(ann$V5)

# rename columns
colnames(ann) <- c("contigs", "start", "stop", "laev", "direction")



```



```{r}
ann_done <- read.table("/Users/jadebohbot/exon_annotation_final.txt", header = T, fill = TRUE)
colnames(ann_done)
nrow(ann_done)
#nrow(ann_dir)

```



```{r}

ann_dir <- read.table("/Users/jadebohbot/ann_done_directional.txt", header = T, fill = TRUE)

NA_dir <- (which(is.na(ann_dir$dir)) == TRUE)

NA_d <- ann_dir[NA_dir,]

try <- is.na(ann_dir$dir)

try1 <- ann_dir[291:length(ann_dir),]

length(which(is.na(ann_dir$dir)))

try_NA <- ann_dir[which(is.na(ann_dir$dir)),]

nrow(try_NA)

head(try_NA)
```


```{r}
#write.table(direc2, file = "laev_exon_direction.txt", sep = "\t", row.names = FALSE, col.names = TRUE)
#write.table(ann_done, file = "alter_ann_done.txt", sep = "\t", row.names = FALSE, col.names = TRUE)


beeboo <- write.table(try_NA, file = "dir_NA.txt", sep = "\t", row.names = FALSE, col.names = TRUE)

head(try_NA)


small_NA <- try_NA[1:50,]




```







# switching start/stop
# THIS ONE WORKS
``` {r}

# switches bed start & stop

unique(ann_done$sstart < ann_done$send)


ann_done$direction <- rep("+", nrow(ann_done))
levels(ann_done$direction) <- c("+", "-")

neg_vec <- ann_done$sstart < ann_done$send
unique(neg_vec)

which_neg <- which(neg_vec == FALSE)

ann_done[which_neg,"direction"] <- "-"

# subset of only negative values
#neg_ann <- subset(ann_done, subset = neg_vec)

# woahhhhh does this work? IT WORKKKKKSSSSSSS
  stemp <- ann_done[which_neg,2]
  etemp <- ann_done[which_neg,3]
  # switch column values
  ann_done[which_neg,3] <- stemp
  ann_done[which_neg,2] <- etemp
  
  
  
  # ADD IN +/- BIT HERE
  
  unique(ann_done$sstart < ann_done$send)

  #write.table(ann_done, file = "ann_complete_plus_minus.txt", sep = "\t", row.names = FALSE, col.names = TRUE)

  
```




```{r}
# rename columns

# original exons weird:
ann <- read.table("/Users/jadebohbot/allo_CDS_depth_pos.txt", header = F, fill = TRUE)
colnames(ann) <- c("laev_pos", "contigs", "V1", "V2", "V3", "V4", "V5", "V6", "start", "stop", "V7", "V8")
```





```{r}
# numbers exons!
library(data.table)
library(tidyr)


# alternate exon colours for each exon
# label first exon use geom_text or geom_annotate, can use same colour for label as exon
# left justified so it starts at same point as exon
# theme_classic for plots
# 6146 visualize
# no gene expression of dhx32 in any tads
# tads are an early stage 50, stage 50 is long
# group by gene, colour by group

```










# check exon version: 
```{r}
options(scipen = 999)
tig="tig00006777"
library(data.table)
library(tidyr)
# write a function that writes a graph so can automatically do each graph in one line with contigs as args
# first arg is the tig name (in quotes)
tig_graph <- function(tig)
{

# GRABBING EXONS
  # subset exon positions from ONLY contig of interest
c1724_exons <- subset(ann, contigs == tig)

ordered_ex <- c1724_exons[order(c1724_exons$start),] # ONLY SUBSETS START ?????

# depth data EXON BIT
c1724_depth <- subset(all_frogs, contig == tig)

# used to be 9, 10 here not 2, 3
rexon <- ordered_ex[ , c(9, 10)] # HERE IS THE ISSUE I DO NOT UNDERSTAND
rexon[,3] <- rep(-0.75, nrow(rexon))
rexon[,4] <- rep(-0.70, nrow(rexon))
rexon <- as.data.frame(rexon)
colnames(rexon) <- c("x1", "x2", "y1", "y2")


# GENE BIT
tig_ann <- subset(ann_done, allo_tig == tig)
ann_ord <- arrange(tig_ann, desc(sstart))
# make it so only only has 1 label if exons are consecutive

gene <- ann_ord[ , c(5, 2, 3)]
gene[,4] <- rep(-0.75, nrow(gene))
gene[,5] <- rep(-0.70, nrow(gene))
gene <- as.data.frame(gene)
colnames(gene) <- c("gene", "x1", "x2", "y1", "y2")
ann_done

# ann_done cols: allo_tig, sstart, send, laev_full, H_gene_name, direction
# add directional bit to exons
d <- ann_done
colnames(d) <- c("contig", "start", "stop", "laev_full", "H_gene_name", "dir")
dd <- subset(d, contig == tig)

# switch here

negs <- which(dd$dir == "-")

  stemp <- dd[negs,2]
  etemp <- dd[negs,3]
  # switch column values
  dd[negs,3] <- stemp
  dd[negs,2] <- etemp



# alternate exon colours for each exon
# label first exon use geom_text or geom_annotate, can use same colour for label as exon
# left justified so it starts at same point as exon
# theme_classic for plots
# 6146 visualize
# no gene expression of dhx32 in any tads
# tads are an early stage 50, stage 50 is long
# group by gene, colour by group




# use min/max to collapse gene name for multiple exons
collapse <- gene[,c(1:3)]

coll <- data.frame(gene = c(unique(collapse$gene)), min = c(rep(NA, length(unique(collapse$gene)))), max = c(rep(NA, length(unique(collapse$gene)))), overlap = c(rep(NA, length(unique(collapse$gene)))))

for(x in 1:length(coll))
{
  # figure out how to subset gene name at x with all start and stop
  now_gene <- coll[x,1]
  wh <- which(collapse$gene == now_gene)
  now <- collapse[wh,]
  coll[x,"min"] <- min(now[,"x1"])
  coll[x,"max"] <- max(now[,"x2"])

  
}


# numbering exons
# numbers exons!

collapse <- collapse[order(collapse$x1), ]

collapse2 <- as.data.table(collapse)

collapse2[,id := seq_len(.N), by = gene]

ugh <- unite(collapse2, exNum, gene, id, sep = "-")
collapse2$exNum <- ugh$exNum

h <- which(collapse2$id == 1)

collapse2[h,"exOne"] <- collapse2[which(collapse2$id == 1), "exNum"]

# SUBSETTING ???
contig <- subset(ann, contigs == tig)

# depth data
contig_depth <- subset(all_frogs, contig == tig)

start1 <- as.vector(dd$start)
stop1 <- as.vector(dd$stop)

sq <- seq(from = 1, to = nrow(dd), by = 1)

# MAKING GRAPH
  # plot
ggplot() +
  geom_point(contig_depth, mapping = aes(x = contig_depth$start, y = contig_depth$M_F_dif)) +
  xlab("Position in Contig (bp)") +
  ylab("Av. M-F Depth") +
  ylim(-0.75, 0.75)  +
      geom_text(aes(label = c(collapse2$exOne), x = c(collapse2$x1), y = c(rep(-0.5, nrow(collapse2))), colour = factor(collapse2$gene)), check_overlap = FALSE, size = 1.75, angle = 90, show.legend = FALSE) +
  theme_classic() + 
  geom_hline(yintercept = -0.2151942 , color = "hotpink", linewidth = 0.2) +
  geom_hline(yintercept = 0.2295217 , color = "dodgerblue", linewidth = 0.2) +
  geom_segment(aes(x = 850000, y = -0.2151942, xend = 850001, yend = -0.7),
    arrow = arrow(length = unit(0.2, "cm"))) + 
  geom_segment(aes(x = 850000, y = 0.2395217, xend = 850001, yend = 0.7),
    arrow = arrow(length = unit(0.2, "cm"))) +
  geom_text(aes(label = "male-specific", x = 800000, y = 0.26), check_overlap = FALSE, size = 2, show.legend = FALSE) +
  geom_text(aes(label = "female-specific", x = 800000, y = -0.24), check_overlap = FALSE, size = 2, show.legend = FALSE) + 
  geom_segment(data = dd, aes(x = start1[sq], y = rep(-0.7, nrow(dd)), xend = stop1[sq], yend = rep(-0.7, nrow(dd)), colour = factor(dd$H_gene_name) 
    ), arrow = arrow(length = unit(0.2, "cm")), key_glyph = "rect") + scale_color_discrete() +
  theme(legend.text = element_text(size = 8), legend.title = element_text(size = 10), legend.key.size = unit(0.75, "line"), legend.box.background = element_rect(color="black", size=0.5, fill = "lightblue1")) +
   guides(colour=guide_legend(title="Gene Name")) +
   annotate("rect", fill = "blue", alpha = 0.1, xmin = 171973, xmax = 177096, ymin = -Inf, ymax = Inf) +
  scale_color_discrete(labels = c(expression(italic("Aldh18a1")), expression(italic("Bccip")), expression(italic("Dhx32*")), expression(italic("Inpp5a"))))




# start, stop, dir

# add theme_classic at the end and add settings bc geom_text overrides
# check exon alpha

#+theme_classic()
  

#scale_fill_manual('Highlight this',
                      #values = 'pink',  
                      #guide = guide_legend(override.aes = )) 

#+guides(colour = guide_legend(override.aes = list(size=0.2)))
  
  
  #geom_text(aes(label = c(collapse2$exNum), x = c(collapse2$x1), y = c(rep(-0.4, nrow(collapse2))), colour = factor(collapse2$gene)), check_overlap = FALSE, size = 1, angle = 90)

}
# ADD THEME CLASSIC


# start, stop, dir


```

```{r}
check_loc <- ann_done[,4:5]

beep <- as.data.frame(str_split_fixed(check_loc$laev_full, "_", 7))

boobo <- cbind(beep, check_loc$H_gene_name)

colnames(boobo) <- c("V1", "V2", "V3", "V4", "V5", "V6", "V7", "hum")

loc1 <- grep("LOC", beep$V3, value = T)



unnamed_hum <- grep("ENSG", boobo$hum, value = T)

length(unnamed_hum)

length(unique(loc1)) - length(unique(unnamed_hum))
```


# alternate exon colours for each exon
# label first exon use geom_text or geom_annotate, can use same colour for label as exon
# left justified so it starts at same point as exon
# theme_classic for plots
# 6146 visualize
# no gene expression of dhx32 in any tads
# tads are an early stage 50, stage 50 is long
# group by gene, colour by group






# what I'm pretty sure is happening is that dd (new dir joined) is longer than ann_done bc 
  # of row repetition which won't allow graph to run properly- have to rewrite, no time
# By default, merge() uses all variables that are common to the two datasets as keys to merge on
# If there are rows in one that do not match any rows in the other, then merge() will exclude those rows by default.
# perhaps doing a different kind of join is better? ask ben
  # left outer join, ann_done on left
# e.g. 3 ann done matches to 1 match fin_dir



# obj with ordered high/low male/female: ord_BG_ex

# +
#  geom_text(aes(label = c(dd$direction), x = c(dd$start), y = c(rep(-0.5, nrow(dd)))), check_overlap = FALSE, size = 1, angle = 90, show.legend = FALSE)

```{r}
library(ggrepel)

contig <- subset(ann, contigs == "tig00006777")
tig = "tig00006777"
# cutoffs: -0.2151942  0.2295217 

# tig 6777 

jpeg("./male_tig00006777_closeup_exons.jpg",w=7, h=3.0, units ="in", bg="transparent", res = 200)
tig_graph("tig00006777")
dev.off()

jpeg("./male_tig00006146_closeup_exons.jpg",w=7, h=3.0, units ="in", bg="transparent", res = 200)
tig_graph("tig00006146")
dev.off()


```

```{r}
# save tig_graph




for(x in 1:nrow(coll))
{
  
  now_gene <- coll[x,1]
  wh <- which(collapse$gene == now_gene)
  now <- collapse[wh,]
  
  # not working
  for(z in 1:(nrow(coll)))
  {
    # checks one gene at a time to make sure (if x is within left/right)
    btw <- between(x = c(coll[z,2], coll[z,3]), left = coll[x,2], right = coll[x,3])
    
    if(btw == c(TRUE, TRUE))
    {
      
      # need to split "left" and "right" into 2 separate entries
      
      
      
      coll[x,"overlap"] <- coll[z,"gene"]
      
      
    }
    
    
  }
    
  
}
```


```{r}

boy_girl_extreme <- ord_BG_ex

```



```{r}
# write a function that writes a graph so can automatically do each graph in one line with contigs as args
# first arg is the tig name (in quotes)


# THIS ONE IS ONLY FOR MALES

high_depth_graph <- function(tig)
{

# GRABBING EXONS
  # subset exon positions from ONLY contig of interest
c1724_exons <- subset(all_frogs, contigs == tig)

ordered_ex <- c1724_exons[order(c1724_exons$start),]

# depth data
c1724_depth <- subset(all_frogs, contig == "tig00001724")

rexon <- ordered_ex[ , c(2, 3)]
rexon[,3] <- rep(-6000, nrow(rexon))
rexon[,4] <- rep(-5700, nrow(rexon))
rexon <- as.data.frame(rexon)
colnames(rexon) <- c("x1", "x2", "y1", "y2")

# SUBSETTING ???
  
contig <- subset(ann, contigs == tig)

# depth data
contig_depth <- subset(all_frogs, contig == tig)

high_depth_start <- subset(boy_girl_extreme_bed, boy_high_low == tig, select = c(start))

high_depth_start <- high_depth_start[1,1]

high_depth_end <- subset(boy_girl_extreme_bed, select = c(end))

high_depth_end <- high_depth_end[1,1]

# HERE is where you limit the x-limits to be only high depth
# use extreme_boy_girl_bed
# MAKING GRAPH
  # plot
ggplot() +
  geom_point(contig_depth, mapping = aes(x = contig_depth$start, y = contig_depth$M_F_dif)) +
  xlab("Position in Contig (Mb)") +
  ylab("Av. M-F Depth") +
  xlim(c(high_depth_start, high_depth_end)) +
geom_rect(data = rexon, mapping = aes(xmin = x1, xmax = x2, ymin = y1, ymax = y2), fill = "darkgreen")

}


```




# NOT NEEDED



# REDOING exon graph function to include gene names
```{r}

# write a function that writes a graph so can automatically do each graph in one line with contigs as args
# first arg is the tig name (in quotes)
tig_graph <- function(tig)
{

# GRABBING EXONS
  # subset exon positions from ONLY contig of interest
c1724_exons <- subset(ann, contigs == tig)

ordered_ex <- c1724_exons[order(c1724_exons$start),]

# depth data EXON BIT
c1724_depth <- subset(all_frogs, contig == tig)

rexon <- ordered_ex[ , c(2, 3)]
rexon[,3] <- rep(-0.75, nrow(rexon))
rexon[,4] <- rep(-0.70, nrow(rexon))
rexon <- as.data.frame(rexon)
colnames(rexon) <- c("x1", "x2", "y1", "y2")







# GENE BIT
tig_ann <- subset(ann_done, allo_tig == tig)
ann_ord <- arrange(tig_ann, desc(sstart))
# make it so only only has 1 label if exons are consecutive

gene <- ann_ord[ , c(5, 2, 3)]
gene[,4] <- rep(-0.65, nrow(gene))
gene[,5] <- rep(-0.60, nrow(gene))
gene <- as.data.frame(gene)
colnames(gene) <- c("gene", "x1", "x2", "y1", "y2")

# use min/max to collapse gene name for multiple exons
collapse <- gene[,c(1:3)]

coll <- data.frame(gene = c(unique(collapse$gene)), min = c(rep(NA, length(unique(collapse$gene)))), max = c(rep(NA, length(unique(collapse$gene)))), overlap = c(rep(NA, length(unique(collapse$gene)))))

for(x in 1:length(coll))
{
  # figure out how to subset gene name at x with all start and stop
  now_gene <- coll[x,1]
  wh <- which(collapse$gene == now_gene)
  now <- collapse[wh,]
  coll[x,"min"] <- min(now[,"x1"])
  coll[x,"max"] <- max(now[,"x2"])

  
}



# SUBSETTING ???
contig <- subset(ann, contigs == tig)

# depth data
contig_depth <- subset(all_frogs, contig == tig)

# MAKING GRAPH
  # plot
ggplot() +
  geom_point(contig_depth, mapping = aes(x = contig_depth$start, y = contig_depth$M_F_dif)) +
  xlab("Position in Contig (Mb)") +
  ylab("Av. M-F Depth") +
  ylim(-0.75, 0.75) +
geom_rect(data = rexon, mapping = aes(xmin = x1, xmax = x2, ymin = y1, ymax = y2), fill = "darkgreen") + geom_rect(data = gene, mapping = aes(xmin = x1, xmax = x2, ymin = y1, ymax = y2), fill = "blue")

}

```




