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

BGR_130708_mapped_cleap
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


Zussamenfassung day2:

 fastqc beinhalten die reads
 first line beinhaltet den identifiere, name
 then quality

 general cut off for the reads is 20

 assembling > assembled sequences with contigs
 
 html erstellt (qualiy check)

 assembled with megahit

 Binning
: looking wih contigs belong together


day two: shotgun sequencing, de novo assembly (with megahiot)
day 3: binning

mags, always have gaps


k=number of the nukleotides that are counted together (cutoff)


forward read and backwards read are both counted the number of nukleotides are counted
only palindromes are not counted


## Protocol Day 3

### Assembly visualization

The final contigs can be found in the direction /3_coassemmbly

go to the folder, if there are `final.contigs.fa` file the assembly worked.

Use `head` or `tail` to look at the start or end of the nukleotide contigs.

```
head final.contigs.fa
```

use the following command to see how many contigs there are:

```
grep -c ">" final.contigs.fa
```
`>` steht in den fa files vor jedem contig. Es wird also die Anzahl der > gezählt um die anzahl der contigs zu ermitteln

There are 55836 contigs

To visualize the graph Bandage is use. Therefore it must be installed 

First the fasta files need to be convertet.

```
megahit_toolkit contig2fastg 99 final.contigs.fa > final.contigs.fastg
```                  

The fasta file is now a FASTG file: k99.fastg, which can be loaded into Bandage.

### Quality Assessment of Assemblies

```
metaquast -t 6 -o /PATH/TO/3_metaquast -m 1000 final.contigs.fa
```

the output is put in the code, where the file is. also the file name for the assembled contigs is put in.

```
metaquast -t 6 -o .../3_metaquast -m 1000 final.contigs.fa
```

Add to the BATCH script: 

Change the headdings!

```
cd /work_beegfs/sunam233/Metagenomics/3_coassembly
```

```
metaquast -t 6 -o ../3_metaquast -m 1000 final.contigs.f
```

after running the BATCH script with `sbatch`

the xxx can be found in the output folder `3_metaquast`

If there is something in this folder and no error notes in the Anvio.slurm.err continue.

### Looking at the report

Next step is to copy the report.pdf from the 3_metaquast to the computer harddrive.

use the following command to copy:

```
scp sunam233@caucluster.rz.uni-kiel.de:/work_beegfs/sunam233/Metagenomics/3_metaquast/*.pdf .
```

Looking at our pdf file `report.pdf` we can answer the following questions:

What is your N50 value?  Why is this value relevant?
- 3014
- N50 is the length for which the collection of all contigs of that length or longer covers at least half an assembly

How many contigs are assembled?
- 55836, einsehbar mit head in den fa files

What is the total length of the contigs?
- 142642586


N50 ist die Länge, für die die Sammlung aller Contigs dieser Länge oder länger mindestens die Hälfte einer Assembly abdeckt

### Genome Binning reformating (First step for binning)

neuen ordner erstell in den Metagenomics path

```
mkdir 3_binning_out
```
Use following helps to create the command for the first binning procces:

```
anvi-script-reformat-fasta ? -o ? --min-len 1000 --simplify-names --report-file name_conversion.txt
```

```
anvi-script-reformat-fasta final.contigs.fa -o /PATH/TO/YOUR/contigs.anvio.fa --min-len 1000 --simplify-names --report-file name_conversion.txt
```

Final Code for genome binning can be put in BATCH script:


```
anvi-script-reformat-fasta final.contigs.fa -o ../3_binning_out/contigs.anvio.fa --min-len 1000 --simplify-names --report-file binning_conversion.txt
```

### Mapping

Then you need to map your raw reads onto your assembled contigs. Mapping will be done using bowtie2. Use the following command to index your mapping reference fasta file. Needed for the next steps and basically makes mapping faser.

The next command is to index the mapping reference fasta file:

run it three times cause f three raw samples

```
module load bowtie2
bowtie2-build contigs.anvio.fa contigs.anvio.fa.index
```

