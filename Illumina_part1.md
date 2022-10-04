# Illumina Tutorial Part 1: Working with Sequence Data #

This is the first of six labs working with the same RNA sequencing data from *Brassica rapa*.

**Outline for this lab:**
1. check FASTQ quality
2. trim reads to keep high-quality reads
3. split reads based on barcodes
4. map to reference genome

## Set Up ##

**multiqc** is a program for aggregrating the reports of quality check programs such as fastqc, which we will use to assess our data quality

**trimmomatic** is a program that filters reads based on quality scores and other criteria

Install from Linux command line:

```
python3 -m pip install multiqc # In most cases you can just do pip3 install multiqc
sudo ln -s ~/.local/bin/multiqc /usr/local/bin/multiqc

```
```
cd ~/Downloads
wget http://www.usadellab.org/cms/uploads/supplementary/Trimmomatic/Trimmomatic-0.39.zip
unzip Trimmomatic-0.39.zip

sudo mv Trimmomatic-0.39 /usr/local/bin
```
The trimmomatic program itself is written in java. We use a bash alias to make it easier to start. We will place the alias in the file ~/.bashrc; this is a bash script that is run every time bash starts.

Use nano or atom to open your ~/.bashrc file. (Note that the file is hidden because it starts with a .. To see it at the command line use ls -a). If the file appears empty when you opened it you made a mistake.

```
cd
ls -a
nano .bashrc
#or atom .bashrc
```
After the file is open in atom, paste following lines to end of file and save it:

```
# alias to run trimmomatic
alias trimmomatic="java -jar /usr/local/bin/Trimmomatic-0.39/trimmomatic-0.39.jar"
```
Finally source your .bashrc file to set the alias for this session. You won’t need to do this again.

```
source .bashrc
```
**auto_barcode** is a program that splits fastq reads based on their barcodes. It is written in Perl and we need to intall a few Perl modules before we install auto_barcode:

```
cpan install CPAN
```
Respond to the two questions that come up with "yes" and "sudo".

```
sudo cpan install Text::Levenshtein::XS
```
Respond "yes" when prompted.

```
cd /usr/local/bin
sudo git clone https://github.com/mfcovington/auto_barcode.git

sudo ln -s /usr/local/bin/auto_barcode/barcode_split_trim.pl /usr/local/bin/barcode_split_trim.pl

barcode_split_trim.pl
```

## Data ##

Clone Assignment_8 repository.
Change directory to Assignment_8 directory.
Now change directories into the input/Brapa_reference directory.

```
#paste the clone link into terminal etc
cd Unix_Tutorials/
cd assignment8-bklong/
cd input/
```

download and unzip *Brassica rapa* fasta reference file and gff reference file:

```
wget https://bis180ldata.s3.amazonaws.com/downloads/Illumina_Assignment/BrapaV1.5_chrom_only.fa.gz
gunzip BrapaV1.5_chrom_only.fa.gz
```
download the *Brassica rapa* gff reference file:

```
wget https://bis180ldata.s3.amazonaws.com/downloads/Illumina_Assignment/Brapa_gene_v1.5.gff.gz
gunzip Brapa_gene_v1.5.gff.gz
```
Download the fastq file:

```
wget https://bis180ldata.s3.amazonaws.com/downloads/Illumina_Assignment/GH.lane67.fastq.gz
```

## Index the *B. rapa* Genome ##

bowtie2 needs a memory-efficient index of the reference genome file. We can create that with the code below. This will take about 6 minutes to run:

```
bowtie2-build --threads 3 BrapaV1.5_chrom_only.fa BrapaV1.5_chrom_only
# this takes about 6 minutes to run
```

## Check Out the fastq File ##

### Exercise 1: look at fastq file ###

A: What is the read length? (can you do this without manually counting?)

`fastqc` opens up the GUI 
In the GUI, the sequence length is stated as 100bp.

B: What is the machine name?

**? I'm not sure what this means. FastQC?**

C: How many reads are in this file?

There are 1,000,000 reads in the file.

D: Are the quality scores Phred+33 or Phred+64? How did you figure this out?

Under encoding, Illumina version 1.5 is stated on FastQC. I googled which Phred scores correspond to that illumina version, and found that Phred+64 is used.

**(Not sure if I got this right.)**

## Check Sequence Quality ##

`fastqc` at the command line will open up a GUI that you can point to your fastq or fastq.gz file to check the quality of the sequence.

### Exercise 2 ###

Compare your fastq results to the examples of good sequence and bad sequence on the fastqc website. Comment on any FastQC items that have an “X” by them in the report. What might have caused these issues? (hint: think about barcodes).

link: https://www.bioinformatics.babraham.ac.uk/projects/fastqc/good_sequence_short_fastqc.html

The per base sequence quality and per base sequence content have red Xs (bad quality) and the rest is good quality. My guess is that the barcodes that were added to these sequences skewed the bases in the reads.

## Filter Reads ##

It is generally a good idea to trim reads when their quality drops below 20 or so. We will use trimmomatic.

