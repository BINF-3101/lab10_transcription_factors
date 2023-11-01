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
