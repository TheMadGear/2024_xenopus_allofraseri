# this mostly takes place in for_jade and /home/froglady/projects/rrg-ben/froglady/2024_allo/new_bam/annotate

#  XENLA_10.1_Xenbase.transcripts.fa file for XL transcripts matches CDS only fasta

## files:
## laevis: /home/froglady/projects/rrg-ben/for_jade/XL_CDS_only.fasta
## human: /home/froglady/projects/rrg-ben/for_jade/human_transcriptome/gencode.v42.transcripts.fa_blastable
## allo: /home/froglady/projects/rrg-ben/for_jade/Adam_allo_genome_assembly_with_bubbles/allo.fasta.contigs.fasta_blastable

## allo & laevis
```
blastn -query /home/froglady/projects/rrg-ben/for_jade/XL_CDS_only.fasta -db /home/froglady/projects/rrg-ben/for_jade/Adam_allo_genome_assembly_with_bubbles/allo.fasta.contigs.fasta_blastable -outfmt 6 -out allo_CDS_depth_pos.txt
# change to bed file
mv allo_CDS_depth_pos.txt allo_CDS_depth_pos.bed
```

# human & laevis
```
blastn -query /home/froglady/projects/rrg-ben/for_jade/XL_CDS_only.fasta -db /home/froglady/projects/rrg-ben/for_jade/human_transcriptome/gencode.v42.transcripts.fa_blastable -outfmt 6 -out human_laev_CDS_depth_pos.txt
# change to bed file
mv human_laev_CDS_depth_pos.txt human_laev_CDS_depth_pos.bed
```

# ok
```
# copy tx. files to MY folder

cp /home/froglady/projects/rrg-ben/for_jade/XENLA_10.1_Xenbase.transcripts/XENLA_10.1_Xenbase.transcripts* .

```

# run R script on compute canada- use first script to run second script:
```
#!/bin/sh
#SBATCH --job-name=rscript2_annotate
#SBATCH --nodes=1
#SBATCH --ntasks-per-node=1
#SBATCH --time=3-24:00:00
#SBATCH --mem=8gb
#SBATCH --output=rscript2.%J.out
#SBATCH --error=rscript2.%J.err
#SBATCH --account=rrg-ben

# run with sbatch ./run_annotate.sh

module load StdEnv/2023 r/4.5.0

R CMD BATCH /home/froglady/projects/rrg-ben/froglady/2024_allo/new_bam/annotate/re-annotate_2.R
```


