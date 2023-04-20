---
title: "Validation of assemblies"
teaching: 30
exercises: 60
questions: 
- "What is the quality of my assembly on a nucleotide level?"
objectives:
- Learn how to map reads to the created assemblies and interpret the results
keypoints:
- There seem to be several consensus calling issues
- Nanopore assembly is of low quality
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
> ./data/reads/pacbio_subsamble.reads.fastq
> ~~~
> {: .bash}
>
> > ## ONT reads on Flye ONT assembly
> > ~~~
> > {{site.vm_prompt}}minimap2 -a -o ~/data/results/minimap2/flye_ont.sam -t 3 -x map-ont --secondary=no ~/data/results/flye_ont/flye_ont_p.fasta /home/bioinf/data/reads/ont_subsample.reads.fastq
> >  {{site.vm_prompt}}grep -v "[0-9]\{2,\}S" ~/data/results/flye_ont.sam > ~/data/results/flye_ont_filtered.sam
> > ~~~
> > {: .bash}
> {: .solution}
>
> > ## Pacbio reads on Hifiasm hifi assembly
> > ~~~
> > {{site.vm_prompt}}minimap2 -a -o  ~/data/results/minimap2/hifiasm_hifi.sam -t 3 -x map-hifi --secondary=no ../hifiasm_hifi/hifiasm_hifi_p.fa /home/bioinf/data/reads/hifi_subsample.reads.fastq 
> >  {{site.vm_prompt}}grep -v "[0-9]\{2,\}S" ~/data/results/minimap2/hifiasm_hifi.sam > ~/data/results/hifiasm_hifi_filtered.sam
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
>{{site.vm_prompt}}samtools view -b -o mapping.bam mapping.sam
>{{site.vm_prompt}}samtools sort -O SAM mapping.bam > sorted.sam
>~~~
>{: .bash}
> Open Tablet and load the reference plus a SAM file. For each of the three results files, visually check:
> 
> 1. The number of mismatches, insertions and deletions (Tablet supports different **Color schemes** to help with this).
> 2. Coverage across the region
> 
> Discuss the differences between the two results. What type of errors do you see? How many of them?
{: .challenge}

> ## Mapping ONT reads to the Flye ONT raw assembly
> As a bonus, you could use minimap2 to map the ont read set to Flye nano-raw assembly. Do you spot the difference?
>   
> > ## Solution
> > ~~~
> > {{site.vm_prompt}}minimap2 -a -o ~/data/results/minimap2/flye_ont_raw.sam -t 3 -x map-ont --secondary=no ~/data/results/flye_ont_raw/flye_ont_raw.fasta /home/bioinf/data/reads/ont_subsample.reads.fastq
> >  {{site.vm_prompt}}grep -v "[0-9]\{2,\}S" ~/data/results/minimap2/flye_ont_raw.sam > ~/data/results/minimap2/flye_ont_raw_filtered.sam
> > ~~~
> > {: .bash}
> {: .solution}
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