### Exercise 3 ###

Take a look at the trimmomatic web page and user manual and figure out how to modify the command below to clip after the average read quality across 4 bases drops to 20 or less. AND only retain reads where the trimmed length is 50bp or more.

link to user manual: http://www.usadellab.org/cms/uploads/supplementary/Trimmomatic/TrimmomaticManual_V0.32.pdf

Modify and then run this command:

```
trimmomatic SE -phred64 GH.lane67.fastq.gz GH.lane67.trimmed.fastq SLIDINGWINDOW:4:20 MINLEN:50
```
Return:

```
TrimmomaticSE: Started with arguments:
 -phred64 GH.lane67.fastq.gz GH.lane67.trimmed.fastq SLIDINGWINDOW:4:20 MINLEN:50
Automatically using 4 threads
Input Reads: 1000000 Surviving: 957893 (95.79%) Dropped: 42107 (4.21%)
TrimmomaticSE: Completed successfully
```

a What trimmomatic command did you use?

I believe I used SLIDINGWINDOW and MINLEN.

b How many reads were removed by trimming?

42,107 were removed

c Trimmomatic has the option to remove Illumina adapters. Why did we not do that here? hint: look at the fastqc output

**?**

d rerun FastQC on the trimmed sequences. Which issues did the trimming fix?

The trimming resolved the per base sequence quality. It is improved now. The per base seq content is the same, and the sequence length distribution now has !.

(If you want a good challenge you can try to figure out how many reads had some basepairs trimmed but were retained)

## Barcode Splitting ##

The fastq file contains reads from many different samples, indexed with a 5’ barcode on each read. We need to split the reads according to the barcode and then remove the barcode from the 5’ end.

### Exercise 4 ###

**NEED HELP HERE**

Look at the README for auto_barcode and figure out how to run it to split your samples. Specify that the split fastq files are placed in the directory split_fq. Use the Perl (.pl) version of the script

link: https://github.com/mfcovington/auto_barcode

hint: at the bottom of the README are three examples of how to run the command. The second one is pretty close to what you need

a what command did you use?

```
 barcode_split_trim.pl --id Demo --barcode barcode_key_GH.txt --list *GH.lane67.trimmed.fastq
 ```

b what percentage of reads did not match a barcode? What are possible explanations?

I'm not sure how to see the percentage.

## Check Quality of Each Sequence Library ##

Now that we have split the sequences by barcode we can check the quality of each library individually.

To do this we will first run fastqc in command line mode and then run multiqc to aggregate the results.

The code below assumes that you are in your Assignment_08/input/Brapa_fastq directory. cd there if you are not.

```
mkdir -p Unix_Tutorials/assignment8-bklong/output/fastqc_out
fastqc --threads 3 -o Unix_Tutorials/assignment8-bklong/output/fastqc_out split_fq/*.fq

cd Unix_Tutorials/assignment8-bklong/output
multiqc fastqc_out
```
**Need help with above code.**

You may need to change the paths above.

Now look at the report. You can have Firefox open the html from the command line:

```
firefox multiqc_report.html
```
**Firefox couldn't find that site.**

### Exercise 5 ###

a: What library has the worst quality based on the Sequence Quality Histograms? Is it bad enough to concern you?

b: Click on each of the other tests. Which three libraries are most problematic? In which tests?

c: Do you think the libraries should be removed from downstream analysis?

## Mapping ##

If we identified serious problems from multiqc we would probably want to do some additional filtering or remove those libraries. But for now we will just proceed with all the libraries.

Now map/align the sequences to the indexed reference. We will use tophat, which in turn calls bowtie2. tophat handles the splice junctions whereas bowtie2 actually maps the fastq sequences to the genome. Note that there are many options that could be specified (See bowtie2 -h and tophat -h).

Change directories back to the Assignment_08 directory

A single fastq file could be mapped using:

```
tophat --phred64-quals -p 4 input/Brapa_reference/BrapaV1.5_chrom_only  input/Brapa_fastq/split_fq/IMB211_DP_1_SILIQUE.fq
```
Where the first path give the location of the bowtie index (built before Exercise 1) and the second path gives the location of the fastq file that you want to map.

### Exercise 6 ###

**I started on this, but I am having a hard time due to how new for loops are for me.**

Using a bash for loop run tophat on all of the fastq files. Show your code here. Make sure that the results are going somewhere in your output directory. Also: IMPORTANT By default tophat will write to directory “tophat_out”. If you run it multiple times it will overwrite the contents of this directory each time. Therefore be sure to read the tophat help file so that you can figure out how to have your for loop write to a new directory for each fastq file!

IMPORTANT 2 This will probably take about an hour to run. You can leave your instance on but do not need to stay in lab while it runs.

```
for fastq file in ${input}
do
tophat --phred64-quals -p 4 input/Brapa_reference/BrapaV1.5_chrom_only  input/Brapa_fastq/split_fq/IMB211_DP_1_SILIQUE.fq
done 
```












