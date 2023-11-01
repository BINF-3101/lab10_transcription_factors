# lab10_transcription_factors

In this weeks lab we are going to identify transcription factors in our coding sequences. Then we are going to search for that transcription factor's motif in our original genome. 

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

## Step 3 - Examine your results

The results of the blast search are saved in the file ```tf_results.out```

Take a look at this file but this time use the command 

```bash
column -t tf_results.out
```

This will display the results with all the columns aligned

# LQ 1 - How many TFs did you find in your protein sequences? 

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


# LQ 2 - Which query gene/sequence id had the lowest/most significant e-value? If there is more than one sequence, choose one. 

## Step 4 - TF info

Now we need to figure out what is the _name_ of the transcription factor you are going to look for. The sequence ID is the identifier of that gene in _S. cerevisiae_. 

The name of the gene is listed in the ```cerevisiae_tfs.info.fasta``` file. Find the name (second column) of your transcription factor 

# LQ 3 
What is the gene name (column 2) of your transcription factor? What is the description (last column) of your transcription factor?


### Step 4a - copy the motif file

You now need to copy the **motif** file that contains information about the motif or motifs recognized by your transcription factor. 

The format for this file is ```GENE.meme``` where GENE is the name of the gene you found in the second column. 

Cop this file into your directory. It is found in the folder: ```/projects/class/binf3101_001/``` 

## Step 5 - Search for the motif

We are now going to search the genome for the motif bound by your transcription factor. To do this, we will use the **Meme** suite we discussed in class. Specifically we will use the ```fimo``` search method. 

Here is how to search for the motif in your genome

```bash
module load meme      #load the meme suite

fimo GENE.meme SRR00000-contigs.v2.fasta

````

You should now see several files, including ```fimo.tsv```. The tsv file will contain our results

# LQ 4 
How many motifs were found in your genome? _hint: the gene name listed in column 2 will be in every line_

# LQ 5 
Select one motif hit and make sure there is on the **positive strand**. The value in the 6th column should be "+". Answer the following questions about your motif hit

- What is the sequence name that it was found on? (It is the entire "word", for example ```52626_3228_45048_179+,...,43143-```)
- What is the start position of the motif?
- What is the end position of the motif?

## Step 6 - Gene?

When the transcription factor binds to the motif, it may regulate the gene that is _closest_ to it. We want to figure out what gene that might be.

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

The file should then 


  





