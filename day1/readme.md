
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

#### Task: How to add (upload) links and images 





## Protocol Day 2

Quality control

ssh -X sunam233@caucluster.rz.uni-kiel.de
biol217_2024!!

https://github.com/AammarTufail/Biol217-2024.git

### Anvio_slurm

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


### Kopieren und auf Rechner öffnen

```
scp sunam233@caucluster.rz.uni-kiel.de:/work_beegfs/sunam233/Metagenomics/1_fastqc/*.html .
```


### fastp

fastp -i ? -I ? -R ? -o ? -O ? -t 6 -q 20

``` 
fastp -i sample1_R1.fastq.gz -I sample1_R2.fastq.gz -R fastp_report -o sample1_R1_clean.fastq.gz -O sample1_R2_clean.fastq.gz -t 6 -q 20 
```


or in a loop:
```
for i in `ls *_R1.fastq.gz`;
do
    second=`echo ${i} | sed 's/_R1/_R2/g'`
    fastp -i ${i} -I ${second} -R "${i}"_report -o output_folder/"${i}" -O output_folder/"${second}" -t 6 -q 20

done
```


> `--html` creates an .html report file in html format\
>`-i` R1 input file \
>`-I` R2 input file name\
>`-R` report title, here ‘_report’ is added to each file\
>`-o` output_folder/R1.fastq.gz output file\
>`-O` output_folder/R2.fastq.gz output file\
>`-t` trim tail 1, default is 0, here 6 bases are trimmed\
>`-q` 20 reads with a phred score of <=20 are trimmed

raw read data name:
BGR_130305_mapped_R1.fastq.gz
BGR_130305_mapped_R2.fastq.gz

BGR_130527_mapped_R1.fastq.gz
BGR_130527_mapped_R2.fastq.gz

BGR_130708_mapped_R1.fastq.gz
BGR_130708_mapped_R2.fastq.gz

Finished Codes:

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

clean data moved to folder: 2_fast


### Assembly
#### Megahit

BGR_130305_mapped_clean_R1.fastq.gz 
BGR_130305_mapped_clean_R2.fastq.gz 

BGR_130527_mapped_clean_R1.fastq.gz
BGR_130527_mapped_clean_R2.fastq.gz

BGR_130708_mapped_clean_R1.fastq.gz
BGR_130708_mapped_clean_R2.fastq.gz

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

```
megahit -1 BGR_130305_mapped_clean_R1.fastq.gz  -1 BGR_130527_mapped_clean_R1.fastq.gz -1 BGR_130708_mapped_clean_R1.fastq.gz -2 BGR_130305_mapped_clean_R2.fastq.gz -2 BGR_130527_mapped_clean_R2.fastq.gz -2 BGR_130708_mapped_clean_R2.fastq.gz --min-contig-len 1000 --presets meta-large -m 0.85 -o ../3_coassembly/ -t 12 
```