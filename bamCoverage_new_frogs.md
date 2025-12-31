
# use loop to run so many files
```
for f in /home/froglady/projects/rrg-ben/for_jade/2025_allo_bamz/*bam; do
  sbatch /home/froglady/projects/rrg-ben/froglady/2024_allo/jade_scripts/bam_coverage.sh "$f"
done
```

# bamCoverage script
```
# run with sbatch /home/froglady/projects/rrg-ben/froglady/2024_allo/jade_scripts/bam_coverage.sh *bam

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
