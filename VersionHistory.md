# TOGA 1.0 #
This was used to produce all data in the original manuscript: 

Kirilenko BM, Munegowda C, Osipova E, Jebb D, Sharma V, Blumer M, Morales AE, Ahmed AW, Kontopoulos DG, Hilgers L, Lindblad-Toh K, Karlsson EK, Zoonomia Consortium, Hiller M.
[Integrating gene annotation with orthology inference at scale](https://www.biorxiv.org/content/10.1101/2022.09.08.507143v1). _Science_, in press, 2022

# TOGA 1.1 #


### Improved transcript classification ###
* Improved handling of compensating frameshifts. If two or more frameshifts (such as +1, +1, +1) compensate each other and restore the reading frame without having a stop codon in the frame-shifted region, TOGA does not consider these frameshifts as inactivating. However, the region between these frameshifts is translated into a protein sequence that is likely entirely different from the reference proteins. We observed extreme cases where compensating frameshifts cover essentially the entire transcript, such that considering the transcript as intact is not correct. 
In TOGA 1.1, we now consider the region between compensating frameshifts as deleted. This improves the transcript classification step. For example, the case where compensating frameshifts cover the entire transcript is now classified as lost. 

* Improved classification of N-terminal mutations. In TOGA 1.0, we masked inactivating mutations if they are located in the first 10% of the reading frame. The reason was that often there is a downstream ATG in proximity, but this is not always the case. 
In TOGA 1.1, we now determine where the next inframe ATG codon is located that is downstream of a potential inactivating mutation. If this ATG is located in the first 10% of the reading frame, the mutation is not considered as inactivating (as in 1.0). However, if this ATG is located outside the first 10% of the reading frame, a truncated protein would lack at least 10% of the N-terminus and TOGA 1.1 now considers this mutation as inactivating in the transcript classification step. 


### Codon alignments ###
* The codonAlignment.fasta file previously contained the raw CESAR output, including exons that were classified as deleted or missing in later processing steps. TOGA 1.1 now generates a codonAlignment.fasta where such exons are excluded. 


### bigBed browser tracks ###
* TOGA 1.0 generated bed and tab files that could be loaded as a total of 4 SQL tables to display the query annotation and provide rich information about each transcript via the TOGA handler function upon clicking on a transcript in the UCSC genome browser. TOGA 1.1 utilizes UCSC's bigBed functionality and produces a single bigBed file that contains all that information, enabling a SQL-free TOGA display. The TOGA handler function was updated to work with bigBed. The bigBed TOGA track often displays detailed information faster than the SQL version. 


### Minor changes ###
* Longer lists of several compensating frameshifts (e.g. FS_1,2,3,4,5) could result in warnings when loading SQL tables if the string gets too long. To avoid this, we now write a condensed format FS_1-5. 
* Avoiding overlapping exon annotations in fragmented query assemblies. If a gene is split into several query chains, there were rare cases where several query chains overlap in the query genome, resulting in exon annotations that overlap each other. This creates invalid bed or genePred records. In TOGA 1.1, we now classify such genes as missing and do not output a bed entry for this gene.
* Sometimes a gene had several orthologous chains and one of them is a 'runaway' chain that spans megabases in the query locus, which is not computable in the CESAR step. TOGA 1.1 now ignores the runaway chain but runs CESAR for the other (normal) orthologous chains. This adds a few orthologs to the annotation. 

