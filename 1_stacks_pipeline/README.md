# 1_stacks_pipeline

Usage: python 1_stacks_pipeline.py [full path to directory containing all de-multiplexed trimmed fq files]

Example:

`python 1_stacks_pipeline.py /Volumes/Project1/3_Trimmed_Output/`

Example file naming scheme (must follow the'SAMPLE.trim.fq' to work!):
```
4_MB312.trim.fq
```

Will result in many intermediate files:
```
4_MB312.trim.snps.tsv
4_MB312.trim.tags.tsv
4_MB312.trim.alleles.tsv
4_MB312.trim.matches.tsv
```

Full pipeline:

Script will run ustacks across all SAMPLE.trim.fq files in directory, then cstacks with all samples,
then sstacks on a per sample basis. Finally, the population script is run taking all samples as one
population, outputs several folders with results from population script with varying -r values 
(0, 50, 60, 70, 80, 90, 100).

User decides whether to run a) full pipeline (above), b) ustacks portion only, c) cstacks sstacks and
populations portion only, or d) populations only. As long as you stick to the same directory, you can 
run ustacks first (decision b), then the remaining pipeline later (decision c), or re-run populations (d).

ustacks call:

	ustacks -t fastq -f SAMPLE.trim.fq -i {iterable} -o . -r -m 5 -M 2 -p {user decision}

Note: The above settings were selected for 50 bp reads, in which 2 bp mismatches are allowed
between stacks. On the trimmed reads (39 bp), this corresponds to a 5% divergence. If you
are using 100 bp or 150 bp reads, you should change the settings of the -M flag from 2 to
something more appropriate. For 100 bp (post-trimmed 89 bp) perhaps use 4 or 5, and for
150 bp (post-trimmed 139 bp), maybe use 7 or 8. You can change line 99 of the 1_stacks_pipeline.py
script to achieve this. Below is the original line:

```
call_ustacks = "ustacks -t fastq -f {0} -i {1} -o {2} -r -m {3} -M {4} -p {5}".format(filetype, identifier, '.', '5', '2', processors)
```

For 100 bp reads you can change this to:
```
call_ustacks = "ustacks -t fastq -f {0} -i {1} -o {2} -r -m {3} -M {4} -p {5}".format(filetype, identifier, '.', '5', '4', processors)
```

And for 150 bp reads you can change this to:
```
call_ustacks = "ustacks -t fastq -f {0} -i {1} -o {2} -r -m {3} -M {4} -p {5}".format(filetype, identifier, '.', '5', '7', processors)
```


 
cstacks call:

	cstacks -b 1 {-s all samples together} -p {user decision} -o .

sstacks call:

	sstacks -b 1 -c batch_1 -s SAMPLE.trim -p {user decision} -o .

populations call:

	populations -b 1 -P . -M {path to script-generated file} -r {0, 50, 60, 70, 80, 90, 100} -t {user decision} --min_maf 0.05

This script was written for and tested with Stacks version 1.35. Note you can edit the call for any of the above modules in the code below for a specific application, these settings are most appropriate for the data sets we have been processing. Familiarize yourself with the different thresholds and parameters to understand the predefined values and how they may affect your processed data.


Written for Python 2.7.3

External Dependencies: Stacks v1.35

ustacks, cstacks, sstacks, populations (called from command line)


**Citation Information:**

***Using the pipeline.***
The scripts involved with this pipeline were originally published as part of the following work:

+ *Portik, D.M., Leache, A.D., Rivera, D., Blackburn, D.C., Rodel, M.-O., Barej, M.F., Hirschfeld, M., Burger, M., and M.K. Fujita. 2017. Evaluating mechanisms of diversification in a Guineo-Congolian forest frog using demographic model selection. Molecular Ecology, 26: 5245-5263. https://doi.org/10.1111/mec.14266*

If you use or modify this script for your own purposes, please cite this publication.


**Contact:**

Daniel Portik, PhD

Postdoctoral Researcher

University of Arizona

daniel.portik@gmail.com
