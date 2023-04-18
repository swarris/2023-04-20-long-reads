---
title: "Assembly of long reads"
teaching: 30
exercises: 120
questions:
- "How do you assembly NGS reads to create a de novo assembly?"
- "What type of information is given by the assembler?" 
objectives:
- "To create de novo assemblies based on PacBio and Nanopore data"
- "Learn to interpet assembly results and logs"
keypoints:
- "Each platform and each data sets requires hands-on work with the assembler"
apps:
- "flye"
- "hifiasm"
---

In this section we focus on creating assemblies using the Flye and hifiasm assemblers.

## Subsampled read sets
Creating a de novo assembly is one of the most compute intensive tasks in bioinformatics. Small genomes, like bacterial genomes, can be done on a laptop, but in most cases you need much more memory and CPU-power. That is the reason why for this workshop we focus on a ~1.5M region of a plant genome. However, assembly can still be a challenging task for your computer. Hence there are sub-sampled read sets available, containing 30% of the reads. You can make this set bigger of smaller by using:
~~~
seqtk sample hifi_reads.fastq 0.1 > hifi_sub.reads.fastq
~~~
{: .bash}

## Long read assemblies

 
> ## Flye 
> We will start with Flye assemblies based on the PacBio and Nanopore data. When there are enough resources available, please create assemblies of both the full read set, as well as the subsampled read set (so four assemblies in total). 
> ~~~
> flye
> usage: flye (--pacbio-raw | --pacbio-corr | --pacbio-hifi | --nano-raw |
>	     --nano-corr | --nano-hq ) file1 [file_2 ...]
>	     --out-dir PATH
>
>	     [--genome-size SIZE] [--threads int] [--iterations int]
>	     [--meta] [--polish-target] [--min-overlap SIZE]
>	     [--keep-haplotypes] [--debug] [--version] [--help] 
>	     [--scaffold] [--resume] [--resume-from] [--stop-after] 
>	     [--read-error float] [--extra-params] 
>	     [--deterministic]
> ~~~
> {: .bash}
> What are the correct settings for each of the assemblies? 
> > ## Solution
> > ~~~
> > flye --pacbio-hifi ~/data/reads/hifi_reads.fastq -t 6 -o flye_hifi
> > flye --pacbio-hifi ~/data/reads/hifi_sub.reads.fastq -t 6 -o flye_hifi_sub
> > flye --nano-hq ~/data/reads/ont_reads.fastq -t 6 -o flye_ont 
> > flye --nano-hq ~/data/reads/ont_sub.reads.fastq -t 6 -o flye_ont_sub
> > ~~~
> {: .solution}
> The output folder contains a log file which shows progress, but is also very handy for inspection when everything is done. 
> Which steps does flye take and what do they mean?
> ## Hifiasm
> Hifiasm is designed for assembling HiFi data. And because of the high quality of HiFi reads, it is very good in splitting up haplotypes. 
> For this workshop we also have high quality Nanopore data, so we give this also a try.
> ~~~
> ~/tools/hifiasm
> Usage: hifiasm [options] <in_1.fq> <in_2.fq> <...>
> Options:
>   Input/Output:
>     -o STR       prefix of output files [hifiasm.asm]
>     -t INT       number of threads [1]
>     -h           show help information
>     --version    show version number
>   Overlap/Error correction:
>     -k INT       k-mer length (must be <64) [51]
>     -w INT       minimizer window size [51]
>     -f INT       number of bits for bloom filter; 0 to disable [37]
>     -D FLOAT     drop k-mers occurring >FLOAT*coverage times [5.0]
>     -N INT       consider up to max(-D*coverage,-N) overlaps for each oriented read [100]
>     -r INT       round of correction [3]
>     -z INT       length of adapters that should be removed [0]
>     --max-kocc   INT
>                  employ k-mers occurring <INT times to rescue repetitive overlaps [2000]
>     --hg-size    INT(k, m or g)
>                  estimated haploid genome size used for inferring read coverage [auto]
>   Assembly:
>     -a INT       round of assembly cleaning [4]
>     -m INT       pop bubbles of <INT in size in contig graphs [10000000]
>     -p INT       pop bubbles of <INT in size in unitig graphs [0]
>     -n INT       remove tip unitigs composed of <=INT reads [3]
>     -x FLOAT     max overlap drop ratio [0.8]
>     -y FLOAT     min overlap drop ratio [0.2]
>     -i           ignore saved read correction and overlaps
>     -u           post-join step for contigs which may improve N50; 0 to disable; 1 to enable
>                  [1] and [1] in default for the UL+HiFi assembly and the HiFi assembly, respectively
>     --hom-cov    INT
>                  homozygous read coverage [auto]
>     --lowQ       INT
>                  output contig regions with >=INT% inconsistency in BED format; 0 to disable [70]
>     --b-cov      INT
>                  break contigs at positions with <INT-fold coverage; work with '--m-rate'; 0 to disable [0]
>     --h-cov      INT
>                  break contigs at positions with >INT-fold coverage; work with '--m-rate'; -1 to disable [-1]
>     --m-rate     FLOAT
>                  break contigs at positions with <=FLOAT*coverage exact overlaps;
>                  only work with '--b-cov' or '--h-cov'[0.75]
>     --primary    output a primary assembly and an alternate assembly
>  Trio-partition:
>     -1 FILE      hap1/paternal k-mer dump generated by "yak count" []
>     -2 FILE      hap2/maternal k-mer dump generated by "yak count" []
>     -3 FILE      list of hap1/paternal read names []
>     -4 FILE      list of hap2/maternal read names []
>     -c INT       lower bound of the binned k-mer's frequency [2]
>     -d INT       upper bound of the binned k-mer's frequency [5]
>     --t-occ      INT
>                  forcedly remove unitigs with >INT unexpected haplotype-specific reads;
>                  ignore graph topology; [60]
>   Purge-dups:
>     -l INT       purge level. 0: no purging; 1: light; 2/3: aggressive [0 for trio; 3 for unzip]
>     -s FLOAT     similarity threshold for duplicate haplotigs in read-level [0.75 for -l1/-l2, 0.55 for -l3]
>     -O INT       min number of overlapped reads for duplicate haplotigs [1]
>     --purge-max  INT
>                  coverage upper bound of Purge-dups [auto]
>     --n-hap      INT
>                  number of haplotypes [2]
>   Hi-C-partition:
>     --h1 FILEs   file names of Hi-C R1  [r1_1.fq,r1_2.fq,...]
>     --h2 FILEs   file names of Hi-C R2  [r2_1.fq,r2_2.fq,...]
>     --seed INT   RNG seed [11]
>     --s-base     FLOAT
>                  similarity threshold for homology detection in base-level;
>                  -1 to disable [0.5]; -s for read-level (see <Purge-dups>)
>     --n-weight   INT
>                  rounds of reweighting Hi-C links [3]
>     --n-perturb  INT
>                  rounds of perturbation [10000]
>     --f-perturb  FLOAT
>                  fraction to flip for perturbation [0.1]
>     --l-msjoin   INT
>                  detect misjoined unitigs of >=INT in size; 0 to disable [500000]
>   Ultra-Long-integration (beta):
>     --ul FILEs   file names of Ultra-Long reads [r1.fq,r2.fq,...]
>     --ul-rate    FLOAT
>                  error rate of Ultra-Long reads [0.2]
>     --ul-tip     INT
>                  remove tip unitigs composed of <=INT reads for the UL assembly [6]
>     --path-max   FLOAT
>                  max path drop ratio [0.6]; higher number may make the assembly cleaner
>                  but may lead to more misassemblies
>     --path-min   FLOAT
>                  min path drop ratio [0.2]; higher number may make the assembly cleaner
>                  but may lead to more misassemblies
> Example: ./hifiasm -o NA12878.asm -t 32 NA12878.fq.gz
> See `https://hifiasm.readthedocs.io/en/latest/' or `man ./hifiasm.1' for complete documentation.
> ~~~
> {: .bash}
> There are many options for hifiasm. Which are the most relevant for now?
> Run the assemblies. You might want to redirect output to a file for inspection later.
> > ## Solution
> > ~~~
> > mkdir ~/data/results/hifiasm_hifi
> > cd ~/data/results/hifiasm_hifi
> > ~/tools/hifiasm -t 6 ~/data/reads/hifi_reads.fastq > hifiasm_hifi.log
> > mkdir ~/data/results/hifiasm_hifi_sub
> > cd ~/data/results/hifiasm_hifi_sub
> > ~/tools/hifiasm -t 6 ~/data/reads/hifi_sub.reads.fastq > hifiasm_hifi_sub.log
> > mkdir ~/data/results/hifiasm_ont
> > cd ~/data/results/hifiasm_ont
> > ~/tools/hifiasm -t 6 ~/data/reads/ont_reads.fastq > hifiasm_ont.log
> > mkdir ~/data/results/hifiasm_ont_sub
> > cd ~/data/results/hifiasm_ont_sub
> > ~/tools/hifiasm -t 6 ~/data/reads/ont_sub.reads.fastq > hifiasm_ont_sub.log
> > ~~~
> > {: .bash}
> {: .solution}
> Have a look at the output files and the log file. Then try to answer these questions:
> 1. Can you identify the primary assembly file and two haplotypes?
> 2. The log file contains lines like this: [M::ha_hist_line]    98: ** 55. What are they?
> 3. Sketch a plot of a k-mer distribution of: a) low-coverage data set; b) high coverage bacterial genome and c) a high coverage read set of a diploid genome
> 4. What happened to the Nanopore-based assemblies?
{: .challenge}

