#Marine Refreence Database Design

##Summary

This design document describes the marine reference database developed in the Elixir Excelerate project (described in detila in the [[Background chapter in META-pipe]]). The reference database is closely integrated with the [[META-pipe pipeline]], the EBI Metagenomics Portal, and the Elixir technical services.

## Dependencies

Standards:
* Metagenomic data standards environment (developed as part of Elixir Excelerate marine use case).

Databases:
* ENA: for retrieving annotated genomes, and metagenomics raw sequence reads.
* EBI MGP: ?

Pipelines:
* META-pipe: to assemble ENA metagenomes.

Other infrastructure projects:
* MIRRI

Elixir services:
* Storage: backup, versioning
* Transfer: synchronize databases at EBI and NO
* Cloud: computation resources for assembly and other computation required to populate database

## Deliverables

* Database design and requiremetns (**this document!**) by 24.02.2016
* Beta-version by Christmas 2016

## Open Issues

Critical issues:
* ENA public data access
* Computational resources and cost required to assemble metagenomes
* Dependency on Elixir services for backup, replication, and versioning

See also the [[META-pipe issue tracker]].

Undecided:
* Include ukaryots and viurses in gold standard databse?

##Detailed Description

###Operaional Scenario

We will establish three different databases with different quality:
* Tier1 is gold standard and build upon complete..
* Tier2 will include all prokaryotic genomes independet of whether they are complete or nto.
* Tier3 will be based upon annotation of assembled marine metagenomics and metatransciptomics reads.

Summarize how the databases are updated, and when
* Tier1: when RefSeq is updated ...
* Tier2: ...
* Tier3: ...

How is database used and by whom?

### User Interface

### Architecture

Can use NCBI defintions for reference genomes, representative genomes, and variant genomes.

Database scehemas

Storage system.

###Performance Considerations

Tier 3: META-pipe must scale to dataset sizes. Highest possibility quality. Ensure standards are followed.

Initial perofrmance of META-pipe.

Dataset size expectation.

Experinces from MOCAT pipeline.

###MarRef

MarRef - Gold standard - complete genomes: (figure for Tier1, based on NCBI pipeline). 1000-200 genomes of which 500 are complete. Manual curation for starting. (MarRef pipeline figure). Outcome: MarRef nucleotide & MarRef protein.

###MarDb

Tier 2 - MarDb marine genome database (figure for Tier 2). Genome sequence datbaase (ENA), Ensembl bacteria (30K), NCBI microbial genomes resources (58.000).

No pipeline.

###MetaMar

Tier 3 - MArine gene catalog (figure for Tier 3). Inuput: marine metagenomics reads and marrine metatrancsiptome. META-pipe. Also MarRef and MarDb. Output: MetaMar nucleotide & MetaMar protein. Millions of genes.

###Data Storage

Data format:
* Useful in BLAST

Object storage?

###Data Source Integration

Where to get raw data and how.

###Pipeline Execution

Short technical description (detailed description is in [[META-pipe design document]]).

###Stallo Implementation

###cPouta Implementation

###MGP/Embassy Cloud Integration

###Data Storage Infrastruture Integration

###Backup, versioning, and repplication

Master replica in Tromsø.

Programming Interface
------------------------

Evaluation
----------

META-pipe vs. MOCAT

References
----------

MicroD3 standard

NCBI 

Future Directions

TaraOcean have made gene catalog. MOCAT pipeline.

Human gut microbiome gene catalog

MetaRef: pan-genomic

OSD