Now bowtie2 can be used for the actual mapping. The loop command, below have the single command for better understanding:

``` 
module load bowtie2
bowtie2 --very-fast -x contigs.anvio.fa.index -1 ? -2 ? -S ?
```

This command has do be done wiht the raw reads, therefore we use it three times for the three xxx raw read files:


```
module load bowtie2
bowtie2 --very-fast -x contigs.anvio.fa.index -1 ../2_fastp/BGR_130305_mapped_clean_R1.fastq.gz -2 ../BGR_130305_mapped_clean_R2.fastq.gz -S SAMPLE.sam
```

```
module load bowtie2
bowtie2 --very-fast -x contigs.anvio.fa.index -1 ../2_fastp/BGR_130527_mapped_clean_R1.fastq.gz -2 ../BGR_130527_mapped_clean_R2.fastq.gz -S SAMPLE.sam
```

```
module load bowtie2
bowtie2 --very-fast -x contigs.anvio.fa.index -1 ../2_fastp/BBGR_130708_mapped_clean_R1.fastq.gz -2 ../BGR_130708_mapped_clean_R2.fastq.gz -S SAMPLE.sam
```

sbatch script for mapping:


cd /work_beegfs/sunam233/Metagenomics/3_binning_out

module load bowtie2
bowtie2-build contigs.anvio.fa contigs.anvio.fa.index

bowtie2 --very-fast -x contigs.anvio.fa.index -1 ../2_fastp/BGR_130305_mapped_clean_R1.fastq.gz -2 ../2_fastp/BGR_130305_mapped_clean_R2.fastq.gz -S BGR_130305.sam

bowtie2 --very-fast -x contigs.anvio.fa.index -1 ../2_fastp/BGR_130527_mapped_clean_R1.fastq.gz -2 ../2_fastp/BGR_130527_mapped_clean_R2.fastq.gz -S BGR_130527.sam

bowtie2 --very-fast -x contigs.anvio.fa.index -1 ../2_fastp/BGR_130708_mapped_clean_R1.fastq.gz -2 ../2_fastp/BGR_130708_mapped_clean_R2.fastq.gz -S BGR_130708.sam



Following command is the actual loop command:

```
module load bowtie2
cd /PATH/TO/FASTP
for i in `ls *_R1.fastq.gz`;
do
    second=`echo ${i} | sed 's/_R1/_R2/g'`
bowtie2 --very-fast -x /PATH/TO/index -1 ${i} -2 ${second} -S /PATH/TO/4_mapping/"$i".sam 
done

```

#### samtools

The output will be a sequence mapping file (SAM) with the .sam extension and which we convert to binary alignment and map (BAM) file with the .bam extension using samtools with the following loop

to view the mapped files use:
fill in the File names

```
module load samtools (only neccesarry one time)
samtools view -bS sam_file.sam > bam_file.bam
```

Do it three times for the BGR Probes:

```
samtools view -bS BGR_130305.sam > BGR_130305.bam
```

```
samtools view -bS BGR_130527.sam > BGR_130527.bam
```

```
samtools view -bS BGR_130708.sam > BGR_130708.bam
```

Full SBATCH script for samtools:

```
cd /work_beegfs/sunam233/Metagenomics/3_binning_out

module load samtools

samtools view -bS BGR_130305.sam > BGR_130305.bam

samtools view -bS BGR_130527.sam > BGR_130527.bam

samtools view -bS BGR_130708.sam > BGR_130708.bam
```

### Contigs data preparation!

You need to convqqase is an anvi’o contigs-db database that contains key information associated with your sequences.

anvi-gen-contigs-database -f contigs.anvio.fa -o contigs.db -n 'biol217'

Output anpassen und directory sowie BATCH Titel ändern.

```
cd /work_beegfs/sunam233/Metagenomics/3_binning_out
```

```
anvi-gen-contigs-database -f contigs.anvio.fa -o ../5_anvio_profiles/contigs.db -n 'biol217'
```

