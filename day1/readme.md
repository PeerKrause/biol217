# Protocol biol217 microbial Omics - Peer Krause 

 

## Biol217 Practise Session Day-1 

 

What we have learned so far? 

 

1. Basic Linux 

2. Bioinformatics basic understanding 

3. Linux commands 

 

- copy from one folder to another: 

 

Block of code: 

```sh 

cp source destination 

``` 

 

inline code: 

this is the command `cp` 

 

ssh -X sunam233@caucluster.rz.uni-kiel.de 

biol217_2024!! 

 

https://github.com/AammarTufail/Biol217-2024.git 

 

#### Task: How to add (upload) links and images  

 
 
 
 

## Protocol Day 2 

 

### Preperation 

 

Log into caucluster with Sunam233 account: 

 

``` 

ssh -X sunam233@caucluster.rz.uni-kiel.de 

``` 

go to "Work" directory  

 

```  

cd $WORK 

``` 

 

All needed packages and programs are allready installed into conda environment.  

To activate this environment use the following commands: 

 

``` 

module load gcc12-env/12.1.0 

module load miniconda3/4.12.0 

conda activate anvio-8 

``` 

 

To xxx we need a BATCH script 

Prepare script: 

 

create a text document 

 

``` 

touch anvio_slurm 

``` 

 

to open text document with xxx use: 

 

``` 

emacs anvio_slurm 

``` 

 

determine following parameters in the script: 

 

| Parameter | Explanation |
| --- | --- | 
| #SBATCH | Slurm batch script directive | 
| --partition=<name> or -p <name> | Slurm partition (~batch class) | 
| --job-name=<name> or -J <jobname> | Job name | 
| --output=<filename> or -o <filename> | Stdout file | 
| --error=<filename> or -e <filename> | Stderr file; if not specified, stderr is redirected to stdout file | 
| --nodes=<nnodes> or -N <nnodes> | Number of nodes | 
| --ntasks-per-node=<ntasks> | Number of tasks per node; number of MPI processes per node | 
| --cpus-per-task=<ncpus> or -c <ncpus> | Number of cores per task or process | 
| --mem=<size[units]> | Real memory required per node; default unit is megabytes (M); use G for gigabytes | 
| --time=<time> or -t <time> | Walltime in the format "hours:minutes:seconds" | 

 

A directory must also be defined: 

 

``` 

cd /work_beegfs//sunam233/Metagenomics/0_raw_reads 

fastqc 

``` 

 

to ??? do: 

 

``` 

for i in *fastq.gz; do fastqc $i -o ../1_fastqc/$i; done 

``` 

 

### Anvio_slurm BATCH script 

 

The script should then be structured as follows and contain the following information 

 

``` 

#!/bin/bash 

#SBATCH --nodes=1 

#SBATCH --cpus-per-task=4 

#SBATCH --mem=10G 

#SBATCH --time=5:00:00 

#SBATCH --job-name=fastqc 

#SBATCH --output=fastqc.out 

#SBATCH --error=fastqc.err 

#SBATCH --partiotional=base 

#SBATCH --reservation=biol217 

 

module load gcc12-env/12.1.0 

module load miniconda3/4.12.0 

conda activate anvio-8 

 

cd /work_beegfs//sunam233/Metagenomics/0_raw_reads 

fastqc 

 

for i in *fastq.gz; do echo fastqc $i -o ../1_fastqc/$i; done 

``` 

 

The   script can then be send to xxx (cluster?), which is done with the following command 

 

``` 

sbatch anvio_slurm 

``` 

 

The execution might take some time. To check if its still running use: 

 

``` 

squeue -u sunam233 

``` 

 

This shows the work (?) that is running on the cluster for the given account. 

 

