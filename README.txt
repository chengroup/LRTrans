# This file is part of LRTrans which is a toolset for clustering, 
# filtering and quantifying the mapped long-read transcripts.
#
# LRTrans include three python scripts:
# (1)bam_parser   (2)read_cluster   (3)transcript_filter
#
#
# These scripts are distributed in the hope that it will be useful,
# but WITHOUT ANY WARRANTY; without even the implied warranty of
# MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
#

# STEPS
After preprocessing steps such as quality control and error correction, 
long cDNA/RNA sequences were aligned against the reference genome
using Minimap2 or the like tools. With samtools, the alignments can be 
saved in a sorted BAM file. Starting with the sorted bam file, 
LRTrans can construct and quantify transcript isoforms by performing
the following steps.

1. Parse and extract information from SORTED BAM file.

Tool:
    bam_parser
Command:
    ./bam_parser -i sorted.bam -r reference.fa -o parsed_read.tsv

Usage:
bam_parser --sorted_bam_file path_to_bam --reference_file path_to_fasta --parsed_file path_to_output

Input files:
    -i , --sorted_bam_file:    Path to the input bam file. (Required)
    -r , --reference_file:     Path to the reference fasta file used to extact splice site sequences. (Required)

Output file:
    -o , --parsed_file:        Path to the output file required by the cluster program. (Required)



2. Cluster transcript reads into isoforms. The abundance of each isoform will be recorded.

Tool:
    read_cluster
Command:
    ./read_cluster -i parsed_read.tsv -o clustered_read.tsv

Usage:
read_cluster --parsed_file path_to_tsv --cluster_file path_to_output

Input files:
    -i  , --parsed_file:      Path to the tsv file outputted by bam_parser. (Required)

Output file:
    -o  , --cluster_file:     Path to the output file that include cluster information. (Required)

Cluster parameter:
    -ebt, --exon_boundary_tolerance        Three prime exons boundary tolerance. (default = 15)
    -ibt, --introns_boundary_tolerance     Internal introns boundary tolerance. (default = 3)
    -msi, --minimum_size_of_intron         Introns with a length (bp) below this value are ignored. (default = 30)



3. Quality filtering and output transcriptoms(GFF3) and representative sequence(fasta).

Tool:
    transcript_filter
Command:
    ./transcript_filter -i clustered_read.tsv -b sorted.bam -op filter_out
    
Usage:
transcript_filter --cluster_file path_to_tsv --bam_file path_to_bam --out_prefix path_to_out

Input files:
    -i , --cluster_file:    Path to the tsv file outputted by read_cluster. (Required)
    -b , --bam_file:        Path to the input bam file. (Required)

Output file:
    -op, --out_prefix      Prefix/path to the output files. (Required)

filter parameter:
    -dn , --standard_donor        Standard donor of splice sites, set off with commas.(default = GT,GC,AT)
    -ac , --standard_acceptor     Standard acceptor of splice sites. (default = AG,AC)
    -ia , --isoform_abundance     Isoform whose abolute abundance is below this value will be filtered. (default = 3)
    -sa , --single_exon_abundance Isoform with single exon will be filtered if its absolute abundance is lower than this value. (default = 3)
    -fr , --filter_by_ratio       The minimum ratio of the isoform relative abundance in the parent gene locus in clusting. (default = 0.05)
    -ej , --erroneous_junction    Isoform whose number of erroneous junction sites is above this value will be filtered. (default = 0)
