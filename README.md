# lab10_transcription_factors

In this weeks lab we are going to identify transcription factors in our coding sequences. Then we are going to search for that transcription factor's motif in our original genome. 

[Step 1 - Setup](#step-1---setup)

[Step 2 - Find TFs](#step-2---find-transcription-factors)

[Step 3 - Results](#step-3---examine-your-results)

[LQ 1](#lq-1)

[LQ 2](#lq-2)

[Step 4 - TF info](#step-4---tf-info)

[LQ 3](#lq-3)

[Step 5 - Motif Search](#step-5---search-for-the-motif)

[LQ 4](#lq-4)

[LQ 5](#lq-5)

[Step 6 - Gene?](#step-6---gene)

[Step 7 - Blast](#step-7---blast-your-region)

[LQ 6](#lq-6)

[]()

&nbsp;
## Step 1 - Setup

We will need to setup our folder with all the files we will need this week. 

You will need the following files in your directory to start 
- cerevisiae_tfs.info.fasta
- cerevisiae_tfs.fasta
- clear_SRR00000.prot.fasta
- SRR00000-contigs.v2.fasta

The two files ```cerevisiae_tfs.info.fasta``` and ```cerevisiae_tfs.fasta``` are both in the folder ```/projects/class/binf3101_001/tf_files/```

Copy both of these files to your lab 10 directory.

You will have a copy of your protein sequences, in the form of```clear_SRR00000.prot.fasta``` in your lab_9 folder. 

You will have a copy of your assembled genome, in the form of ```SRR00000-contigs.v2.fasta``` in your lab_5 folder. 

&nbsp;

## Step 2 - Find Transcription Factors

The ```cerevisiae_tfs.fasta``` file contains protein sequences of transcription factors from _Saccharomyces cerevisiae_. We will use these as the reference sequences to search our protein sequences. 

As usual we will use ```blast``` to do this search. I have written out the code we will use below as there are some new options. 

```bash
module load blast    #load our blast

#don't forget to replace the prot.fasta file with your sequence

blastp -query cerevisiae_tfs.fasta -subject clear_SRR00000.prot.fasta -max_target_seqs 1 -outfmt 6 -evalue 1e-50 > tf_results.out

```

We have added several new commands **that we will use again** so read about them below

```blastp``` Protein blast
```-query cerevisiae_tfs.fasta``` - This is what we are searching for. We are setting the query to our tf file because we want to find **1 hit** per transcription factor and _not_ one hit per our genome's protein sequences
```-subject clear_SRR00000.prot.fasta``` - This is what we are searching in. 
```-max_target_seqs 1``` - We want to examine only the _best_ hit for each transcription factor. Typically you would want to look for all the hits at a particular threshold. This is a shortcut to get us only the best hits. 
```-outfmt 6``` - This format will report statistics for our search in an easy to read format. 
```-evalue 1e-50``` - a reasonable threshold for our search

&nbsp;

## Step 3 - Examine your results

The results of the blast search are saved in the file ```tf_results.out```

Take a look at this file but this time use the command 

```bash
column -t tf_results.out
```

This will display the results with all the columns aligned

&nbsp;

# LQ 1
How many TFs did you find in your protein sequences? 

&nbsp;

### Step 3a - Examine the different columns

There are 12 columns in the file. These are the columns

   1.  qseqid      query or source (gene) sequence id

   2.  sseqid      subject or target (reference genome) sequence id

   3.  pident      percentage of identical positions

   4.  length      alignment length (sequence overlap)

   5.  mismatch    number of mismatches

   6.  gapopen     number of gap openings

   7.  qstart      start of alignment in query

   8.  qend        end of alignment in query

   9.  sstart      start of alignment in subject

 10.  send        end of alignment in subject

 11.  evalue      expect value

 12.  bitscore    bit score

&nbsp;

# LQ 2
Which query gene/sequence id had the lowest/most significant e-value? If there is more than one sequence, choose one. 

&nbsp;

## Step 4 - TF info

Now we need to figure out what is the _name_ of the transcription factor you are going to look for. The sequence ID is the identifier of that gene in _S. cerevisiae_. 

The name of the gene is listed in the ```cerevisiae_tfs.info.fasta``` file. Find the name (second column) of your transcription factor 

&nbsp;

# LQ 3 
What is the gene name (column 2) of your transcription factor? What is the description (last column) of your transcription factor?

&nbsp;

### Step 4a - copy the motif file

You now need to copy the **motif** file that contains information about the motif or motifs recognized by your transcription factor. 

The format for this file is ```GENE.meme``` where GENE is the name of the gene you found in the second column. 

Cop this file into your directory. It is found in the folder: ```/projects/class/binf3101_001/``` 

&nbsp;

## Step 5 - Search for the motif

We are now going to search the genome for the motif bound by your transcription factor. To do this, we will use the **Meme** suite we discussed in class. Specifically we will use the ```fimo``` search method. 

Here is how to search for the motif in your genome

```bash
module load meme      #load the meme suite

fimo GENE.meme SRR00000-contigs.v2.fasta

````

You should now see several files, including ```fimo.tsv```. The tsv file will contain our results

&nbsp;

# LQ 4 
How many motifs were found in your genome? _hint: the gene name listed in column 2 will be in every line_

&nbsp;

# LQ 5 
Select one motif hit and make sure there is on the **positive strand**. The value in the 6th column should be "+". Answer the following questions about your motif hit

- What is the sequence name that it was found on? (It is the entire "word", for example ```52626_3228_45048_179+,...,43143-```)
- What is the start position of the motif?
- What is the end position of the motif?

&nbsp;

## Step 6 - Gene?

When the transcription factor binds to the motif, it may regulate the gene that is _closest_ to it. We want to figure out what gene that might be.

&nbsp;

### Step 6a - Define boundaries

We want to look 5,000 base pairs down from our gene. To do this, you will add 5,000 to the **end position** of the motif you found above.

For example, if the motif ended at position 246, I would want to look up until position 5,246.

To extract the sequence we want we need to save the information to a file.

```bash
echo -e "SEQUENCE_NAME\tSTART_POS\tSEARCH_END" > to_get.txt
```

The ```\t``` character says to print a "tab"

If this were the raw information for my sequence

```52626_3228_45048_179+,...,43143-	231	246```

I would use the command

```echo -e "52626_3228_45048_179+,...,43143-\t231\t5246" > to_get.txt```

Now we want to print the sequence to the screen 

&nbsp;

### Step 6b - Get the sequence

We will use bedtools again

```bash
module load bedtools2

bedtools getfasta -fi SRR00000-contigs.v2.fa -bed to_get.txt

```

**NOTE - IF YOU GET THIS ERROR** you will have to edit your to_get.txt file

If you get an error that says ```Feature (52626_3228_45048_179+,...,43143-:231-5246) beyond the length of 52626_3228_45048_179+,...,43143- size **(3228 bp)**.  Skipping.``` that means you asked for a point _beyond_ the end of the fasta sequence. 

Therefore, instead of the endpoint you previously used, you will substitute it for the value reported. In the example above that is 3228

You will the re-do the previous two steps

For example:

```bash
echo -e "52626_3228_45048_179+,...,43143-\t231\t3228" > to_get.txt
bedtools getfasta -fi SRR00000-contigs.v2.fa -bed to_get.txt
```

You should now have a fasta sequence printed to your screen. 

**COPY THIS SEQUENCE TO A FILE** Copy this sequence to a word document or text document _just in case_ you accidentally close your terminal before completing. 

&nbsp;

## Step 7 - Blast your region

We now want to figure out _if there is a gene_ present in our region we extracted. 

We will use BlastX - Open this link to get to blastx https://blast.ncbi.nlm.nih.gov/Blast.cgi?PROGRAM=blastx&PAGE_TYPE=BlastSearch&LINK_LOC=blasthome

Paste the sequence from the terminal into the section that says **Enter Query Sequence**

Then click **Blast**

&nbsp;

# LQ 6
Were there any genes in the region you searched?

If so, what is that gene likely to be? This information will likely be in the descriptions of the genes that _do not start with "hypothetical protein" or "uncharacterized protein"_. 

For example, if you got the results shown below, you would say it's probably a "cell wall protein" or "family 17 glucosidase"

![image](https://github.com/BINF-3101/lab10_transcription_factors/assets/47755288/67b2d838-358c-4859-bd30-a7382d2d735b)





  