## Basic statistics on the assemblies

> ## Get statistics
> Use the assembly-stats program from the previous session to get the statistics on the six assemblies.
> > ## PacBio Flye assembly
> >~~~
> >./tools/assembly-stats-master/build/assembly-stats ./results/canu_pacbio/canu_pacbio.contigs.fasta 
> >stats for ./results/canu_pacbio/canu_pacbio.contigs.fasta
> >sum = 1042738, n = 2, ave = 521369.00, largest = 919604
> >N50 = 919604, n = 1
> >N60 = 919604, n = 1
> >N70 = 919604, n = 1
> >N80 = 919604, n = 1
> >N90 = 123134, n = 2
> >N100 = 123134, n = 2
> >N_count = 0
> >Gaps = 0
> >~~~
> >{: .bash}
> {: .solution}
> > ## Nanopore Flye assembly
> >~~~
> >./tools/assembly-stats-master/build/assembly-stats ./results/canu_nanopore/canu_nanopore.contigs.fasta 
> >stats for ./results/canu_nanopore/canu_nanopore.contigs.fasta
> >sum = 1001134, n = 1, ave = 1001134.00, largest = 1001134
> >N50 = 1001134, n = 1
> >N60 = 1001134, n = 1
> >N70 = 1001134, n = 1
> >N80 = 1001134, n = 1
> >N90 = 1001134, n = 1
> >N100 = 1001134, n = 1
> >N_count = 0
> >Gaps = 0
> >~~~
> >{: .bash}
> > ## PacBio Hifiasm assembly
> >~~~
> >./tools/assembly-stats-master/build/assembly-stats ./results/canu_pacbio/canu_pacbio.contigs.fasta 
> >stats for ./results/canu_pacbio/canu_pacbio.contigs.fasta
> >sum = 1042738, n = 2, ave = 521369.00, largest = 919604
> >N50 = 919604, n = 1
> >N60 = 919604, n = 1
> >N70 = 919604, n = 1
> >N80 = 919604, n = 1
> >N90 = 123134, n = 2
> >N100 = 123134, n = 2
> >N_count = 0
> >Gaps = 0
> >~~~
> >{: .bash}
> {: .solution}
> > ## Nanopore Hifiasm assembly
> >~~~
> >./tools/assembly-stats-master/build/assembly-stats ./results/canu_nanopore/canu_nanopore.contigs.fasta 
> >stats for ./results/canu_nanopore/canu_nanopore.contigs.fasta
> >sum = 1001134, n = 1, ave = 1001134.00, largest = 1001134
> >N50 = 1001134, n = 1
> >N60 = 1001134, n = 1
> >N70 = 1001134, n = 1
> >N80 = 1001134, n = 1
> >N90 = 1001134, n = 1
> >N100 = 1001134, n = 1
> >N_count = 0
> >Gaps = 0
> >~~~
> >{: .bash}
> {: .solution}
> Discuss the results and make a comparison of the four assemblies. Do the results match your expectations? Which one do you prefer and why?
{: .challenge}
> ## Additional challenge
> Play around with some of the settings of Canu and check the effects on the process and the end results. Change for example the minimum read length or the error correction values.
{: .challenge} 