skript anpassen!



Then you need to perform an HMM search on your contigs. "Basically, in anvi’o, Hidden Markov Models (or HMMs for short) are used to search for specific genes with known functions in a larger dataset"

```
anvi-run-hmms -c contigs.db
```
memory:  24 G 
--num-threads 4


Batch skript for anvirun:

cd /work_beegfs/sunam233/Metagenomics/5_anvio_profiles

anvi-run-hmms -c contigs.db --num-threads 4



### srun

Put following command in the Terminal and execute:

```
srun --reservation=biol217 --pty --mem=10G --nodes=1 --tasks-per-node=1 --cpus-per-task=1 --nodelist=node002 /bin/bash
```

dann arbeitsbereich wurde geändert auf node105 (normalersweise arbeiten allle auf cluster, aber es werden minicluster erstellt auf dem das data prep weiter läuft)

Anschließend folgende Module laden:

``` 
module load gcc12-env/12.1.0
module load miniconda3/4.12.0
conda activate anvio-8

anvi-display-contigs-stats contigs.db
 ```

In a +new Terminal log into the follwoilng local host:

```
ssh -L 8060:localhost:8080 sunam233@caucluster.rz.uni-kiel.de
```
After that log in with given number = n100

```
ssh -L 8080:localhost:8080 n100
```

open the following link on google chrome (or else)

```
http://127.0.0.1:8060 or

http://127.0.0.1:8080
```
Presented are the finnished xxx???

Safe as .pdf with `strg + p`


## Protocol Day 4

Anvi-profile wurde auch an Tag 3 scho ausgeführt, war aber fehlerhaft.

### Binning with ANVI´O

#### Anviprofile
From here on you will be using ANVI´O, an ANalysis and Visualization platform for microbial ´Omics.

Together with a very extensive program, anvi’o offers a lot of explanations on every command or data type it uses, so, whenever you need more information than provided here, look through the online tutorial Anvi'o User Tutorial for Metagenomic Workflow !

anvi’o does this by using samtools in the background, it merges two separate samtools commands (sorting and indexing = fo each .bam file you have, there also is a .bam.bai file in the same directory) into one.

The first step will be to sort and index your .bam files.
For this use the following command:

Change the parameters and create the finnished command:

```
for i in *.bam; do anvi-init-bam $i -o "$i".sorted.bam; done

anvi-profile -i ? -c ? --output-dir ?

anvi-profile -i YOUR_SORTED.bam -c contigs.db --output-dir OUTPUT_DIR
```

Do it three times for the BGR .bam files that are the 3_binning_out (mit Cd Path angeben). Each File needs a seperate folder, which are created manually by Anvio.


Hat nicht fuktioniert wegen folgendem Befehl:
```
for i in *.bam; do anvi-init-bam $i -o "$i".sorted.bam; done
```

Die Files wurden zurück in den `3_binning_out` folder getan. Wegen output (-0) "$i".


Viele Proboleme, finaler code ?: Problemlösung: Jeden command einzeln direkt im Terminal ausführen.

```
anvi-profile -i BGR_130305.bam.sorted.bam -c ../5_anvio_profiles/contigs.db --output-dir ../5_anvio_profiles/BGR_130305

anvi-profile -i BGR_130527.bam.sorted.bam -c ../5_anvio_profiles/contigs.db --output-dir ../5_anvio_profiles/BGR_130572

anvi-profile -i BGR_130708.bam.sorted.bam -c ../5_anvio_profiles/contigs.db --output-dir ../5_anvio_profiles/BGR_130708

```


#### Anvi Merge


anvi-merge /PATH/TO/SAMPLE1/? /PATH/TO/SAMPLE2/? /PATH/TO/SAMPLE3/? -o ? -c ? --enforce-hierarchical-clustering

Put in Input, Output, and c ?
Finnished command:

