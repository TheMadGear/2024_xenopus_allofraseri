

### blast cuzd1.2 seq against allo assembly
```
blastn -query /home/froglady/projects/rrg-ben/for_jade/Adam_allo_genome_assembly_with_bubbles/cuzd1.2_seq.fa -db /home/froglady/projects/rrg-ben/for_jade/Adam_allo_genome_assembly_with_bubbles/allo.fasta.contigs.fasta_blastable -outfmt 6 -out allo_cuzd1.2_seqs.txt

```

### pull out seqs associated
```
cut -f2,7,8 allo_cuzd1.2_seqs.txt > allo_cuzd1.2_seqs.bed
```

### grab fastas
```
bedtools getfasta -fi /home/froglady/projects/rrg-ben/for_jade/Adam_allo_genome_assembly_with_bubbles/allo.fasta.contigs.fasta -bed allo_cuzd1.2_seqs.bed -fo allo_cuzd1.2.fasta
```









