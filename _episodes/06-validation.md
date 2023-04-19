---
title: "Validation of assemblies"
teaching: 30
exercises: 120
questions: 
- "What is the quality of my assembly on a nucleotide level?"
- "What impact does a SNP have on gene structure and function?"
objectives:
keypoints:
apps:
- "minimap2"
- "~/tools/Tablet/tablet"
- "samtools"
---

## Evaluating the results

During this session we will map the reads back to the assemblies to validate the results and to investigate the impact of assembly errors.

> ## Mapping reads to the assemblies
> 
> Use minimap2 to map the three read sets to each of the assemblies. For example, map the PacBio reads against the PacBio assembly.
> 
> You can use the PacBio subsampled data to speed things up:
> ~~~
> ./data/pacbio_reads.subsample.fasta
> ~~~
> {: .bash}
> > ## Solution
> > ~~~
> > ~~~
> > {: .bash}
> {: .solution}
{: .challenge}

> ## Visualizing the results
> Use Tablet to view the alignments.
> ~~~
> ~/tools/Tablet/tablet
> ~~~
> {: .bash}
> You might need to sort the SAM file: Tablet will report this if necessary. You can do this with samtools:
>~~~
>samtools view -b -o mapping.bam mapping.sam
>samtools sort -O SAM mapping.bam > sorted.sam
>~~~
>{: .bash}
> Open Tablet and load the reference plus a SAM file. For each of the three results files, visually check:
> 
> 1. The number of mismatches, insertions and deletions (Tablet supports different **Color schemes** to help with this).
> 2. Coverage across the region
> 
> Discuss the differences between the three results. What type of errors do you see? How many of them?
{: .challenge}

> ## Mapping reads to the other assemblies
> Use minimap2 to map the read sets to each of the other assemblies. 
> > ## Solution PacBio reads
> > ~~~
> > ~~~
> > {: .bash}
> {: .solution}
> > ## Solution Nanopore reads
> > ~~~
> > ~~~
> > {: .bash}
> {: .solution}
> Visualize the results again in Tablet. 
> 
> 1. Do these mappings match you expectations?
> 2. Which mapping(s) is/are the most informative?
{: .challenge}



## Integrative Genomics Viewer

The [Integrative Genomics Viewer (IGV)](http://software.broadinstitute.org/software/igv/home) is a more complex type of viewer for, amongst others, sequence alignment results. 
With this viewer it is possible to have the different alignment files in a single window, which is not possible with Tablet.
It is written in Java and hence [also available for Linux](http://data.broadinstitute.org/igv/projects/downloads/2.4/IGV_2.4.13.zip).
It is possible to start the IGV directly from command line using Java Web Start. For this an IcedTea package need to be installed (sudo password is bioinf):
~~~
#install icedtea
sudo apt install icedtea-netx
#run IGV
javaws  http://data.broadinstitute.org/igv/projects/2.4/igv24_lm.jnlp
~~~
{: .bash} 

To be able to use IGV you need to have an indexed, sorted BAM file:
~~~
samtools view -b mapping.sam > mapping.bam
samtools sort mapping.bam > mapping.sorted.bam
samtools index mapping.sorted.bam
~~~
{: .bash} 

> ## IGV
> Load each of the three assemblies in IGV and add the different mapping results (reads, mRNA) to the viewer.
> Have a close look at SNPs and other differences you might find.
{: .challenge}

## Whole genome analyzes

In this workshop we focused on creating the assemblies and providing a first glance of the quality of the assemblies. Statistics like N50, maximum contig length, mapping quality of reads, etc. are very important in this. For further analyzes of your genome, several more steps need to be included to verify the results. These include:

- Detecting and annotating repeat content
- Aligning RNASeq or IsoSeq
- Aligning known gene sequences / CDS from closely related species (if own species is not available)
- Run gene prediction software, like prokka for bacterial genomes
- BUSCO genes analysis