Use the SBATCH skript again:
```
anvi-merge ./5_anvio_profiles/BGR_130305/PROFILE.db ./5_anvio_profiles/BGR_130572/PROFILE.db ./5_anvio_profiles/BGR_130708/PROFILE.db -o ./6_merged_profiles -c ./5_anvio_profiles/contigs.db --enforce-hierarchical-clustering
```

after that the computing is done

### Binning

Two binners are used. Firts Metabat and then MaxBin2

#### Binning with Metabat2
Fill in the parameters:

```
anvi-cluster-contigs -p /PATH/TO/merged_profiles/PROFILE.db -c /PATH/TO/contigs.db -C METABAT --driver metabat2 --just-do-it --log-file log-metabat2

anvi-summarize -p /PATH/TO/merged_profiles/PROFILE.db -c /PATH/TO/contigs.db -o SUMMARY_METABAT -C METABAT ?
```

-p output folder from the last step/PROFILE.db file with the merged profiles
-c contig.db folder
-C name of the output collection that this step will create. Note that this will be stored within your merged profiles and not as an extra file
--driver here you can name the driver you want to use
--just-do-it you need to specify this flag as anvi-cluster-contigs is an experimental workflow of the anvi´o program and therefore still under development. This way the developers want to make sure you are aware of it.

put the following commands in the SBATCH srcipt and chage the Headlines:

```
anvi-cluster-contigs -p ./6_merged_profiles/PROFILE.db -c ./5_anvio_profiles/contigs.db -C METABAT --driver metabat2 --just-do-it --log-file log-metabat2

anvi-summarize -p ./6_merged_profiles/PROFILE.db -c ./5_anvio_profiles/contigs.db -o SUMMARY_METABAT -C METABAT

```

#### Binning with MaxBin2

For the seccond binning just do the same steps like before but with MaxBin2:

```
anvi-cluster-contigs -p /PATH/TO/merged_profiles/PROFILE.db -c /PATH/TO/contigs.db -C MAXBIN2 --driver maxbin2 --just-do-it --log-file log-maxbin2

anvi-summarize -p /PATH/TO/merged_profiles/PROFILE.db -c /PATH/TO/contigs.db -o SUMMARY_MAXBIN2 -C MAXBIN2
```

```
anvi-cluster-contigs -p ./6_merged_profiles/PROFILE.db -c ./5_anvio_profiles/contigs.db -C MAXBIN2 --driver maxbin2 --just-do-it --log-file log-maxbin2

anvi-summarize -p ./6_merged_profiles/PROFILE.db -c ./5_anvio_profiles/contigs.db -o SUMMARY_MAXBIN2 -C MAXBIN2
```

after finishing no more srun!


summerization:

raw reads, file format: fastq 
> quality control from these raw reads (quality control means: cleaning and )
outcome: 
clean reads, clean fastq
> assembled them, file format now fasta format fa
fa contigs
> quality control with fa contogs
>
contigs are biologically: DNA 
> mapping (major step): input was fa contigs + clean reads (these where mapped)
coverage: abundance of short reads, which are alligning at that area, genome signature
to reconstruckt bins we need 
xxx???
binning: competentness , contigs


### MAGs Quality Estimation

Estimate your genomes completeness and contamination levels.
You can assess the quality of your bins by using:

```
anvi-estimate-genome-completeness -c /PATH/TO/contigs.db -p /PATH/TO/merged_profiles/PROFILE.db -C METABAT
```
```
anvi-estimate-genome-completeness -c ./5_anvio_profiles/contigs.db -p ./6_merged_profiles/PROFILE.db -C METABAT
```

In the next part you will visualize and evaluate your results.
If you want to check what collections you generated you can use:

```
anvi-estimate-genome-completeness -p ./6_merged_profiles/PROFILE.db -c ./5_anvio_profiles/contigs.db --list-collections
```

fill in the lacking parameters:

```
anvi-estimate-genome-completeness -p ./6_merged_profiles/PROFILE.db -c ./5_anvio_profiles/contigs.db --list-collections
```

