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

