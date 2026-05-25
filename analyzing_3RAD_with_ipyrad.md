# Analysing 3RAD data with ipyrad

This file explains how to analyse 3RAD data with ipyrad.

## Work in Ripley

This tutorial assumes that you are working on a computing server. For more info on these aspects, check the [work_in_Ripley.md]() file before continuing with this tutorial.


## Install ipyrad

We will be working within a virtual environment in conda.

We will create an environment called ipyrad, open it (activate), and then install ipyrad.

```sh
conda create -n ipyrad
conda activate ipyrad
conda install ipyrad -c conda-forge -c bioconda
```

To exit the environment called ipyrad, type

```sh
conda desactivate
```


## Remove PCR duplicates

This step is optional and could only be done if a random nucleotide tag has been added to the adapters, which is the default in the Joly laboratory. The idea is that a radom sequence of 8 nucleotides is added when generating one Illumina adapter. Therefore, is two reads have the exact same sequences, including the random primer, we can almost be certain that they are PCR duplicates. These could be removed from the dataset because they are not independent information on the genome sequence. This is particularly important for population genetic application where homozygote and heterozygote callings are important.


### Combining sequence files

If you have multiple sequence files, the first step here is to combine different sequence files of the same library in a single file. This is important because the software will use the information of one file to exclude PCR duplicates. You have to do this for reads 1 and 2 separately, combining the sequence files in the same order in both cases.

```sh
cat ./seqs/rawseqs/spike/p01/impatiens1_iTRU7_101_01_S1_L001_R1_001.fastq.gz ./seqs/rawseqs/run1/p01/impatiens1_iTRU7_101_01_R1.fastq.gz > ./seqs/rawseqs/allruns/p01/plate01_R1_spikeandrun1.fastq.gz
cat ./seqs/rawseqs/spike/p01/impatiens1_iTRU7_101_01_S1_L001_R2_001.fastq.gz ./seqs/rawseqs/run1/p01/impatiens1_iTRU7_101_01_R2.fastq.gz > ./seqs/rawseqs/allruns/p01/plate01_R2_spikeandrun1.fastq.gz
```


### Removing PCR duplicates

