---
layout: tutorial_hands_on

title: "Genome annotation and analysis with panaroo"
zenodo_link: "https://zenodo.org/records/14796384"

questions:
  - "What is Panaroo?"
  - "What are the inputs?"
  - "What are the expected outputs?"
  - "How does Panaroo make pangenome analysis more accurate?"
  - "How does Panaroo’s performance compare to other methods?"
  - 
objectives:
  - "Correct for many of the sources of annotation error"
  - "Improve annotation calls and allow for the comparison of pangenomes between species"
  - 
time_estimation: "15m"

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

## What does it do

> <hands-on-title> Data Upload </hands-on-title>
>
> 1. Create a new history for this tutorial and give it a proper name
>
>    {% snippet faqs/galaxy/histories_create_new.md %}
>
>    {% snippet faqs/galaxy/histories_rename.md %}
>
> 2. Import the file `query.faa` from [Zenodo]({{ page.zenodo_link }})
>
>    ```
>    https://zenodo.org/records/7524427/files/query.faa
>    ```
>
>    {% snippet faqs/galaxy/datasets_import_via_link.md %}
>
>
{: .hands_on}

We just imported a FASTA file into Galaxy. Now, the next would be to perfrom the BLAST analysis against MAdLandDB.

## What are the inputs

Since MAdLandDB is the collection of protein sequences, You can perform {% tool [BLASTp](toolshed.g2.bx.psu.edu/repos/devteam/ncbi_blast_plus/ncbi_blastp_wrapper/2.10.1+galaxy2) %} and {% tool [BLASTx](toolshed.g2.bx.psu.edu/repos/devteam/ncbi_blast_plus/ncbi_blastx_wrapper/2.10.1+galaxy2) %} tools.

> <hands-on-title> Similarity search against MAdLand Database </hands-on-title>
>
> 1. {% tool [BLASTp](toolshed.g2.bx.psu.edu/repos/devteam/ncbi_blast_plus/ncbi_blastp_wrapper/2.10.1+galaxy2) %} OR {% tool [BLASTx](toolshed.g2.bx.psu.edu/repos/devteam/ncbi_blast_plus/ncbi_blastx_wrapper/2.10.1+galaxy2) %} with the following parameters:
>    - _"Protein query sequence(s)"_: `Amino acid input sequence` (In case of BLASTp) *OR*
>    - _"Translated nucleotide query sequence(s)"_: `Translated nucleotide input sequence` (In case of BLASTx)
>    - _"Subject database/sequences"_: `Locally installed BLAST database`
>    - _"Protein BLAST database"_: `MadLandDB (Genome zoo) plant and algal genomes with a focus on non-seed plants and streptophyte algae (22 Dec 2022)`
>    - _"Set expectation value cutoff"_: `0.001`
>    - _"Output format"_:
>    - In _"Output Options"_: `Tabular (extended 25 columns)`
> <img src="../../images/ncbi-blast-against-the-madland/blast-example.png" alt="blast against madland" width="80%">
>
{: .hands_on}

## What are the expected outputs

{% icon tool %} The BLAST output will be in tabular format (you can select the desired output format from the drop down menu) and include the following fields :

| Column | NCBI name | Description |
|-------|------------|-------------|
| 1 | qseqid | Query Seq-id (ID of your sequence) |
| 2 | sseqid | Subject Seq-id (ID of the database hit) |
| 3 | pident | Percentage of identical matches |
| 4 | length | Alignment length |
| 5 | mismatch | Number of mismatches |
| 6 | gapopen | Number of gap openings |
| 7 | qstart | Start of alignment in query |
| 8 | qend | End of alignment in query |
| 9 | sstart | Start of alignment in subject (database hit) |
| 10 | send | End of alignment in subject (database hit) |
| 11 | evalue | Expectation value (E-value) |
| 12 | bitscore | Bit score |

The fields are separated by tabs, and each row represents a single hit. For more details for BLAST analysis and output, we recommand you to follow the [Similarity-searches-blast]({% link topics/genome-annotation/tutorials/genome-annotation/tutorial.md %}#similarity-searches-blast) tutorial.


> <details-title>Further Reading about BLAST Tools in Galaxy</details-title>
>
> See {% cite Cock2015 %} and {% cite Cock2013 %}
>
{: .details}

## How to interpret the results

* **Diamond**: {% tool [Diamond](toolshed.g2.bx.psu.edu/repos/bgruening/diamond/bg_diamond/2.0.15+galaxy0) %} is a high-throughput program for alignment of  large-scale data sets. It aligns sequences to the reference database using a compressed version of the reference sequences called a "database diamond" which is faster to read and can save computational time (~20,000 times the speed of Blastx, with high sensitivity).

> <details-title>Diamond in depth</details-title>
>
> See {% cite Buchfink2014 %} for more discussion.
{: .details}


