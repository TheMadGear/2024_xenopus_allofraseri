# fastQC before cutadapt

```
#!/bin/sh
#SBATCH --job-name=fastQC
#SBATCH --nodes=4
#SBATCH --time=32:00:00
#SBATCH --cpus-per-task=20
#SBATCH --mem=16gb
#SBATCH --array=0-19
#SBATCH --output=fastQC.%J.out
#SBATCH --error=fastQC.%J.err
##SBATCH --mail-user=bohbom1@mcmaster.ca
#SBATCH --mail-type=BEGIN
#SBATCH --mail-type=END
#SBATCH --mail-type=FAIL
#SBATCH --account=def-ben

# run with sbatch fastQC_script.sh /home/froglady/projects/rrg-ben/froglady/2024_allo/2024_allo_RNAseq/


# loading stuff
module load StdEnv/2023
module load fastqc/0.12.1

parallel -j $SLURM_CPUS_PER_TASK fastqc -t 1 -o /home/froglady/projects/rrg-ben/froglady/2024_allo/2024_allo_RNAseq {} ::: /home/froglady/projects/rrg-ben/froglady/2024_allo/2024_allo_RNAseq/*001.fastq.gz
```


# PYTHON ENVIRONMENT
```
# setting up VE in jade_scripts
# load python and stdenv first
BASE=software/cutadapt-3.1/
virtualenv $BASE/venv
$BASE/venv/bin/pip install dnaio==1.2.1 # it next auto uninstalls, and installs 1.2.3
$BASE/venv/bin/pip install cutadapt==5.1
mkdir $BASE/bin
cd $BASE/bin
ln -s ../venv/bin/cutadapt

# add directory to path
# the path:
/home/froglady/projects/rrg-ben/froglady/2024_allo/jade_scripts/software/cutadapt-3.1/bin
# supposedly below should work? getting unexpected token error
prepend_path("PATH", "/software/cutadapt-3.1/bin")
```






# cutadapt
```
#!/bin/sh
#SBATCH --job-name=ben_cutadapt
#SBATCH --nodes=4
#SBATCH --time=8:00:00
#SBATCH --mem=16gb
#SBATCH --output=ben_cutadapt.%J.out
#SBATCH --error=ben_cutadapt.%J.err
#SBATCH --account=rrg-ben
#SBATCH --mail-user=bohbom1@mcmaster.ca
#SBATCH --mail-type=END
#SBATCH --mail-type=FAIL


module load StdEnv/2023
module load python/3.13.2


v=1
#  Always use for-loop, prefix glob, check if exists file.

for file in $1/*R1_001.fastq.gz ; do         # Use ./* ... NEVER bare *
	    /home/froglady/projects/rrg-ben/froglady/2024_allo/jade_scripts/software/cutadapt-3.1/bin/cutadapt -b "AGATCGGAAGAGCACACGTCTGAACTCCAGTCA" -B "AGATCGGAAGAGCGTCGTGTAGGGAAAGAGTGT" -e 0.2 -q 15,10 -o ${file::-15}cut.twice.R1.fastq -p ${file::-15}cut.twice.R2.fastq ${file::-15}R1_001.fastq.gz ${file::-15}R2_001.fastq.gz
done 
```

## run with
```
sbatch ./ben_cutadapt.sh /home/froglady/projects/rrg-ben/froglady/2024_allo/2024_allo_RNAseq/
```



- OKAY transfered RNAseq files, cutadapt.sh and fastQC.sh to cedar because graham not working
```
scp froglady@graham.computecanada.ca:/home/froglady/projects/rrg-ben/froglady/2024_allo/2024_allo_RNAseq/*001.fastq.gz .
```

- running fastQC and cutadapt again because graham not working
```
# fastQC
sbatch ./fastQC.sh /home/froglady/projects/def-ben/froglady/RNAseq_transfer/*001.fastq.gz

# cutadapt
sbatch ./cutadapt.sh *L001_R1_001.fastq.gz *L001_R2_001.fastq.gz
```




# Trinity Assembly (Brian's Machine)
# run on info114
```
ssh jade@info.mcmaster.ca
ssh info114
# echos all paths clearly
echo -e ${PATH//:/\\n}
# add to path
export PATH="$PATH:/home/jade/rnaseq/"
# run with
./trinity_assembly.sh .
```
# the original path in case I break it
```
.:/home/jade/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin:/home/jade/.local/bin:/home/jade/bin
```

# runs script
```
./trinity_assembly.sh .
```

# Trinity script
```

```








### adaptor sequences:

 >NEBNext_3p
AGATCGGAAGAGCACACGTCTGAACTCCAGTCA
 >NEBNext_5p
AGATCGGAAGAGCGTCGTGTAGGGAAAGAGTGT

# next steps
- fastQC again

- assembly with trinity
