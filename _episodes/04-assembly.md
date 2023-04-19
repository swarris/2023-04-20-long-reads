---
title: "Assembly of long reads"
teaching: 30
exercises: 120
questions:
- "How do you assembly NGS reads to create a de novo assembly?"
- "What type of information is given by the assembler?" 
objectives:
- "To create de novo assemblies based on PacBio and Nanopore data"
- "Learn to interpret assembly results and logs"
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
{{site.vm_prompt}}seqtk sample hifi_reads.fastq 0.1 > hifi_sub.reads.fastq
~~~
{: .bash}

## Long read assemblies

 
> ## Flye 
> We will start with Flye assemblies based on the PacBio and Nanopore data. When there are enough resources available, please create assemblies of both the full read set, as well as the subsampled read set (so four assemblies in total). 
> ~~~
> {{site.vm_prompt}}flye
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
> > {{site.vm_prompt}}flye --pacbio-hifi -f0 ~/data/reads/hifi_reads.fastq -t 6 -o flye_hifi
> > {{site.vm_prompt}}flye --pacbio-hifi -f0 ~/data/reads/hifi_sub.reads.fastq -t 6 -o flye_hifi_sub
> > {{site.vm_prompt}}flye --nano-hq -f0 ~/data/reads/ont_reads.fastq -t 6 -o flye_ont 
> > {{site.vm_prompt}}flye --nano-hq -f0 ~/data/reads/ont_sub.reads.fastq -t 6 -o flye_ont_sub
> > ~~~
> {: .solution}
> The output folder contains a log file which shows progress, but is also very handy for inspection when everything is done. 
> Which steps does flye take and what do they mean?
> ## Hifiasm
> Hifiasm is designed for assembling HiFi data. And because of the high quality of HiFi reads, it is very good in splitting up haplotypes. 
> For this workshop we also have high quality Nanopore data, so we will give this also a try.
> ~~~
> {{site.vm_prompt}}~/tools/hifiasm
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
> > {{site.vm_prompt}}mkdir ~/data/results/hifiasm_hifi
> > {{site.vm_prompt}}cd ~/data/results/hifiasm_hifi
> > {{site.vm_prompt}}~/tools/hifiasm -t 6 ~/data/reads/hifi_reads.fastq > hifiasm_hifi.log
> > {{site.vm_prompt}}mkdir ~/data/results/hifiasm_hifi_sub
> > {{site.vm_prompt}}cd ~/data/results/hifiasm_hifi_sub
> > {{site.vm_prompt}}~/tools/hifiasm -t 6 ~/data/reads/hifi_sub.reads.fastq > hifiasm_hifi_sub.log
> > {{site.vm_prompt}}mkdir ~/data/results/hifiasm_ont
> > {{site.vm_prompt}}cd ~/data/results/hifiasm_ont
> > {{site.vm_prompt}}~/tools/hifiasm -t 6 ~/data/reads/ont_reads.fastq > hifiasm_ont.log
> > {{site.vm_prompt}}mkdir ~/data/results/hifiasm_ont_sub
> > {{site.vm_prompt}}cd ~/data/results/hifiasm_ont_sub
> > {{site.vm_prompt}}~/tools/hifiasm -t 6 ~/data/reads/ont_sub.reads.fastq > hifiasm_ont_sub.log
> > ~~~
> > {: .bash}
> {: .solution}
> Have a look at the output files and the log file. Then try to answer these questions:
> 1. Can you identify the primary assembly file and two haplotypes?
> 2. The log file contains lines like this: [M::ha_hist_line]    98: ** 55. What are they?
> 3. Sketch a plot of a k-mer distribution of: a) low-coverage data set; b) high coverage bacterial genome and c) a high coverage read set of a diploid genome
> 4. What happened to the Nanopore-based assemblies?
{: .challenge}
> ## Combining PacBio and Nanopore
> This is an additional challenge: hifiasm supports the usage of nanopore data together with HiFi data through the --ul option. Give it a go! 
{: .challenge}
## Getting a fasta-file
Hifiasm generates a .gfa file, which can be converted into a fasta file using, for example, awk:
~~~
{{site.vm_prompt}}awk '/^S/{print ">"$2"\n"$3}' hifiasm.asm.bp.p_ctg.gfa | fold > hifiasm.asm.bp.p_ctg.fa
~~~
{: .bash}

## Basic statistics on the assemblies

