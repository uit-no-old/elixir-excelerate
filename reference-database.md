#Marine Refreence Database Design

##Summary

This design document describes the marine reference database developed in the Elixir Excelerate project (described in detail in the [[Background chapter in META-pipe]]). The reference database is closely integrated with the [[META-pipe pipeline]], the EBI Metagenomics Portal, and the Elixir technical services.

## Dependencies

Standards:
* Metagenomic data standards environment (developed as part of Elixir Excelerate marine use case).

Databases:
* Eurpeoan Nucelitide Archive: for retrieving annotated genomes, and metagenomics raw sequence reads.

Pipelines:
* META-pipe: to assemble ENA metagenomes.

Other infrastructure projects:
* MIRRI

Elixir services:
* Storage: backup, versioning
* Transfer: synchronize databases at EBI and NO
* Cloud: computation resources for assembly and other computation required to populate database

## Deliverables

* Database design and requiremetns (**this document!**) by 06.03.2016
* Beta-version by Christmas 2016

## Open Issues

Critical issues:
* ENA private data access
* Computational resources and cost required to assemble metagenomes
* Dependency on Elixir services for backup, replication, and versioning

See also the [[META-pipe issue tracker]].

Undecided:
* Include eukaryots and viurses in gold standard databse?

##Detailed Description

We will establish three different databases with different quality:
* Tier 1 - **MarRef** - is the gold standard and it builds upon complete genomes.
* Tier 2 - **MarDB** - will include all prokaryotic genomes independet of whether they are complete or not.
* Tier 3 - **MetaMar** will be based upon annotation of assembled marine metagenomics and metatransciptomics reads.

###Operaional Scenario

The two main operations for the database is update and lookup. The updates differ for each tier, but we provide a common interface for lookup.

MarRef (Tier 1) is updated when RefSeq ... is updated:
1. We automatically detect updated by ...
2. Then ...

MarDB (Tier 2) is updated when ...:
1. ...
2. ...

MetaMar is updated when a new marine public dataset becomes available:
1. We detect new datasets, by periodically queryingg ENA for marine datasets (META-pipe and EBI MG datasets are also uploaded to ENA). In addition, other?
2. If the datasets has not been run processed by META-pipe, it will be processed.
3. The ??? is added to MetaMar.

To query the database, the end-users either use a user interface provided as a web application, or the public API for programmitic access. The users can query the databses for ???

### User Interface

We provide a web based user interface for the database users. There are three type of users, and hence three types of interfaces.

The end-users:

The curators:

Administrators:

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

Future Directions
-----------------

References
----------

MicroD3 standard

NCBI 



TaraOcean have made gene catalog. MOCAT pipeline.

Human gut microbiome gene catalog

MetaRef: pan-genomic

OSD