module load gcc12-env/12.1.0
module load miniconda3/4.12.0
conda activate anvio-8

anvi-interactive -p ./6_merged_profiles/PROFILE.db -c ./5_anvio_profiles/contigs.db -C METABAT

anvi-interactive -p ./6_merged_profiles/PROFILE.db -c ./5_anvio_profiles/contigs.db -C MAXBIN2

the Picture ? is opened manually in a Webbrowser

safe it as pdf and answer the following Questions:

Which binning strategy gives you the best quality for the
bins?

- Metabat: Gives  less blanks. The redunancy overall is much better compared to Maxbin2, there are ore bacteria with a completion above 90% and a redundancy less than 5%.  
  
How many Archaea bins do you get that are of High Quality? How many Bacteria bins do you get that are of High Quality?

Metabat: > Archea - 0 (only one Archaea with redundancy 5,26 and completion 97,3), > Bacteria - 10
Maxbin: > Archea - 0 (only one Archaea with redundancy 80,26 and completion 96,05) > Bacteria - 3

### Bin refinement

Do this in Terminal again:

First, you can use the following command to get a list of your collections; then use anvi-summarize:

```
anvi-summarize -p ./6_merged_profiles/PROFILE.db -c ./5_anvio_profiles/contigs.db --list-collections
```

Then use anvi-summarize as displayed below.

```
anvi-summarize -c ./5_anvio_profiles/contigs.db -p ./6_merged_profiles/PROFILE.db -C METABAT -o SUMMARY_METABAT2 --just-do-it
```


search the folder of the different bins with:

```
anvi-estimate-genome-completeness -c ./5_anvio_profiles/contigs.db -p ./6_merged_profiles/PROFILE.db -C METABAT
```

As each bin is stored in its own folder, use:


cd ./SUMMARY_METABAT2/bin_by_bin

mkdir ../../ARCHAEA_BIN_REFINEMENT