If the script runs succesfully there should be new files with the provided Names in the script. (#SBATCH --xxx) 

 

To check for errors within the script, open the error file from the anvio_slurm file. (File might be in different direction, depending on given directory in script) 

 

``` 

emacs anvio_slurm.err 

``` 

 

If there are no errors proceed. 

 

### Quality control 

 

First to evaluate the quality of the sequenced data fastqc and fastp are used. 

 

FastQC provides an overview of basic quality control metrics like the phred quality score. This metric gives you an idea on how accurately the base reading was. 

 

With the following commands the files can be translated into .html files 

 

#### fastp 

 

fastp allows you to process the reads using different parameters. 

The following command executes a loop over the data. With the help of fastp.  

  

Since there are separate end readings (e.g. -i,-o), different inputs must be specified for the files R1 and R2. 

 

##### Define the following folders for the command (e.g. `-o` output folder) 

 

fastp -i ? -I ? -R ? -o ? -O ? -t 6 -q 20 

 

use the following explenation help:  

 

> `--html` creates an .html report file in html format\ 

>`-i` R1 input file \ 

>`-I` R2 input file name\ 

>`-R` report title, here ‘_report’ is added to each file\ 

>`-o` output_folder/R1.fastq.gz output file\ 

>`-O` output_folder/R2.fastq.gz output file\ 

>`-t` trim tail 1, default is 0, here 6 bases are trimmed\ 

>`-q` 20 reads with a phred score of <=20 are trimmed 

 

For the command the rawdata names are used: 

 

raw read data name: 

BGR_130305_mapped_R1.fastq.gz > -i 

BGR_130305_mapped_R2.fastq.gz > -I  

 

BGR_130527_mapped_R1.fastq.gz > -i 

BGR_130527_mapped_R2.fastq.gz > -I 

 

BGR_130708_mapped_R1.fastq.gz > -i 

BGR_130708_mapped_R2.fastq.gz > -I 

 

```  

fastp -i sample1_R1.fastq.gz -I sample1_R2.fastq.gz -R fastp_report -o sample1_R1_clean.fastq.gz -O sample1_R2_clean.fastq.gz -t 6 -q 20  

``` 

 

Each Sample needs its ownCode: 

 

Sample1 

``` 

fastp -i BGR_130305_mapped_R1.fastq.gz -I BGR_130305_mapped_R2.fastq.gz -R fastp_report -o ../2_fastp/BGR_130305_mapped_clean_R1.fastq.gz -O ../2_fastp/BGR_130305_mapped_clean_R2.fastq.gz -t 6 -q 20 

``` 

Sample2 

 

``` 

fastp -i BGR_130527_mapped_R1.fastq.gz -I BGR_130527_mapped_R2.fastq.gz -R fastp_report -o ../2_fastp/BGR_130527_mapped_clean_R1.fastq.gz -O ../2_fastp/BGR_130527_mapped_clean_R2.fastq.gz -t 6 -q 20 

``` 

 

Sample3 

``` 

fastp -i BGR_130708_mapped_R1.fastq.gz -I BGR_130708_mapped_R2.fastq.gz -R fastp_report -o ../2_fastp/BGR_130708_mapped_clean_R1.fastq.gz -O ../2_fastp/BGR_130708_mapped_clean_R2.fastq.gz -t 6 -q 20 

``` 

 

clean data moved to folder: 2_fast due to the given directory in the commands. 

 

There is also a way to design the command as a loop (not done in the course). The command is as follows: 

 

``` 

for i in `ls *_R1.fastq.gz`; 

do 

    second=`echo ${i} | sed 's/_R1/_R2/g'` 

    fastp -i ${i} -I ${second} -R "${i}"_report -o output_folder/"${i}" -O output_folder/"${second}" -t 6 -q 20 

 

done 

``` 

 

### Copy and open on computer 

 

To view the graphics  a graphics tool like Chrome or Firefox is needed. These are located on the desktop. Therefore the  

.html files are copied to the harddrive. 

 

``` 

scp sunam233@caucluster.rz.uni-kiel.de:/work_beegfs/sunam233/Metagenomics/1_fastqc/*.html . 

``` 

 

### Assembly 

#### Megahit 

 

The data processed with fastp can now be used for genome assembly. For this purpose megahit is used. An ultrafast, memory-efficient NGS assembler. 

 

This allows the metagenomes to be assembled and the samples to be optimized 

 

##### Define the following paths for the command (e.g. `-1` path to R1 file) 

 

```                                  

megahit -1 ? -1 ? -1 ? -2 ? -2 ? -2 ? --min-contig-len 1000 --presets meta-large -m 0.85 -o ? -t 12         

``` 

The full command: 

 

``` 

megahit -1 sample1_R1_clean.fastq.gz -1 sample2_R1_clean.fastq.gz -1 sample3_R1_clean.fastq.gz -2 sample1_R2_clean.fastq.gz -2 sample2_R2_clean.fastq.gz -2 sample3_R2_clean.fastq.gz --min-contig-len 1000 --presets meta-large -m 0.85 -o /PATH/TO/3_coassembly/ -t 12    

``` 

 

For the paths of the read data for the three probes the folder (?) names are used: 

 

BGR_130305_mapped_clean_R1.fastq.gz  

BGR_130305_mapped_clean_R2.fastq.gz  

 

BGR_130527_mapped_clean_R1.fastq.gz 

BGR_130527_mapped_clean_R2.fastq.gz 

 

BGR_130708_mapped_clean_R1.fastq.gz 

BGR_130708_mapped_clean_R2.fastq.gz 

 

The finished command is shown in the following: 

 

``` 

megahit -1 BGR_130305_mapped_clean_R1.fastq.gz  -1 BGR_130527_mapped_clean_R1.fastq.gz -1 BGR_130708_mapped_clean_R1.fastq.gz -2 BGR_130305_mapped_clean_R2.fastq.gz -2 BGR_130527_mapped_clean_R2.fastq.gz -2 BGR_130708_mapped_clean_R2.fastq.gz --min-contig-len 1000 --presets meta-large -m 0.85 -o ../3_coassembly/ -t 12  

``` 

 

The finnished Data are located in the given output within the command. The output is: 

 

``` 

-o .../3_coassembly/  

``` 

 

#### Megahit SBATCH script 

 

For clarity and so that the files of the previous scripts are not overwritten by BATCH, the SBATCH names must be adapted. 

 

``` 

#!/bin/bash 

#SBATCH --nodes=1 

#SBATCH --cpus-per-task=4 

#SBATCH --mem=10G 

#SBATCH --time=5:00:00 

#SBATCH --job-name=megahit 

#SBATCH --output=megahit.out 

#SBATCH --error=megahit.err 

#SBATCH --partiotional=base 

#SBATCH --reservation=biol217 

``` 

 

The previous commands that are no longer to be executed must be marked with a #. A new path is also specified. 

 

``` 

cd ../xxx 

``` 

Lastly the megahit command is put in the script: 

 

``` 

megahit -1 BGR_130305_mapped_clean_R1.fastq.gz  -1 BGR_130527_mapped_clean_R1.fastq.gz -1 BGR_130708_mapped_clean_R1.fastq.gz -2 BGR_130305_mapped_clean_R2.fastq.gz -2 BGR_130527_mapped_clean_R2.fastq.gz -2 BGR_130708_mapped_clean_R2.fastq.gz --min-contig-len 1000 --presets meta-large -m 0.85 -o ../3_coassembly/ -t 12  

``` 

the script can then be submitted with 

 

``` 

emacs anvio_slurm 

``` 

The processing will take some time. If there are no errors keep the megahit running. 

 