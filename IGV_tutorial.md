# Viewing Illumina Reads in IGV #

## Data Files ##

```
cd ../

wget https://bis180ldata.s3.amazonaws.com/downloads/Illumina_Assignment/tophat_out-IMB211_A03_INTERNODE.tar.gz
tar -xvzf tophat_out-IMB211_A03_INTERNODE.tar.gz

wget https://bis180ldata.s3.amazonaws.com/downloads/Illumina_Assignment/tophat_out-R500_A03_INTERNODE.tar.gz
tar -xvzf tophat_out-R500_A03_INTERNODE.tar.gz
```
## Examine tophat Output ##

```
cd tophat_out-IMB211_A03_INTERNODE/ 

ls
```
output:

  accepted_hits.bam – A bam file for reads that were successfully mapped (this is called accepted_hits_A03.bam in the tophat output that I created for you since I only retained chromosome A03)
    
  unmapped.bam A bam file for reads that were not able to be mapped missing from the downloaded directory because I deleted it to save space
    
  deletions.bed and insertions.bed bed files giving insertions and deletions
    
  junctions.bed A bed file giving introns
    
  align_summary.txt Summarizes the mapping
  
### Exercise 7 ###

Take a look at the `align_summary.txt` file.
You only need to answer for the IMB211 alignment.

a. What percentage of reads mapped to the reference?

81.1%

b. Think about the process of read mapping and that the reference genome is from a different cultivar (B3) than the cultivar we sequenced (IMB211). Give 2 reasons why reads might not map to the reference.

Being that they are two different cultivars, their sequences may not be exactly the same and there won't be a perfect match for every sequence.

## Look at a bam File ##

Bam files contain the information about where each read maps. They are in a binary, compressed format so we can not use less on its own. Thankfully there is a collection of programs called samtools that allow us to view and manipulate these files.

Let’s take a look at accepted_hits_A03.bam. For this we use the samtools view command:

```
samtools view -h accepted_hits_A03.bam | less
```
|Field | Value |
|------|---------|
|01|Read Name (just like in the fastq)|
|02|Code providing info about the alighment (bitwise FLAG)|
|03|Template Name (Chromosome in this case)|
|04|Position on the template where the read starts|
|05|Phred based mapping quality for the read|
|06|CIGAR string providing information about the mapping|
|07-09|These give information about the “mate pair” if paired end sequencing was done|
|10|Sequence|
|11|Phred+33 Quality of Sequence|
|Additional Files|Varies; see SAM page for more info|

samtools has many additional functions. These include:

samtools merge – merge BAM files

samtools sort – sort BAM files; required by many downstream programs

samtools index – create an index for quick access; required by many downstream programs

samtools idxstats – summarize reads mapping to each reference sequence

samtools mpileup – count the number of matches and mismatches at each position

samtools addreplacerg – add/replace read group identifiers in a BAM file

And more

## Look at a bam File with IGV ##

While samtools view is nice, it would be nicer to actually see our reads in context. We can do this with IGV, the Integrative Genome Viewer

To use IGV, first create an index of the bam file:

```
samtools index accepted_hits_A03.bam 

igv
```
### Prepare genome reference files for IGV to use ###

By default IGV starts with the human genome. It has a number of built-in genomes, but does not include B. rapa. We must upload it ourselves.

In IGV, click on Genomes > Load Genome from File

Select BrapaV1.5_chrom_only.fa located in input/Brapa_reference of your Assignment 8 repository

In IGV, click on File > Load from File

Select Brapa_gene_v1.5.gff located in input/Brapa_reference of your Assignment 8 repository

### Load some tracks ###

Now to load our mapped reads:

Click on File > Load From File ; then select the accepted_hits_A03.bam file from both IMB211 and R500

**Had trouble finding index?**

### Take a Look ###

Click on the “ALL” pull-down menu and select chromosome A03. Then zoom in until you can see the reads. If are having trouble with your cursor and the pulldown menu, try resizing your VNC window while IGV is open

  Grey vertical bars are a histogram of coverage
  
  Grey horizontal bars represent reads.
        Thin lines connect read segments that were split across introns.
        Colored vertical lines show places where a read differs from the reference sequence.
        
  In the lower panel, the blue blocks and lines show the reference annotation.
   Blocks are exons and lines are introns.
  he orange lines show junctions inferred by Tophat from our reads

### Exercise 8 ###

Can you distinguish likely SNPs from sequencing/alignment errors? How?

My best guess at this is that SNPs will likely be just one or two base changes (not entire sections of bad sequence), or, the location matters too. A single base change in the middle of the sequence is more likely to be a SNP than large areas of changes at the beginning and end of sequence (muddy sequence areas).

### Exercise 9 ###

View each gene listed below in IGV. (You can type the gene ID into the IGV search bar). For each gene, does the computationaly predicted annotation (blue bars) appear to be correct or incorrect given our RNA-seq data? If incorrect, describe what is wrong.

a Bra001706 Good
b Bra001700 Good
c Bra001702 This one seems to be missing the tail end of the sequence.
d Bra012917 There is sequence missing in the middle as well as extra sequence that doesn't correspond to the reference sequence.

### Exercise 10 ###

Comment on the trust-worthiness of computational annotation.

I think computational genome annotation is relatively trustworthy, given it's used correctly.