To remove PCR duplicates, we use a program called [dedup](https://github.com/simjoly/dedup). All the info on how to compile and use the program are in the github repository.

dedup can take a while on large files, so it could be a good idea to use `screen`.

```sh
./dedup --read1 ./seqs/rawseqs/allruns/p01/plate01_R1_spikeandrun1.fastq.gz --read2 ./seqs/rawseqs/allruns/p01/plate01_R2_spikeandrun1.fastq.gz --barcode-in-name --use-bloom
```

`dedup` saves the output files in the directory where the command was given. You'll have to move the sequence files without duplicates afterwards.

```sh
mkdir ./seqs/rawseqs/allruns/p01/nodups/
mv nodup_plate01_R1_spikeandrun1.fastq.gz ./seqs/rawseqs/allruns/p01/nodups/
mv nodup_plate01_R2_spikeandrun1.fastq.gz ./seqs/rawseqs/allruns/p01/nodups/
```

## ipyrad

Before using iyrad, you should read the [documentation](https://ipyrad.readthedocs.io/en/master/index.html). It explains well how it works, what are the steps and parameters, and provides many tutorials.

I suggest to run ipyrad on the /data/ disk because it creates a lot of files and the other disk might get full! So run everything from /data/joly_data/impatiens, for instance.

Create a new analysis for plate 01.

```bash
conda activate ipyrad
ipyrad -n run1-p01
```

Edit the parameter file. Here's an example for 3RAD:

```
------- ipyrad params file (v.0.9.95)-------------------------------------------
run1-p01                                                ## [0] [assembly_name]: Assembly name. Used to name output directories for assembly steps
/data/joly_data/impatiens/                              ## [1] [project_dir]: Project dir (made in curdir if not present)
/data/joly_data/impatiens/seqs/rawseqs/allruns/p01/nodups/*.fastq.gz    ## [2] [raw_fastq_path]: Location of raw non-demultiplexed fastq files
/data/joly_data/impatiens/barcodes/barcodes_p01.txt              ## [3] [barcodes_path]: Location of barcodes file
                                                        ## [4] [sorted_fastq_path]: Location of demultiplexed/sorted fastq files
reference                                               ## [5] [assembly_method]: Assembly method (denovo, reference)
/data/joly_data/impatiens/Impatiens_reference_genome/    ## [6] [reference_sequence]: Location of reference sequence file
pair3rad                                                ## [7] [datatype]: Datatype (see docs): rad, gbs, ddrad, etc.
CTAGC,CTAGA,AATTC                                       ## [8] [restriction_overhang]: Restriction overhang (cut1,) or (cut1, cut2)
5                                                       ## [9] [max_low_qual_bases]: Max low quality base calls (Q<20) in a read
33                                                      ## [10] [phred_Qscore_offset]: phred Q score offset (33 is default and very standard)
6                                                       ## [11] [mindepth_statistical]: Min depth for statistical base calling
6                                                       ## [12] [mindepth_majrule]: Min depth for majority-rule base calling
10000                                                   ## [13] [maxdepth]: Max cluster depth within samples
0.90                                                    ## [14] [clust_threshold]: Clustering threshold for de novo assembly
1                                                       ## [15] [max_barcode_mismatch]: Max number of allowable mismatches in barcodes
2                                                       ## [16] [filter_adapters]: Filter for adapters/primers (1 or 2=stricter)
35                                                      ## [17] [filter_min_trim_len]: Min length of reads after adapter trim
2                                                       ## [18] [max_alleles_consens]: Max alleles per site in consensus sequences
0.05                                                    ## [19] [max_Ns_consens]: Max N's (uncalled bases) in consensus
0.05                                                    ## [20] [max_Hs_consens]: Max Hs (heterozygotes) in consensus
15                                                      ## [21] [min_samples_locus]: Min # samples per locus for output
0.2                                                     ## [22] [max_SNPs_locus]: Max # SNPs per locus
5                                                       ## [23] [max_Indels_locus]: Max # of indels per locus
0.5                                                     ## [24] [max_shared_Hs_locus]: Max # heterozygous sites per locus
0, 0, 0, 0                                              ## [25] [trim_reads]: Trim raw read edges (R1>, <R1, R2>, <R2) (see docs)
0, 0, 0, 0                                              ## [26] [trim_loci]: Trim locus edges (see docs) (R1>, <R1, R2>, <R2)
p, s, l, u, v                                           ## [27] [output_formats]: Output formats (see docs)
                                                        ## [28] [pop_assign_file]: Path to population assignment file
                                                        ## [29] [reference_as_filter]: Reads mapped to this reference are removed in step 3
```

I notice that the reference genome needs to be in the 'home' folder for the analyses, otherwise ipyrad crashes. 

Once the parameter file is edited, you can run the step 1, which will demultiplex the sequences according to the internal barcodes. This has to be done for each plate individually as each plate has the same internal barcodes. Sequences will be grouped by samples and a different file will be created.

```bash
ipyrad -p params-run1-p01.txt -c 24 -s 1
```

To see the results after step 1.

```bash
#output results
COLUMNS=9999 ipyrad -p params-run1-p01.txt -r > results-run1-p01.txt
```

You can repeat this step for all the plates you have, creating a new ipyrad project each time.

Once you have run step 1 for all plates, you can merge all projects into one.

```bash
## merge the two lanes into one Assembly named both
ipyrad -m run1-allplates params-run1-p01.txt params-run1-p02.txt params-run1-p03.txt params-run1-p04.txt
```

```bash
## print merged stats of new Assembly
COLUMNS=9999 ipyrad -p params-run1-allplates.txt -r > results-run1-allplates.txt
```

```bash
#run remaining steps on the merged assembly
ipyrad -p params-run1-allplates.txt -c 24 -s 234567
```

```bash
#output results
COLUMNS=9999 ipyrad -p params-run1-allplates.txt -r > results-run1-allplates.txt
```



