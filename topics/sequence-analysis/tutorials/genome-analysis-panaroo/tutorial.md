---
layout: tutorial_hands_on

title: "Genome annotation and analysis with Panaroo"
zenodo_link: ""

questions:
  - "What is Panaroo?"
  - "What are the inputs?"
  - "What are the expected outputs?"
  - "How does Panaroo make pangenome analysis more accurate?"

objectives:
  - "Correct for many of the sources of annotation error"
  - "Improve annotation calls and allow for the comparison of pangenomes between species"

time_estimation: "20m"

key_points:
  - Panaroo is able to account for many of the sources of error during automated annotation.
  - Panaroo uses a graph-based approach to correct annotation errors, fragmented assemblies, and contamination, leading to more accurate genome predictions.
  - Panaroo is written in Python, open-source, and designed to handle large datasets.
  - Panaroo also includes a number of pre- and post-processing scripts for the analysis of bacterial pangenomes that assist in quality control of the input data and facilitate downstream processing of the pangenome.

contributions:
  authorship:
    - LuisFernandoMartinGarcia

tags:
  - sequence analysis
  - genome annotation
---

Panaroo is a graph-based pangenome clustering tool, that is designed to correct for many sources of error introduced during annotation. This includes refinding genes were there are inconsistencies. Panaroo does this by clustering genes based on sequence similarity and identifies similar genes from different species.

> <agenda-title></agenda-title>
>
> In this tutorial, we will deal with:
>
> 1. TOC
> {:toc}
>
{: .agenda}

# What does Panaroo do

Panaroo builds a graph that represents the pangenome, where nodes are clusters of orthologous genes and edges connect nodes if they are adjacent on a sequence in any sample from the population. This graphical representation is then used for error correction and cleaning for the problems found in genome annotation. Panaroo corrects the errors by collapsing diverse gene families, merging fragmented gene segments, filtering contamination, and identifying missing genes.

Fragmented assemblies can lead to issues where a DNA sequence is fragmented or split into smaller parts during assembly. These kind of erroneous annotations tend to appear as short paths of weak edges and nodes that end in a node of degree 1 that also splits of from the main graph. Panaroo removes recursively nodes of degree 1 that are below a support threshold. To remove contaminating contiguous sequences of DNA or RNA, Panaroo uses the same approach.

Panaroo takes as input annotated genomes in GFF3 format as output by the annotation pipeline Prokka and generates a variety of outputs, including a presence-absence matrix and an annotated graph. Panaroo tries to preserve the global context of each gene in the graph. 

Panaroo generates initial gene clusters by using a tool called CD-HIT (Cluster Database at High Identity with Tolerance), which is for clustering and comparing protein or nucleotide sequences. Resulting clusters are split into two groups. Either as non-paralogous gene clusters if they contain at most one instance of each genome, or paralogous clusters if they contain more than one instance of each genome. First  non-paralogous gene clusters are illustrated by a single node in the graph and  paralogous clusters are illustrated by a node for every occurence of that cluster in the dataset. Later on the graph is built by connecting cluster node with edges and paralogous nodes are merged into the highest number of nodes where those genes are present within a single genome using the global context of the graph.

Panaroo identifies gene families using an alignment threshold together with neighbourhood information. Potentially missing genes from one or more samples are identified in the graph and the continuous sequence of DNA fragments assembled from overlapping pieces of genetic data near neighbouring nodes is searched to check for the presence of the gene.

To construct the graph, Panaroo uses a number of predefined thresholds that can all be adjusted by the user. Panaroo has several modes available for common use cases. 

The ‘strict’ mode is more aggressive for the removal of contamination and error in the annotation. This is mostly used when investigating genomes where parameters such as gene gain and loss rates are relevant or if no rare plasmids are expected.

In its ‘sensitive’ mode, Panaroo does not remove any gene clusters. This is mostly used if rare plasmids are expected, which may be hard to distinguish from contamination, but it is possible to get a higher number of erroneous clusters.

Panaroo includes a number of pre- and post-processing scripts that can be used for quality control, evaluating the pangenome size, and identifying coincident genes through gene gain and loss rates.

# What are the inputs

> <hands-on-title>Upload the datasets</hands-on-title>  
> 1. Upload your local files and let Panaroo read them:  
>   - Click on the three dots (Browse or Upload Datasets).  
>   - Click the "Upload" button on the bottom left.  
>   - Choose a local or remote file and upload it by clicking the "Start" button.  
>   - If they turn green, you can close this window, and then select your datasets.  
> 2. Set the parameters:  
>   - Take a look at the sections and set the parameters accordingly.  
>   - The parameters have a short description text right under them.  
>   - Note: Parameters with a "*" tag are required, and parameters with the "-optional" tag do not have to be defined in order for the tool to run correctly.  

{: .hands_on}

In order to run Panaroo properly, it needs at least two GFF3 files.  
![input data upload](/images/1.jpg "This is where you select your local GFF3 files")  
![input data upload](/images/2.jpg)  
![input data upload](/images/3.jpg "If everything is fine with the provided GFF3 files, it should then look like this")

Now you are able to run the tool. Before running it, you need to set the parameters correctly. Therefore, there are some sections with settings for the respective parameters.

![input data upload](/images/4.jpg "Here we can see Panaroo Mode. In here we have filtering mode that sets the aggressiveness for removing errors and contamination in the annotation. Here we can also see, thanks to the '*' symbol, that this is a required parameter and needs to be defined. Then we have the option to select which genetic code table to use when analyzing nucleotide sequences and we can choose from 33 options. At last we have the option to remove invalid genes but this option is not required.")

# What are the expected outputs

Panaroo generates between 12 and 17 outputs, depending on the parameter settings. With the default settings, it produces 12 outputs. 
Panaroo outputs a fully annotated pangenome graph, where each gene node and edge is annotated with the genomes it belongs to, the gene annotations provided by Prokka, the gene sequence, as well as the classification wheter the node is a paralog or not. This graph format is able to give insights other similar tools like Roary cannot, as Panaroo uses the global context and builds the entire pangenome graph. 

Here is how an output can look like:
![input data upload](/images/5.jpg)

Note: Panaroo outputs many of the same file formats as Roary to ease the integration with existing bioinformatics pipelines.

# How to interpret the results

results.