> ## Get statistics
> Use the assembly-stats program from the previous session to get the statistics on the assemblies.
> > ## PacBio Flye assembly
> >~~~
> > {{site.vm_prompt}}assembly-stats ~/data/results/flye_hifi/assembly.fasta
> > stats for assembly.fasta
> > sum = 96610088, n = 3198, ave = 30209.53, largest = 1225347
> > N50 = 31643, n = 1140
> > N60 = 29490, n = 1457
> > N70 = 27165, n = 1797
> > N80 = 23901, n = 2174
> > N90 = 19790, n = 2616
> > N100 = 10115, n = 3198
> > N_count = 0
> > Gaps = 0
> >~~~
> >{: .bash}
> {: .solution}
> > ## Nanopore Flye assembly
> >~~~
> > {{site.vm_prompt}}assembly-stats ~/data/results/flye_ont/assembly.fasta 
> > stats for assembly.fasta
> > sum = 54294716, n = 367, ave = 147942.01, largest = 509128
> > N50 = 188004, n = 104
> > N60 = 168365, n = 135
> > N70 = 149096, n = 169
> > N80 = 122872, n = 209
> > N90 = 97252, n = 259
> > N100 = 667, n = 367
> > N_count = 0
> > Gaps = 0
> >~~~
> >{: .bash}
> {: .solution}
> > ## PacBio Hifiasm assembly
> >~~~
> > {{site.vm_prompt}} ~/data/results/hifiasm_hifi/hifiasm.asm.bp.p_ctg.fa
> > stats for hifiasm.asm.bp.p_ctg.fa
> > sum = 79670447, n = 2290, ave = 34790.59, largest = 1225363
> > N50 = 34615, n = 834
> > N60 = 32217, n = 1073
> > N70 = 30164, n = 1328
> > N80 = 27704, n = 1603
> > N90 = 24103, n = 1908
> > N100 = 14305, n = 2290
> > N_count = 0
> > Gaps = 0
> >~~~
> >{: .bash}
> {: .solution}
> Discuss the results and make a comparison of the assemblies. Do the results match your expectations? Which one do you prefer and why?
{: .challenge}

> ## Quast
> The generated assemblies appear to be much larger than expected. We will investigate this further. A key tool in comparing assembly statistics is the tool quast.py.
> Quast outputs many different statistics, like N50, maximum contig length, GC percentage, A50 plot, etc in pdf, text and html formats. Run quast on your assemblies and inspect the results.
> > ## Solution
> >~~~
> > {{site.vm_prompt}}cd ~
> > {{site.vm_prompt}}mkdir quast
> > {{site.vm_prompt}}cd quast
> > {{site.vm_prompt}}quast.py -o all_raw_assemblies -t 6 ~/data/results/hifiasm_hifi/hifiasm_hifi.p_ctg.fa ~/data/results/hifiasm_hifi_sub/hifiasm_hifi_sub.p_ctg.fa ~/data/results/hifiasm_hifi_ont/hifiasm_hifi_ont.p_ctg.fa ~/data/results/flye_hifi_sub/flye_hifi_sub.fasta  ~/data/results/flye_hifi/flye_hifi.fasta ~/data/results/flye_ont_sub/flye_ont_sub.fasta ~/data/results/flye_ont/flye_ont.fasta
> >~~~
> >{: .bash}
> {: .solution}
> Please discuss the following items:
> 1. Given the statistics, which assembly do you prefer?
> 2. What does the GC-plot show and why is this plot relevant?
> 3. Check out the icarus view. What would be a nice length to filter the contigs on to remove the noise and keep the ~1.5mb region?
{: .challenge}
> ## Filtering the assembly
> We can use seqtk to filter the assemblies based on sequence length N:
>~~~
> {{site.vm_prompt}}seqtk seq -L 100000 assembly.fasta > assembly_filtered.fasta
>~~~
>{: .bash}
> Use the length(s) you determined in the previous section to filter the assemblies and run quast on these files to see the effect of this filtering.
> > ## Solution
> > Use for the assemblies based on the full read set a length of 500000 and for the assemblies based on the subsampled read sets a length of 200000.
> > ~~~
> > {{site.vm_prompt}}quast.py -o all_filtered_assemblies -t 6 ~/data/results/hifiasm_hifi/hifiasm_hifi_p.fa ~/data/results/hifiasm_hifi_sub/hifiasm_hifi_sub_p.fa ~/data/results/hifiasm_hifi_ont/hifiasm_hifi_ont_p.fa ~/data/results/flye_hifi_sub/flye_hifi_sub_p.fasta  ~/data/results/flye_hifi/flye_hifi_p.fasta ~/data/results/flye_ont_sub/flye_ont_sub_p.fasta ~/data/results/flye_ont/flye_ont_p.fasta
> > ~~~
> >{: .bash}
> {: .solution}
> Are you satisfied with the results?
{: .challenge}
 