cp /METABAT__25/*.fa ../../ARCHAEA_BIN_REFINEMENT/



cp ../ARCHAEA_BIN_REFINEMENT/*.fa /PATH/TO/ARCHAEA_BIN_REFINEMENT/




bin for archea:

METABAT__25 | ARCHAEA
METABAT__40 | ARCHAEA
METABAT__15 | ARCHAEA

### Day5

#### Chimera detection in MAGs

Use GUNC to check run chimera detection. Chimeric genomes are genomes wrongly assembled out of two or more genomes coming from separate organisms.

to use GUNC , activate the following environment:

```
module load gcc12-env/12.1.0
module load miniconda3/4.12.0
conda activate gunc
```

Use the following loop to process all your files in one run:

```
cd ./Metagenomics/ARCHAEA_BIN_REFINEMENT

mkdir GUNC

for i in *.fa; do gunc run -i "$i" -r /work_beegfs/sunam233/Databases/gunc_db_progenomes2.1.dmnd --out_dir GUNC --threads 10 --detailed_output; done
```



gunc plot -d ./GUNC/diamond_output/METABAT__25-contigs.diamond.progenomes_2.1.out -g ./GUNC/genes_calls/gene_counts.json




Answer the following questions:

Do you get Archaea bins that are chimeric? hint: look at the CSS score (explained in the lecture) and the column PASS GUNC in the tables outputs per bin in your gunc_output folder.

In your own words (2 sentences max), explain what is a chimeric bin.



#Chimera detection sbatch script

module load gcc12-env/12.1.0
module load miniconda3/4.12.0
conda activate gunc

cd ./Metagenomics/ARCHAEA_BIN_REFINEMENT

mkdir GUNC

for i in *.fa; do gunc run -i "$i" -r /work_beegfs/sunam233/Databases/gunc_db_progenomes2.1.dmnd --out_dir GUNC --threads 10 --detailed_output; done

gunc plot -d ./GUNC/diamond_output/METABAT__25-contigs.diamond.progenomes_2.1.out -g ./GUNC/genes_calls/gene_counts.json

gunc plot -d ./GUNC/diamond_output/METABAT__40-contigs.diamond.progenomes_2.1.out -g ./GUNC/genes_calls/gene_counts.json

gunc plot -d ./GUNC/diamond_output/METABAT__15-contigs.diamond.progenomes_2.1.out -g ./GUNC/genes_calls/gene_counts.json



#### Manual bin refinement

As large metagenome assemblies can result in hundreds of bins, pre-select the better ones for manual refinement, e.g. > 70% completeness.

Before you start, make a copy/backup of your unrefined bins the ARCHAEA_BIN_REFINEMENT.

```
cp ./Metagenomics/ARCHAEA_BIN_REFINEMENT/*.fa
``` (done manually) 

First load the following Modules:

```
module load gcc12-env/12.1.0
module load miniconda3/4.12.0
conda activate anvio-8
```

Use anvi refine to work on your bins manually with the following command (fill in the parameters):

```
anvi-refine -c /PATH/TO/contigs.db -C METABAT -p /PATH/TO/merged_profiles/PROFILE.db --bin-id Bin_METABAT__##
```

Bin_METABAT__# = METABAT__25 | ARCHAEA, METABAT__40 | ARCHAEA, METABAT__15 | ARCHAEA


Do this for all three Archaea in the console: 

```anvi-refine -c ../5_anvio_profiles/contigs.db -C METABAT -p ../6_merged_profiles/PROFILE.db --bin-id METABAT__25

anvi-refine -c ../5_anvio_profiles/contigs.db -C METABAT -p ../6_merged_profiles/PROFILE.db --bin-id METABAT__40

anvi-refine -c ../5_anvio_profiles/contigs.db -C METABAT -p ../6_merged_profiles/PROFILE.db --bin-id METABAT__15
```





module load gcc12-env/12.1.0
module load miniconda3/4.12.0
conda activate anvio-8

anvi-refine -c ./5_anvio_profiles/contigs.db -C METABAT -p ./6_merged_profiles/PROFILE.db --bin-id METABAT__25

40 und 15 nicht für weitere studien aber man kann die taxonomy einsehen

25 zeigt gute qualität



#### Coverage visualization

how abundant are the archaea bins in the 3 samples? (relative abundance)

you can also use anvi-inspect -p -c, anvi-script-get-coverage-from-bam or, anvi-profile-blitz. Please look up the help page for each of those commands and construct the appropriate command line

commands:

-p ./6_merged_profiles/PROFILE.db
-c ./5_anvio_profiles/contigs.db

```
anvi-inspect -p ./6_merged_profiles/PROFILE.db -c ./5_anvio_profiles/contigs.db --split-name 
 
anvi-script-get-coverage-from-bam

anvi-profile-blitz
```

### Taxonomic assignment


You will now add taxonomic annotations to your MAG.

Fill in the missing Parameters:

```anvi-run-scg-taxonomy -c /PATH/TO/contigs.db -T 20 -P 2
```

Finnished comand:

```
anvi-run-scg-taxonomy -c ./5_anvio_profiles/contigs.db -T 20 -P 2
```

Now you can run anvi-estimate-scg-taxonomy, ‘This program makes quick taxonomy estimates for genomes, metagenomes, or bins stored in your contigs-db using single-copy core genes`.

To estimate abundance of Ribosomal RNAs within your dataset (coverage) use:

```
anvi-estimate-scg-taxonomy -c /PATH/TO/contigs.db -p /PATH/TO/profile.db --metagenome-mode --compute-scg-coverages --update-profile-db-with-taxonomy > temp.txt
```

```
anvi-estimate-scg-taxonomy -c ./5_anvio_profiles/contigs.db -p ./6_merged_profiles/PROFILE.db --compute-scg-coverages --update-profile-db-with-taxonomy > temp.txt
```

ONE final summary to get comprehensive info about your METABAT2 bins:

```
anvi-summarize -p /PATH/TO/merged_profiles/PROFILE.db -c /PATH/TO/contigs.db --metagenome-mode -o /PATH/TO/SUMMARY_METABAT2 -C METABAT2
```

```
anvi-summarize -p ./6_merged_profiles/PROFILE.db -c ./5_anvio_profiles/contigs.db --metagenome-mode -o ./SUMMARY_METABAT2 -C METABAT2
```

batch script for Taxonomi:

```
module load gcc12-env/12.1.0
module load miniconda3/4.12.0
conda activate anvio-8module load gcc12-env/12.1.0
module load miniconda3/4.12.0
conda activate anvio-8

cd /work_beegfs/sunam233/Metagenomics

anvi-run-scg-taxonomy -c ./5_anvio_profiles/contigs.db -T 20 -P 2

anvi-estimate-scg-taxonomy -c ./5_anvio_profiles/contigs.db -p ./6_merged_profiles/PROFILE.db --compute-scg-coverages --update-profile-db-with-taxonomy > temp.txt

anvi-summarize -p ./6_merged_profiles/PROFILE.db -c ./5_anvio_profiles/contigs.db --metagenome-mode -o ./SUMMARY_METABAT2 -C METABAT2

```




How good is the quality of genome?

Why did we use Hybrid assembler?
To combine short reads and long reads to get a better assembly

What is the difference between short and long reads?
Shortreads: produced by Illumina, about 15 bp long
Longreads:  

Did we use Single or Paired end reads? Why?
There is no need to use 

Write down about the classification of genome we have used here
d__Bacteria;p__Bacteroidota;c__Bacteroidia;o__Bacteroidales;f__Bacteroidaceae;g__Bacteroides;s__Bacteroides sp002491635



## Day 6 

#### Bacterial chromosme Data

Clostridium tetani E88
Clostridium argentinense strain 89G
Clostridium botulinum A str. ATCC 3502

Bacteroides caccae CL03T12C61
Bacteroides fragilis strain FDAARGOS_1225
Bacteroides humanifaecis strain KGMB07931

Vibrio ruber strain LMG 23124
Vibrio vulnificus strain FORC_037 
Vibrio ziniensis strain ZWAL4003

Pseudomonas canadensis strain GZH03

## Day 7

look in batch script

## Day 8 Transcriptomics

reademption create --project_path READemption_analysis --species metanosarcina="Methanosarcina mazei Gö1"

## Day 9
What is tnoar? file format?
answer in notes

5 gene that are downregulated: (mutantvs.wildtype)
1.MM_RS00150: Mt is up regulated by -2.06       yes
2.MM_RS04770: Mt is up regulated by by -1.2     yes 
3.MM_RS05370: Mt is up regulated by by -1.16    yes
4.MM_RS07870: Mt is up regulated by by -1.15
5.MM_RS05335: Mt is up regulated by by -1.09


5 genes that are upregulated:
1.MM_RS03790: Mt is downregulated by 1.9
2.MM_RS06645: Mt is downregulated by 1.71
3.MM_RS00155: Mt is downregulated by 1.58
4.MM_RS02310: Mt is downregulated by 1.3
5.MM_RS00765: Mt is downregulated by 1.23


#### Bilder einfügen

![igb](../Bilder/Screenshot-1.png)

#### Demo/together_ Find the ORF encoding csrA and some of its features
Answer the following Questions with looking in the Data
1. Its start codon (canonical ATG or alternative GTG, TTG):

TAC > ATG

2. Its stop codon (TAG, TGA, TAA):

ATT > TAA

3. Its length in amino acids:

61 (Length = 186 bp, but 1 triplet (codon) as stopcodon, 183/3=61)

4. Its SD (consensus AGGAGG at -7 to -4)

-7 = TCCTC > AGGAG

5. The name of the upstream gene:

alaS

6. D oyu think csrA is transated? Why?

Probably


### Zusammenfassung Day 10

#### Different Omics Methods
