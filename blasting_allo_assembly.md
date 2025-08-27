- make blast db
```{bash}
makeblastdb -in allo.fasta.contigs_nobubbles.fasta.gz -dbtype nucl -out allo.fasta.contigs_nobubbles.fasta.gz_blastable
```

- use consensus sequence as a query
- do same for laevis seq
- pull out diverged sequences with dc-megablast
```
blastn -query allo_notch4_region.fa -db allo.fasta.contigs_nobubbles.fasta.gz_blastable -task dc-megablast -outfmt 6 -out allo_notch4_to_pacbio6.out
```


- from when it just absolutely wouldn't work- with bubbles
```
blastn -query ../../froglady/2024_allo/2024_allo_primers/foxi2_correct_18612557_18613557.fa -db allo.fasta.contigs.fasta_blastable -outfmt 6 -out tempFile
```


- make bed file, manually check that start < stop
```
cut -f2,9,10 allo_notch4_to_pacbio6.out > allo_notch4_to_pacbio6.bed
```

- extract fasta seqs
```
bedtools getfasta -fi allo.fasta.contigs_nobubbles.fasta.gz -bed allo_notch4_to_pacbio6.bed -fo allo_notch4_to_pacbio6.fasta
```

- align fasta seqs
```
mafft --adjustdirectionaccurately allo_notch4_to_pacbio6.fasta > allo_notch4_to_pacbio6_align.fasta
```


