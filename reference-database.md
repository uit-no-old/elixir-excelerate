#Marine Refreence Database Design

##Summary

This design document describes the marine reference database developed in the Elixir Excelerate project (described in detail in the [[Background chapter in META-pipe]]). The reference database is closely integrated with the [[META-pipe pipeline]], the EBI Metagenomics Portal, and the Elixir technical services.

## Terminology

###NCBI defintions

* **Reference genomes**: manually selected “gold standard” complete genomes with high quality annotation and the highest level of experimental support for structural and functional annotation. * **Representative genomes**: representative genome for an organism (species); for some diverse species there can be more than one. For example, pathogenic and non-pathogenic E. coli will each be assigned a reference. 
* **Variant genomes**: all other genomes from individual samples representing genome variations within the species. ###Other definitions

* **Gene catalog**:...
* **ENA entry**:...

## Dependencies

###Standards:

* Metagenomic data standards environment (developed as part of Elixir Excelerate marine use case).
* NCBI defintions for reference genomes, representative genomes, and variant genomes.

###Databases:
* Eurpeoan Nucelitide Archive: for retrieving annotated genomes, and metagenomics raw sequence reads.
* National Center for Biotechnology Information: for access to biomedical and genomic information.(?)

###Pipelines:
* META-pipe: to assemble ENA metagenomes.

###Other infrastructure projects:
* MIRRI

###Services (EliXir and others):
* Storage: backup, versioning
* Transfer: synchronize databases at EBI and NO
* Cloud: computation resources for assembly and other computation required to populate database
* AAI


## Deliverables 

* Database design and requiremetns (**this document!**) by 06.03.2016
* Beta-version by Christmas 2016

## Open Issues

###Critical:

* ENA private data access
* Computational resources and cost required to assemble metagenomes
* Dependency on Elixir services for backup, replication, and versioning

See also the [META-pipe issue tracker](https://sfb-uit.atlassian.net/).

###Non-critical:

* Decide to include eukaryots and viurses in gold standard databse

##Detailed Description

![Tier 3](img/ncbi-pipeline.jpg)

###We will establish three different databases with different quality:
* Tier 1 - **MarRef** - The Tier1 database will be the Gold Standard and build upon complete marine archaeal and bacterial genomes available in NCBI Prokaryotic RefSeq Genomes database. Manually curated.[Details about the content...]
* Tier 2 - **MarDB** - will include all prokaryotic genomes independet of whether they are complete or not.
Manually curated at the beginning, if possible. Later there will be standards to avoid manual curation.[Details about the content...]
* Tier 3 - **MarCat** will be based upon annotation of assembled marine metagenomics and metatransciptomics reads. [Details about the content...]

###Operational Scenario

The two main operations for the database is update and lookup. The updates differ for each tier, but we provide a common interface for lookup.

The databases are also used as input for analysis pipelines?

To query the database, the end-users either use a user interface provided as a web application, or the public API for programmitic access. The users can query the databses for ??? 

###MarRef (Tier 1) 
![Tier 1](img/tier1.jpg)
MarRef is updated when RefSeq is updated.
It will be manually curated.
New version with each new version of RefSeq.

###MarDB (Tier 2) 
![Tier 2](img/tier2.jpg)

Manually curated so far for lack of proper metadata.
Curation will be automated when metadata is up to standard.

###MetaMar (Tier 3)  
![Tier 3](img/tier3.jpg)

Updated when a new marine public dataset becomes available.

1. We detect new datasets, by periodically queryingg ENA (or others) for marine datasets (META-pipe and EBI MG datasets are also uploaded to ENA).
2. The data is manually curated in order to ...
3. If the datasets has not been run processed by META-pipe, it will be processed. Hence, META-pipe must scale to dataset sizes, and produce results wit the highest possibility quality. 
4. Manual or automatic? data results quality check.
5. Automatic removal of redundant genes. Add references to datasets?
6. The <what output of META-pipe???> is added to MetaMar. 




## User Interface

We provide a web based user interface for the database users. 
There are three type of users, and hence three types of interfaces.

Interface inspiration: NCBI microbial genomics

Renzo has ideas for curator tools.



**End-users** query the database using a web based interface/REST API

**Curators** populate the database by hand

**Administrators** manage configurations/general maintenance

## Architecture



![Tier 3](img/db-arch3.pdf)

Start simple:

- SQL database containing the metadata.
- Spark on top of HDFS to run Meta-pipe.
- gridFTP to tranfer raw reads to be processed by Meta-pipe.
- NorStore for backup (tape).
- WEB GUI, REST API anda CLI tool


Version enforced by timestamps in the RDBMS.
If final data is not too big it can be directly served form the RDBMS, otherwise we have HDFS.

Ensure standards are followed by avaiding submissions without proper metadata.

Data format, BLAST?


###MarRef




MarRef is the marine **gold standard** database. It contains complete genomes, and provides reference nucleotide and proteins. Initially there will be about 1000-2000 genomes of which 500 are complete. Hence, the database size will be ???. The data will be added through manual curation (just for starting? or will it be automated?). We therefore expect it to grow with ??? and have a size of ??? after four years.

Database entries: complete genome assemblies, reference + representatives. Whole chromosomes? Nucliotides () + proteins (linked to uniprot IDs).

Inspiration: RefSeq


Storage system. Details about data format ???

###MarDB


MarDB is the marine genome database. It contains... Genome sequence datbaase (ENA), Ensembl bacteria (30K), NCBI microbial genomes resources (58.000).

No pipeline.

Storage system. Details about data format ???

###MetaMar


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

Giacomo: how do we know what to get, what we already have, and when new data is available? Also which interfaces do we use to access the data? How is ENA/ EMG data structured and what are the dependencies?

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