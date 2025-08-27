
# from X. laevis data
- analysis run on merged files

```
sbatch ../../jade_scripts/samtools_depthpersite.sh list_of_bams.txt 
```

# where Chr7.bed was as follows:
Chr7L	8000000	20000000



- from X. allofraseri data that was already mapped etc. with final deduped bam files


# correctly runs script
```
sbatch ./bam_coverage.sh ../../../for_jade/bam_files_mapped_to_allo_genome/BJE3485_sorted_rg_dedup.bam .
```

```
sbatch ./bam_coverage.sh retry_BJE3485_sorted_rg_dedup.bam .

```

# script
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
import bamCoverage from deepTools 

srun --ntasks $SLURM_NNODES --tasks-per-node=1 bash << EOF
virtualenv --no-download $SLURM_TMPDIR/env
source $SLURM_TMPDIR/env/bin/activate
pip install --no-index --upgrade pip
pip install --no-index deepTools


bamCoverage -b ${1} --outFileFormat bedgraph --binSize 100000 --ignoreDuplicates --minMappingQuality 30 --normalizeUsing RPKM -o output_bamCoverage.bw

EOF

```
