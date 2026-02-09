# done in /home/froglady/projects/rrg-ben/froglady/2024_allo/new_bam/bam_cov directory

# run in background on nohup
```
nohup Rscript ./2025_M_v_F_depth_in_windowz_include_outlierz.R &
```

# now try using alliancecan & sbatch
```
# below code installs pkgs here:
# 	‘/tmp/RtmpcuzMaL/downloaded_packages’
# Installing package into ‘/home/froglady/R/x86_64-pc-linux-gnu-library/4.5’
# /home/froglady/R/x86_64-pc-linux-gnu-library/4.5/00LOCK-Rcpp/00new/Rcpp/libs


# tidyverse here
# /home/froglady/R/x86_64-pc-linux-gnu-library/4.5

# ggplot
# Would you like to create a personal library
# ‘/home/froglady/R/x86_64-pc-linux-gnu-library/4.5’

# reshape
# Installing package into ‘/home/froglady/R/x86_64-pc-linux-gnu-library/4.5’

R
install.packages("ggplot2", repos = "https://cloud.r-project.org/")
```

# exon db won't run
```
nohup Rscript ./R_exon_annotation.Rmd &
```





