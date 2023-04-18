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

We will start with Flye assemblies based on the PacBio and Nanopore data. When there are enough resources available, please create assemblies of both the full read set, as well as the subsampled read set (so four assemblies in total). 
~~~
flye
usage: flye (--pacbio-raw | --pacbio-corr | --pacbio-hifi | --nano-raw |
	     --nano-corr | --nano-hq ) file1 [file_2 ...]
	     --out-dir PATH

	     [--genome-size SIZE] [--threads int] [--iterations int]
	     [--meta] [--polish-target] [--min-overlap SIZE]
	     [--keep-haplotypes] [--debug] [--version] [--help] 
	     [--scaffold] [--resume] [--resume-from] [--stop-after] 
	     [--read-error float] [--extra-params] 
	     [--deterministic]
~~~
{: .bash}




## Basic statistics on the assemblies

> ## Get statistics
> Use the assembly-stats program from the previous session to get the statistics on the four assemblies.
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
