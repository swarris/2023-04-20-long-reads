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

## Long read assemblies

We will start with Flye assemblies based on the PacBio and Nanopore data.
~~~
flye
~~~
{: .bash}

Canu can use a configuration file ('specs file'). For our assemblies we will use these settings:
~~~
useGrid=False
corOutCoverage=30  # default is 40
corMinCoverage=4 # default is 4
minOverlapLength=1000 # default is 500
minReadLength=1000
ovlMerDistinct=0.99 # change from auto mode to 0.99 in order to decrease runtime. 
maxThreads=1
maxMemory=2.5G
merylMemory=2.5G
merylThreads=1
cormhapThreads=1
cormhapMemory=2.5G
corMemory=2.5G
corThreads=1
obtovlMemory=2.5G
obtovlThreads=1
utgovlMemory=2.5G
utgovlThreads=1
oeaMemory=2.5G
oeaThreads=1

~~~
{: .bash}
> ## Configuration file
> Added the configuration line to a text file. The text editor **nano** is install in the VM. Name this file **canu.spec**.
>
> You can change the '...Memory' and '.../Threads' to optimize Canu run time. With **useGrid=True** on SLURM/SGE Canu will automatically divide jobs on the cluster. 
{: .challenge}

> ## Run Canu on PacBio data
> You can now run Canu using the following command:
> ~~~
> ./tools/canu-1.7.1/Linux-amd64/bin/canu -s results/canu.spec -pacbio-raw ./data/raw_data/pacbio_reads.fasta -p canu_pacbio -d results/canu_pacbio genomeSize=1M
> ~~~
> {: .bash}
> Try to find out in the manual what each of the following settings mean and how they effect the assembly process:
>
> 1. corOutCoverage
> 2. corMinCoverage
> 3. minOverlapLength
> 4. minReadLength
> 5. ovlMerDistinct
{: .challenge}

> ## The Canu log file
> During the assembly process, Canu generates a report file. In this case:
> ~~~
> ./results/canu_pacbio/canu_pacbio.report
> ~~~
> {: .bash}
> While Canu is running, try to find out what steps Canu will take to assembly the reads and how it works.
> Have a look at this file while Canu is running. What do you see? Find the expected coverage after each step and try to explain what is happening.
> {: .solution}
{: .challenge}

> ## Run Canu on the Nanopore data
> Change the command in such a way that it will use the Nanopore data for assembly. Follow the process of Canu. What do you see? Find the expected coverage after each step and try to explain what is happening.
> 
> > ## Solution
> >~~~
> >./tools/canu-1.7.1/Linux-amd64/bin/canu -s results/canu.spec -nanopore-raw ./data/raw_data/nanopore_reads.fastq -p canu_nanopore -d results/canu_nanopore genomeSize=1M
> >~~~
> >{: .bash}
> {: .solution}
{: .challenge}

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