```
library(ggplot2)
library(MLEcens)
library (ggplot2)
library(tidyverse)
library(reshape2)


# loads each blast file

L_H <- read.table("/home/froglady/projects/rrg-ben/froglady/2024_allo/new_bam/annotate/human_laev_CDS_depth_pos.bed", header = F, fill = TRUE)
A_L <- read.table("/home/froglady/projects/rrg-ben/froglady/2024_allo/new_bam/annotate/allo_CDS_depth_pos.bed", header = F, fill = TRUE)


length(unique(L_H$V1))

nrow(L_H)

######################################################
# test num unique human genes
now_split <- as.data.frame(str_split_fixed(L_H$V2, "\\|", 8))

length(unique(now_split$V6))
######################################################

laev_split <- str_split_fixed(L_H$V1, "_", 7)
LH_sp <- cbind(laev_split, L_H)
colnames(LH_sp) <- c("L1", "L2", "gene_laev", "L3", "L4", "L5", "L6")

l_split <- str_split_fixed(LH_sp$L6, ":", 2)

lh_new <- cbind(l_split, LH_sp)

colnames(lh_new) <- c("chrom", "pos")

lsplit <- str_split_fixed(lh_new$pos, "-", 2)

newL <- cbind(lsplit, lh_new)

colnames(newL) <- c("L_start", "L_stop", "chrom", "pos", "laev1", "laev2", "gene_name", "laev3", "laev4", "laev5", "laev6", "laev_full", "human", "pident", "length", "mismatch", "gapopen", "qstart", "qend", "sstart", "send", "evalue", "bitscore")

newL$L_start <- as.numeric(newL$L_start)
newL$L_stop <- as.numeric(newL$L_stop)

newL$ex_length <- newL$L_stop - newL$L_start




# loop through unique gene names


my_df <- data.frame(gene = c(), chrom = c(), L_start = c(), L_stop = c(), ex_length = c(), V1 = c(), V2 = c(), v4 = c(), v5 = c(), v6 = c(), v10 = c(), v11 = c(), v12 = c(), v13 = c(), v14 = c(), v15 = c(), v16 = c(), v17 = c(), v18 = c(), v19 = c(), v20 = c(), v21 = c())

unq_gene <- unique(newL$gene_name)

length(unq_gene)
# splits human info into columns
try_spl <- str_split_fixed(newL$human, "\\|", 8)
try_spl <- as.data.frame(try_spl)


hum_ann <- cbind(try_spl, newL)

# rename V1-V8
colnames(hum_ann) <- c("H_trancript", "H_gene", "H_ortho_g", "H_ortho_t", "tx_name", "H_gene_name", "unknown", "function", "L_start", "L_stop", "chrom", "pos", "laev1", "laev2", "gene_name", "laev3", "laev4", "laev5", "laev6", "laev_full", "human", "pident", "length", "mismatch", "gapopen", "qstart", "qend", "sstart", "send", "evalue", "bitscore")


# convert each hum & laev gene name to all lowercase
hum_ann$gene_name <- str_to_upper(hum_ann$gene_name)
hum_ann$H_gene_name <- str_to_upper(hum_ann$H_gene_name)


# makes column for L or S subgenome
L_S <- as.data.frame(str_split_fixed(hum_ann$gene_name, "\\.", 2))
hum_ann$gene_name <- L_S$V1
hum_ann <- cbind(L_S$V2, hum_ann)
names(hum_ann)[names(hum_ann) == "L_S$V2"] <- "LS_subgenome"


unq_gene <- unique(hum_ann$gene_name)
length(unq_gene)



unq <- unique(hum_ann$laev_full)


subbing <- hum_ann#[1:20000,]





final_laev <- data.frame(H_gene_name = c(), L_start = c(), L_stop = c(), L_chrom = c(), L_gene_name = c(), L_full = c(), L_S = c())

# PRACTISE LOOP
for(z in 1:length(unique(hum_ann$laev_full)))
{
  
  # subset each unique laevis exon 
  sub1 <- subset(hum_ann, laev_full == unq[z]) 
  
  # subsets hum/laev to get best pident & length
  max_pid <- subset(sub1, pident == max(pident))
  max_len <- subset(max_pid, length == max(length))  
  # which(max_len$laev_full == unq[z])
  n_gene <- nrow(max_len)
  
  # concats human gene names if multiple
  # MAYBE THIS PART FILTERS TOO MUCH
  hum_gene <- unique(sub1[n_gene,"H_gene_name"])
  squish <- paste(hum_gene, collapse = "_")
  squish1 <- rep(squish, n_gene)
  
  # dataframe to concat
  sub_df <- data.frame(H_gene_name = c(squish1), L_start = c(max_len[,"L_start"]), L_stop = c(max_len[,"L_stop"]), L_chrom = c(max_len[,"chrom"]), L_gene_name = c(max_len[,"gene_name"]), L_full = c(max_len[,"laev_full"]), L_S = c(max_len[,"LS_subgenome"]))
  
  final_laev <- rbind(final_laev, sub_df)
  
}

length(unique(hum_ann$H_gene_name))

length(unique(final_laev$H_gene_name))

#####################################################################



# ABOVE SEEMINGLY WORKS AND DOESN'T REMOVE UNIQUE GENES
####################################################################################



















# A_L PARSING HERE

# make sure laev_full matches


colnames(A_L) <- c("laev_full", "allo_tig", "pident", "length", "mismatch", "gapopen", "qstart", "qend", "sstart", "send", "evalue", "bitscore")

length(unique(A_L$laev_full))

length(unique(final_laev$L_full))

AL_now_laev <- unique(A_L$laev_full)
length(AL_now_laev)
LH_now_laev <- unique(final_laev$L_full)

#sub1 <- subset(final_laev, L_full == now_laev[2])
#sub2 <-subset(A_L, laev_full == AL_now_laev[2])

LHA_ann <- data.frame(allo_tig = c(), sstart = c(), send = c(), laev_full = c(), H_gene_name = c())
#colnames(LHA_ann) <- c("allo_tig", "sstart", "send", "AL_pident", "H_gene_name")

# loops through each unique laev_full info
#

#missed_AL <- AL_now_laev[x] %in% A_L
#missed_HL <-  subset(final_laev, L_full == AL_now_laev[1])


end_tab <- data.frame(allo_tig = c(), sstart = c(), send = c(), laev_full = c(), H_gene_name = c())
sub1 <- subset(final_laev, L_full == AL_now_laev[1])
sub2 <-subset(A_L, laev_full == AL_now_laev[1])

#subset(final_laev, L_full == "XBXL10_1g1_LOC108704873_XBmRNA2_XM_041586392.1_Chr1L:75531-75629")


#subset(final_laev, L_full == "XBXL10_1g299_LOC121400414_XBmRNA308_XM_041583453.1_Chr1L:7093986-7094252")


#unique(final_laev$H_gene_name)



for(x in 1:length(LH_now_laev))
{
  #rm(final_allo) # TRY USING THIS TO ERASE ???
  # grabs allo positions with laev code
  sub2 <-subset(A_L, laev_full == LH_now_laev[x])
  
  # grabs human genes with laev code
  sub1 <- subset(final_laev, L_full == LH_now_laev[x])
  
  # subsets allo to get max pident & length
  max_pid2 <- subset(sub2, pident == max(pident))
  max_len2 <- subset(max_pid2, length == max(length)) 
  
  n_hum <- nrow(sub1)
  n_allo <- nrow(max_len2)
  
  # check if unique 
  
  
  if(n_hum == 0)
  {
    # ignore, or make a row where it shows allo information, and laev is NA
    final_allo <- data.frame(allo_tig = c(max_len2[,"allo_tig"]), sstart = c(max_len2[,"sstart"]), send = c(max_len2[,"send"]), laev_full = c(max_len2[,"laev_full"]), H_gene_name = c(rep(NA, n_allo)))
    
    
    #final_allo <- data.frame(allo_tig = c(max_len2[,"allo_tig"]), sstart = c(max_len2[,"sstart"]), send = c(max_len2[,"send"]), laev_full = c(max_len2[,"laev_full"]), H_gene_name = c(rep(NA, n_hum)))
  }else if(n_hum != n_allo)
  {
    # 2 cases: 
    
    # allo > human: mark "unknown exon (LOC)", something in allo & laev, not found in humans
    
    if(length(unique(sub1$H_gene_name) == 1))
    {
      
        hum_val <- sub1[1,"H_gene_name"]
        
        # before running this, need to catch "dif num rows", add NA to non-matching exon
        final_allo <- data.frame(allo_tig = c(max_len2[,"allo_tig"]), sstart = c(max_len2[,"sstart"]), send = c(max_len2[,"send"]), laev_full = c(max_len2[,"laev_full"]), H_gene_name = c(rep(hum_val, n_allo)))
        
      
    } else
    {
      final_allo <- data.frame(allo_tig = c(max_len2[,"allo_tig"]), sstart = c(max_len2[,"sstart"]), send = c(max_len2[,"send"]), laev_full = c(max_len2[,"laev_full"]), H_gene_name = c(rep("NON-COLLAPSED", n_allo)))
      
    }
    
  }else
  {
    
    # before running this, need to catch "dif num rows", add NA to non-matching exon
    final_allo <- data.frame(allo_tig = c(max_len2[,"allo_tig"]), sstart = c(max_len2[,"sstart"]), send = c(max_len2[,"send"]), laev_full = c(max_len2[,"laev_full"]), H_gene_name = c(sub1[,"H_gene_name"]))
  }
  
  end_tab <- rbind(end_tab, final_allo)
  
}

write.table(end_tab, file = "exon_annotation_final.txt", sep = "\t", row.names = FALSE, col.names = TRUE)
```



