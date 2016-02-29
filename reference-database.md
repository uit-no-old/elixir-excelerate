#Marine Refreence Database Design

##Summary

This design document describes the marine reference database developed in the Elixir Excelerate project (described in detail in the [[Background chapter in META-pipe]]). The reference database is closely integrated with the [[META-pipe pipeline]], the EBI Metagenomics Portal, and the Elixir technical services.

## Dependencies

Standards:
* Metagenomic data standards environment (developed as part of Elixir Excelerate marine use case).
* NCBI defintions for reference genomes, representative genomes, and variant genomes.

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
* Tier 1 - **MarRef** - is the gold standard and it builds upon complete genomes. Details about the content...
* Tier 2 - **MarDB** - will include all prokaryotic genomes independet of whether they are complete or not. Details about the content...
* Tier 3 - **MetaMar** will be based upon annotation of assembled marine metagenomics and metatransciptomics reads. Details about the content...

###Operaional Scenario

The two main operations for the database is update and lookup. The updates differ for each tier, but we provide a common interface for lookup.

MarRef (Tier 1) is updated when RefSeq ... is updated. There will be a new version each...
1. We automatically detect updated by ...
2. Then ...

MarDB (Tier 2) is updated when ... There will be a new version each...:
1. ...
2. ...

MetaMar is updated when a new marine public dataset becomes available. There will be a new version each...:
1. We detect new datasets, by periodically queryingg ENA for marine datasets (META-pipe and EBI MG datasets are also uploaded to ENA). In addition, other?
2. If the datasets has not been run processed by META-pipe, it will be processed. Hence, META-pipe must scale to dataset sizes, and produce results wit the highest possibility quality. 
3. The ??? is added to MetaMar.

To query the database, the end-users either use a user interface provided as a web application, or the public API for programmitic access. The users can query the databses for ??? 

The databases are also used as input for analysis pipelines?

### User Interface

We provide a web based user interface for the database users. There are three type of users, and hence three types of interfaces.

**End-users** query the database using a web based interface ???

**Curators** populate the database by ???

**Administrators** ???

### Architecture

Storage system. Version support. Ensure standards are followed.

Data format:
* Useful in BLAST

Object storage?

###MarRef

(figure for Tier1, based on NCBI pipeline)

MarRef is the marine **gold standard** database. It contains complete genomes, and provides reference nucleotide and proteins. Initially there will be about 1000-2000 genomes of which 500 are complete. Hence, the database size will be ???. The data will be added through manual curation (just for starting? or will it be automated?). We therefore expect it to grow with ??? and have a size of ??? after four years.

Pipeline.

Storage system. Details about data format ???

###MarDB

(figure for Tier 2)

MarDB is the marine genome database. It contains... Genome sequence datbaase (ENA), Ensembl bacteria (30K), NCBI microbial genomes resources (58.000).

No pipeline.

Storage system. Details about data format ???

###MetaMar

(figure for Tier 3)

MetaMar is the marine gene catalog. It contains the nucleotides and proteins output by META-pipe for marine metagenomics reads and marine metatrancsiptome data processed. It also contains the MarRef and MarDb data. 

It will contain millions of genes. Intially, we will add 1XYZ public marine prokaryotic datasets downlaoded from ENA (XYTB uncompressed size). As more data becomes publically avaialble it will be processed by META-pipe and added to MetaMar. We therefore expect the size to grow by XYZ each year, and the database size to reach XYZTB after 4 years.

###Performance and Scalability Considerations

The databases are queried by end-users interactively. Hence they should support query times less than 5 seconds.

The databses are also used as input for pipelines. For these the throughput should be???

Database update times should be ???. Data should be insterted without blocking interactive users. Versioning should also be done without blocking the database. It should be possible to backup/ replicate the data at ??? 

Initially the database size is 40TB (uncompressed). But the data size may double every year, hence the database architecture should sclae to at least 600TB. Most of this data will be in MetaMar (Tier 3), while MarRef and MarDB are expected to be less than 1TB in size.

(Experinces from MOCAT pipeline.)

###Data Source Integration

MarRef

MarDB

For MetaMar will get raw data from ENA, META-pipe, EBI MG, ...

Giacomo: how do we know what to get, what we already have, and when new data is available? Also which interfaces do we use to access the data?

###Pipelines Execution

MetaMar processes the raw metagenome and metatranscroptomics data through META-pipe as follows:
1. Foo
2. Bar ...

###Stallo Implementation

Giacomo how can we implement the databases on Stallo?

###cPouta Implementation

How can we implement the databases on cPouta and other cloud resources?

###Data Storage Infrastruture Integration

What do we need NorStore and EUDAT for? How can we integrate with these services? What accounts etc do we need?

###Backup, versioning, and replication

How do we backup the databases?

How do we maintain database versions?

There will severak replicas of the databases. The master replica will be in Tromsø. It will automatically be replicated through ELIXIR (which service?) ...

##Programming Interface

###Updates

###Queries

###Pipeline access

###Replication, backup, etc

##Evaluation

META-pipe vs. MOCAT

Other?

##Future Directions


##References

MicroD3 standard

NCBI 

TaraOcean have made gene catalog. MOCAT pipeline.

Human gut microbiome gene catalog

MetaRef: pan-genomic

OSD