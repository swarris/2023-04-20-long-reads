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
- "./tools/minimap2-2.11_x64-linux/minimap2"
- "./tools/Tablet/tablet"
- "./tools/samtools-1.9/samtools"
- "Integrative Genomics Viewer"
---

## Evaluating the results using other data

During this session we will use genes and RNASeq data to validate the results of the assembly process and investigate the impact of assembly errors on the gene content.


> ## Mapping reads to the assemblies
> 
> Use minimap2 to map the three read sets to each of the assemblies. For example, map the PacBio reads against the PacBio assembly.
> 
> You can use the Illumina and PacBio subsampled data to speed things up:
> ~~~
> ./data/raw_data/illumina_R1.subsample.fastq
> ./data/raw_data/illumina_R2.subsample.fastq
> ./data/pacbio_reads.subsample.fasta
> ~~~
> {: .bash}
> > ## Solution
> > ~~~
> > ./tools/minimap2-2.11_x64-linux/minimap2 -a -x sr ./results/illumina_assembly_contig.fa ./data/raw_data/illumina_R1.subsample.fastq ./data/raw_data/illumina_R2.subsample.fastq > ./results/illumina_assembly.sam
> > ./tools/minimap2-2.11_x64-linux/minimap2 -x map-pb -a ./results/canu_pacbio/canu_pacbio.contigs.fasta ./data/pacbio_reads.subsample.fasta > results/pacbio_assembly.subsample.sam
> > ./tools/minimap2-2.11_x64-linux/minimap2 -a -x map-ont ./results/canu_nanopore/canu_nanopore.contigs.fasta ./data/raw_data/nanopore_reads.fastq > results/nanopore_assembly.sam
> > ~~~
> > {: .bash}
> {: .solution}
{: .challenge}

> ## Visualizing the results
> Use Tablet to view the alignments.
> ~~~
> ./tools/Tablet/tablet
> ~~~
> {: .bash}
> You might need to sort the SAM file: Tablet will report this if necessary. You can do this with samtools:
>~~~
>./tools/samtools-1.9/samtools view -b -o mapping.bam mapping.sam
>./tools/samtools-1.9/samtools sort -O SAM mapping.bam > sorted.sam
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
> Use minimap2 to map the three read sets to each of the other assemblies. For example, map the PacBio reads against the Illumina assembly.
> > ## Solution PacBio reads
> > ~~~
> > ./tools/minimap2-2.11_x64-linux/minimap2 -x map-pb -a ./results/illumina_assembly_contig.fa ./data/pacbio_reads.subsample.fasta > results/pacbio_illumina_assembly.subsample.sam
> > ./tools/minimap2-2.11_x64-linux/minimap2 -x map-pb -a ./results/canu_nanopore/canu_nanopore.contigs.fasta ./data/pacbio_reads.subsample.fasta > results/pacbio_nanopore_assembly.subsample.sam
> > ~~~
> > {: .bash}
> {: .solution}
> > ## Solution Nanopore reads
> > ~~~
> > ./tools/minimap2-2.11_x64-linux/minimap2 -a -x map-ont ./results/illumina_assembly_contig.fa ./data/raw_data/nanopore_reads.fastq > results/nanopore_illumina_assembly.sam
> > ./tools/minimap2-2.11_x64-linux/minimap2 -a -x map-ont ./results/canu_pacbio/canu_pacbio.contigs.fasta ./data/raw_data/nanopore_reads.fastq > results/nanopore_pacbio_assembly.sam
> > ~~~
> > {: .bash}
> {: .solution}
> > ## Solution Illumina reads
> > ~~~
> > ./tools/minimap2-2.11_x64-linux/minimap2 -a -x sr ./results/canu_pacbio/canu_pacbio.contigs.fasta ./data/raw_data/illumina_R1.subsample.fastq ./data/raw_data/illumina_R2.subsample.fastq > ./results/illumina_pacbio_assembly.sam
> > ./tools/minimap2-2.11_x64-linux/minimap2 -a -x sr ./results/canu_nanopore/canu_nanopore.contigs.fasta ./data/raw_data/illumina_R1.subsample.fastq ./data/raw_data/illumina_R2.subsample.fastq > ./results/illumina_nanopore_assembly.sam
> > ~~~
> > {: .bash}
> {: .solution}
> Visualize the results again in Tablet. 
> 
> 1. Do these mappings match you expectations?
> 2. Which mapping(s) is/are the most informative?
{: .challenge}

## Assembly base quality check using mRNA sequence

For this we use [four genes](https://www.dropbox.com/s/s0lopvphn9na49i/AT4G_genes.fasta?dl=0) we selected from this region of interest. Please download these [four mRNA](https://www.dropbox.com/s/s0lopvphn9na49i/AT4G_genes.fasta?dl=0) sequences and store them in the **data** folder.

> ## Mapping mRNA sequences
> **minimap2** can also be used to map mRNA sequences to a genomic sequence:
>~~~
>./tools/minimap2-2.11_x64-linux/minimap2 -t 4 -x splice -a
>~~~
>{: .bash}
> Map these mRNA sequences to each of the open the alignments in Tablet.
> > ## Solution
> >~~~
> >./tools/minimap2-2.11_x64-linux/minimap2 ./results/canu_pacbio/canu_pacbio.contigs.fasta  ./data/AT4G_genes.fasta -t 4 -x splice -a > ./results/AT4G_to_pacbio_assembly.sam
> >./tools/minimap2-2.11_x64-linux/minimap2 ./results/canu_nanopore/canu_nanopore.contigs.fasta  ./data/AT4G_genes.fasta -t 4 -x splice -a > ./results/AT4G_to_nanopore_assembly.sam
> >./tools/minimap2-2.11_x64-linux/minimap2 ./results/illumina_assembly_contig.fa ./data/AT4G_genes.fasta -t 4 -x splice -a > ./results/AT4G_to_illumina_assembly.sam
> >~~~
> >{: .bash}
> {:.solution}
{: .challenge}


## Integrative Genomics Viewer

The [Integrative Genomics Viewer (IGV)](http://software.broadinstitute.org/software/igv/home) is a more complex type of viewer for, amongst others, sequence alignment results. 
With this viewer it is possible to have the different alignment files in a single window, which is not possible with Tablet.
It is written in Java and hence [also available for Linux](http://data.broadinstitute.org/igv/projects/downloads/2.4/IGV_2.4.13.zip).
It is possible to start the IGV directly from command line using Java Web Start. For this an IcedTea package need to be installed (sudo password is genetwister):
~~~
#install icedtea
sudo apt install icedtea-netx
#run IGV
javaws  http://data.broadinstitute.org/igv/projects/2.4/igv24_lm.jnlp
~~~
{: .bash} 

To be able to use IGV you need to have an indexed, sorted BAM file:
~~~
./tools/samtools-1.9/samtools view -b mapping.sam > mapping.bam
./tools/samtools-1.9/samtools sort mapping.bam > mapping.sorted.bam
./tools/samtools-1.9/samtools index mapping.sorted.bam
~~~
{: .bash} 

> ## IGV
> Load each of the three assemblies in IGV and add the different mapping results (reads, mRNA) to the viewer.
> Have a close look at SNPs and other differences you might find.
{: .challenge}

