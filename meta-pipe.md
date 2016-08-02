# META-pipe v.2.0 Runtime System Desgin Document

## Summary

This design document describes the runtime system developed for the META-pipe marine metagenomics data analysis pipeline. The runtime system is generic and other pipelines can easily be implemetned using the runtime system. However, in this document we use for simplicity only META-pipe as an example data analysis pipeline. 

META-pipe is closely integrated with the ELIXIR [marine reference database](https://github.com/elixir-marine/marref/blob/master/reference-database.md) and the [Elixir compute platform](https://drive.google.com/open?id=0ByWHBDVpEowoTm92N0VEUnV2UXc).

## Version History

This design document is based on:
* The architecture of META-pipe 1.0 ([paper](https://udoc.uit.no/prosjekt/sfb/Delte%20dokumenter/papers/Draft_metapipe.docx?Web=1))
* The goals and descriptions in the [ELIXIR Excelerate grant application](documents/ELIXIR-EXCELERATE_PartB_1-3.pdf)
* The description and requirements of our [Marine metagenomics scientific use case](https://docs.google.com/document/d/1gMKFrcbzuN9BSREU1VDnlml-bl6KSOnfyQbJGh20L5s/edit#)

Version 1.0 (deliverable December 2016)

## Terminology

[ELIXIR](http://www.elixir-europe.org/): European infrastructure for life sciences.

[ELIXIR Excelrate](documents/ELIXIR-EXCELERATE_PartB_1-3.pdf): the infrastructure project in Elixir where META-pipe is in a use case.

EMBRIC:

MIRRI:

## Dependencies

ELIXIR platforms and services:
* [ELIXIR AAI](https://docs.google.com/document/d/1CMY1np3GyvPD8LcKvXljXcRO04V2zu3n_Jcg19jgNOw/edit): authentication and authorization infrastructure used to authenticate META-pipe users and authorize access to META-pipe data and services.
* *Compute platform*: the analysis processes can be run on Open Stack clouds that are part of the compute platform.
* **Not known**: resource usage accounting and reporting.
* *Marine metagenomics search tool* (planned work): the output of META-pipe is made available for the marine metagenomics data search tool.

Other platforms:
* [Stallo Supercomputer at UiT](https://www.sigma2.no/content/stallo): the primary platform for executing analysis processed.
* **Not determined**: storage.
* UiT virtual machine servers: virtual machine to host the web application providing the user interface to META-pipe.

Infrastructure systems:
* [Spark](http://spark.apache.org/) version **X.Y**: the analyses are executed using the Spark system for large-scale data processing.
* *Global filesystem*, such as NFS: storage of reference databases and other metadata required by some of the analysis tools in the pipeline.
* **ADD MORE**

Data and metadata:
* *The comprehensive metagenomics standards environment* (work in progress): a standard for the metadata that should be provided with the input for META-pipe and the variables to be provided for META-pipe output data.
* *Verification tools for metagenoics standards environment* (future work): tools for veryfying that META-pipe input and output data meets the standard requirements.
* [EBI Metagenomics](https://www.ebi.ac.uk/metagenomics/) (EMG): A generic metagenomics data analysis pipeline that META-pipe data formats and tools are harmonized with.
* [Marine reference database](https://github.com/elixir-marine/marref/blob/master/reference-database.md): META-pipe will be used to populate the content of the MarCat marine reference database.

Analysis tools:
* Third party tools in META-pipe: these tools should be able to run unmodified. The tools may require accces to data stored in files on a POSIX file system.

## Deliverables

Documents:
* Requirements and technical architecture document by December 2016
* Standardized setup of execution environment on Elixir cloud platform by December 2016

Cloud:
* cPouta execution environment based on OpenStack by December 2016 (protype done)

Backend:
* Spark backend by 15.09.2016? (currently testing)
* Job manager by 15.09.2016? (status?)
* Storage prototype by 15.09.2016 (status?)
* Elixir AAI integration (done, waiting for needed features from AAI)

META-pipe specific:
* Stable META-pipe on Spark backend by 15.09.2016? (currently testing)
* Web interface for META-pipe by December 2016 (first version done)

## Open Issues

Open issues:
* Resource accounting.
* Resource usage payment.
* Security / sandboxing approach for third party tools executed in a pipeline.

On hold:
* Privilegied access to data submitted by META-pipe users in ENA: this requires changes to ENA that will not be done within this project period.

See also the [META-pipe issue tracker](https://sfb-uit.atlassian.net/).

## Detailed Description

### Operational Scenario

There are four operational scenarios for META-pipe: automated batch processing to populate the marine reference database, analysis of user provided data, and interactive exploration of previously processed data.

For automated batch processing the workflow is as follows:
1. A marine dataset is discovered and scheduled to be added to the *marine reference database*.
2. The data is copied and saved in META-pipe storage.
3. A job is created for the dataset analysis.
4. An execution evironment, in the form of cluster or cloud, is selected for the analysis job.
5. The data is transfered to the selected execution environment.
6. The data is processed by the META-pipe analysis pipeline.
7. The results are saved in META-pipe storage.
8. The assembled nucletiods and proteins are added to the reference database.
9. Public results are added to the META-pipe results repository.
10. Public results are harmonized with EMG results, and search engine data structures are updated.

Notes:
* Initially we will manually transfer public marine metagenomic datasets from ENA.
* Initially we will not harmonize results with EMG, and update the search engine data structures.

There are three main classes of users. Biotechnology industry users are primarily interested in functional analyses, to for example find enzymes with a commercial potential. Aqua cultural users are primary interested in taxonomical classification, to for example understand for example diseases in a fish farm.Researchers are interested in using META-pipe to discover new biological knowledge. 

For analysis of user provided data:
1. The user logs into the META-pipe site using Elixir AAI.
2. How is resource usage payment handled??? Is there a difference between academic and commercial users???
3. The user filles in the required meta data and deposits the data to be analyzed into ENA.
4. The user selects META-pipe for data analysis, or the user configures a pipeline with a subset of the META-pipe tools.
5. The data is transfered to META-pipe resource as described in Step 2 above.
6. The data is processed by META-pipe as described in Step 3 above.
7. For public data the assembled nucletiods and proteins are added to the reference database as described in step 4 above. Private results are archived with a publication date at which they will be added to the reference database.
8. Public data is added to the results database as described in Step 5 above, and private data will be added later on the publication date.
9. Public data is harmonized with EMB results as in Step 6 above, and private data will be harmonized on the publication date.
10. The user compares the data to previous results, and does other types of data exploration using the META-pipe GUI.

Notes:
* We do not initially support ENA deposit, nor selection of META-pipe. Instead the data must be submitted through the META-pipe GUI.
* We do not initially allow tools selection. Instead the default META-pipe is run.

META-pipe also supports users that do not submit data for analysis. These can use the META-pipe data exploration GUI without login into the META-pipe site.

### User Interface

The META-pipe user interface is organized into three pages: data submit, pipeline configuration and execution, and result exploration. All are provided by a single web application.

The data submit page enables submission of user provided data for analysis. The page uses the Elixir design and layout, and the content and look is harmonized with the data submission page of EMG ().

The pipeline configuration and submission enables the user to set parameters in META-pipe tools, and to select tools from other pipelines (not supported initially). 

The results exploration is shown either when a META-pipe execution is completed, or when a user explores the META-pipe results data. The user can do the following:

### Architecture

![META-pipe architecture](img/meta-pipe-architecture.png)

The architecture of the META-pipe backend is designed for scalable processing and production deployment. It consists of the following components. A web application provides the GUI for end users. It uses a REST interface that also provides a programmatic interface. An AAI server integrates with Elixir and Norwegian AAI services. It is implemented to use the SAML2.0 and WebSSO protocols. Storage for input, output, and intermediate data is provided by an object store. It is implemented using the Hadoop Distributed File System. The Job Service enables execution of jobs implemented using Spark. Finally, the execution environment provides a cluster or cloud specific execution manager for easy deployment of the META-pipe tools.

### Performance and scalability considerations

META-pipe will be used to generate data to the marine reference gene catalogue (MarCat). We have the following performance requirements:

1. The tools and the parallel pipeline implementation must  scale to the biggest available marine datasets, which currently is the ???TB TaraOcean dataset. 
2. Initially we will use META-pipe to analyse all publicly available marine prokaryotic datasets. In February 2016 these are about 40TB (uncompressed). We expect the data to double every year, hence META-pipe should scale be used to process  at least 600TB. 
3. The pipeline should also efficiently utilize the available compute and storage resources and hence have a parallel efficiency of at least 80%, and compress the data at least 10x.
4. For a user the analysis time should be short. Jobs should therefore start immediately and several concurrent users should be supported. This requires elasticity in resource allocation.
5. The analysis time and resource usage should be predictable.
6. The cost of the processing should be less than ???
7. More ???

### Other requirements

META-pipe has the following non-performance requirements:

* It should adhere to the data and meta-data standards.
* It should provide and maintain provenance data for experiment repeatability.
* Raw data and results should only be visible to users who have been given access.
* Data quality ???
* More ???

### Web GUI and REST API

???

### Authentication service

The design and implementation is described in detail in the [The META-pipe Authorization service design dcoument](authorization-server.md).

The Authentication service mechanisms limit the users that are authorized to access and modify META-pipe datasets and job results. We have integrated our service with Elixir AAI and the Norwegian Feide AAI. In addition, we provide ad-hoc authentication non-Elixir users.

The authentication service design is simple and developer friendly such that we can easily incorporate it in any framework/programming language (including Scala, Go and Java). It is implemented as an external application that is completely decoupled from the rest of the META-pipe services. The applications contains all the integration code needed to integrate with external authentication services (such as Elixir AAI and Feide) and it can developed independently an at its own pace. We designed the knowledge required by applications to be 
mall enough that the integration could be implemented from scratch with minimal efforts. We also follow relevant standards
since this means that we can re-use existing libraries and use proven, stable interfaces.

### Pipeline abstraction

META-pipe is implemented in Spark.

Describe Jarl’s work

### Job service backend

Spark based

### Data Storage

Object store implemented in Go.

HDFS.

How these can be used from Spark.

Main data structures:
* Reference database.
* Results database
* Search engine data structures.

### Execution manager and deployment

Stallo Integration

cPouta Integration

### Extensibility

New tools

New data 

New organisms
...
### EMG interoperability

## Programming Interface(s)

### REST API

### Pipeline

### Storage

### Execution manager


## Evaluation

Analysis result quality and usefulness.

Pipeline tool scalability.

Pipeline backend scalability.

Stalls resource utilisation. 

cPouta resource utilization.

Job completion times.

## References

ELIXIR Excelerate documents:
*   [all](https://drive.google.com/drive/folders/0B7btK9HAXhx1flN3ek9uV05hTUVsb2ZMX1VDTVZLaFRQLWduOXYtZHZ3QVNJc0RfWU1jSUU)
*   [Marine metagenomics scientific use case](https://drive.google.com/drive/folders/0B4WQQq4hwmbQfjNSYl9zdkpTeDFqV2c2dW5QVk5HVk56NlZGaXlacGluZFJIdGpWLXJWYWs)
*   [Technical services work package](https://drive.google.com/drive/folders/0B4WQQq4hwmbQfkUtdUJzU2JScTVTcU92aFFhcEhTWG82eFFMMmxlZVFZQ2xaME1jNy1uQTQ)

## Future Directions

##  OLD STUFF

### Task 6.3
The sequencing machine availabe at NorStruct at the time of writing is an Illumina MiSeq.
This machien produces, roughly, 20 GB of data per run and one run takes approximately one week.
Assuming 50 runs a year, his put the initial requirement at 1 TB of storage per year.
The processing of data, through META-pipe or another metagenomic pipeline, will produce 20 times the amount of initial data.
The data produced needs to be kept for further reference in accordance to Task 6.1 placing the sotrage requirement around 20 TB.

In addition to that, when META-pipe becomes an ELIXIR named service, it will get more users and the sotorage requirement will need to scale accordingly.
The term of paragon we use is MGP, the EBI metagenomic pipeline.
Given that META-pipe will be able to scale to the biggest datasets availabe (i.e. tara ochean) the storage and data transfer capability of MGP are a good estimate of the requirements of META-pipe.

Assuming the same amount of data per user, as per the estimate of the NorStruct usage, a total of 10 users will require 200 TB.

A greater infaltion of storage requirement can happen in case of a more recent or more efficient sequencing machine where the output data can be up to 10 time more per single run and each run can take as little as two days.


## EVEN OLDER STUFF

Action Points:

1. Main goal: integrate storage with the MetaPipe (or any other pipeline).

3. Figure out how MetaPipe (or any other pipeline) can use the Storage. 
	- User raw data.
	- MetaPipe provenance and intermediate data. Maybe some meta data on the pipeline runs.
	- MetaPipe  results (to the archive).
	- Reference database (task 6.2).

4. Figure out if we can just pull data to NorStore, EUDAT links: Geant.
	As per task 6.2 actions.


## 
## Overview

3 years timeline. (Sept. 2018)

#### Notes on 6.1
Maintain the meta-data of MetaPipe runs along with provenance/linage of data involved in the analysis.
Each time MetaPipe runs an analysis all the information to grant the reproducibility of the analysis must be collected and stored automatically.
The set of information include the MetaPipe version and the provenance of the dataset object of the analysis.
Candidate for the storage is NorStore/EUDAT.

#### Notes on 6.2
As specified in the [Work Package 6 Description](https://docs.google.com/document/d/1_CHt8vW-50bxL4X4hzHxqThURzmo7Tz7KfKQozSTPnQ/edit#) document, this task is to provide a metagenomic marine microbial database.
The database must be publicly available
1. Replicate the marine reference database from Tromsø to EBI.
	- Public information.
	- Content/Size: ? 
	- Schema: we should be ENA compliant (which is NCBI compliant in the data structure/annotation), but we want to be more flexible in our schema.
	- Start with the Prokaryotic bacteria, they ar connected to the MIRRI infrastructure (3K genomes could be astart).
	- Follow up with the eukaryotic adn viruses later.
	- There is standard to be followed: MIGS, to store the genmes.
	- We should have a manually cureted index of the database as a gold standard.
	- Figure out what the database should be.
	- And add a database for the protein sequences and peptides.
	- Gap analysis of the taxonomic tree in the database, MIRRI can help.

2. Check if ENA (NorStore) can notify updates to pull new data automatically.
	- In alternative scan ENA (NorStore) regularly for new data. Automatically?
	- Public information.
	- Content/Size: ?
	- Keep copies of the releases of the data and allow MetaPipe to run on each release.

3. API for programmatic access (pipeline) and WEB search (human).

4. The storage should be provided by NorStore.
	- Can they do it?

#### Notes on 6.3
1. Copy private data from ENA (NorStore) to MetaPipe (UiT or elsewhere)

2. Scale to the biggest dataset available in ENA, Tara Ocean or OCD.
	- Figure out how long can it take to process both on Stallo.

3. MetaPipe needs to access private data on behalf of the user, hence AAI.

4. Compare/combine/integrate/interrogate new results with old results from both MGP and MetaPipe or maybe others.

5. Figure out if/how the two pipeline can be interconnected. Output from a pipeline can be input of the other. Or even interconnection among stages of the pipelines.

# Background
Metagenomics has the potential to provide unprecedented insight into the structure and function of heterogeneous communities of microorganisms and their vast biodiversity.
Microbial communities affect human and animal health and are critical components of all terrestrial and aquatic ecosystems.
They can be exploited e.g. to identify novel biocatalysts for production of fuels or chemicals (bioprospecting), make functional feed for aquaculture species, and for environmental monitoring.
However, in order to expand the potential further for the research community and biotech industry, especially within the marine domain, the metagenomics methodologies need to overcome a number of challenges related to standardization, development of relevant databases and bioinformatics tools.

New and emerging sequencing technologies, integration of metadata gives an extra burden to the development of future databases and tools.
The Use Case *"Marine metagenomic infrastructure as driver for research and industrial innovation"* will contribute to the overall objectives of the ELIXIR-EXCELERATE project by developing research infrastructure and service provision specific for the marine domain in order to enable metagenomic approaches responding to societal and industrial needs.
The outcome of the proposed Use Case will meet the major needs expresses by the marine domain (e.g. ESF Marine board Position Paper 17 *"Marine Microbial Diversity and its role in Ecosystem Functioning and Environmental Change"* and Position Paper 15 *"Marine Biotechnology: A New Vision and Strategy for Europe"*).


## The ELIXIR EXCELERATE Scientific Use Cases
Marine metagenomic infrastructure provides a web based portal that will act as a driver for research and industrial innovation.
The portal will be populated with data produced by running pipelines (including curated Galaxy workflows) on internal HTC clusters using data generated from within the collaboration, but also collected from other data sources. These pipelines will be ported from the current cluster to the future cloud infrastructure.

![ Scientific Use Case](UseCase.jpeg)

## Tasks

### Task 6.1: Development and implementation of a comprehensive metagenomics data standards environment for the marine domain

To maximize the impact and long term utility and discoverability of metagenomics datasets, it is essential for the experimental methods and data acquisition/storage protocols to be established.
In Task 6.1, we will bring together a comprehensive metagenomics data standards environment in collaboration with marine experimental scientists, data providers, end users and the existing communities involved in marine standards development.

The environment will bring together three components:

1. **Data format conventions and standards** will address the various data types for which sharing is required, that will include contextual data (e.g. sample information, expedition-related data), metadata (e.g. provenance and tracking information, descriptions of experimental configurations and bioinformatics tools in use) and data (e.g. raw sequence data, aligned reads, taxonomic identifications, gene calls).

2. **Reporting standards** will address community-accepted thresholds for richness/precision that are required to make data useful, including depth of raw machine data, such as resolution of sequence quality scoring, conventions for references to reference assemblies and minimal reporting requirements for contextual data.

3. **Validation tools** will address the automated validation of compliance with conventions and standards and the meeting of minimal reporting expectations for given datasets in preparation by the marine research community.

In this task we will bring together components that exist already; in particular the contextual data and metadata reporting standards we have developed under the Micro B3 project (EU FP7), data standards and conventions developed around our European Nucleotide Archive (ENA) programme, such as CRAM, FASTQ conventions, work existing in the biodiversity and molecular ecology domains (such as tabular data conventions and BIOM matrices) and construct new components as required.
The major output of this work will be a set of well described and navigable elements to aid the marine community in the preparation, sharing, dissemination and publication of highly interoperable and comprehensive metagenomics datasets.

### Task 6.2. Establishment of marine specific data resources

Due to the data biases of existing reference databases, only about one quarter of sequences are annotated, and this fraction diminishes further when more diverse samples such as soil and marine are analysed.
To improve the characterisation of marine metagenomic samples, this task involves the construction of sustainable public data resources for the marine microbial domain.

Task 6.2 will be achieved by establishing marine microbial databases including reference genomes, nucleotide and protein databases.
The established databases, based on the standards developed in Task 6.1, will enhance the precision and accuracy of biodiversity and function analysis.
The reference databases will be non-redundant datasets generated from sequences acquired from ENA (as part of the International Nucleotide Sequence Database Collaboration), UniProt and other publicly available datasets.
In particular, we will use some of the higher-coverage and higher quality sequence outputs from the TaraOceans and Ocean Sampling Day metagenomic projects, to build high quality marine specific reference databases.

All datasets will be checked with respect to quality, consistency, and interoperability, and in compliance with standards developed in Task 6.1.
The respective knowledge-enhanced databases will be the cornerstone for sustainable analysis of marine metagenomics sequence data.
The databases will be developed in collaboration with members of the ESFRI infrastructures EMBRC and MIRRI and made publicly available through ELIXIR.

### Task 6.3: Gold-standards for metagenomics analysis
The majority of existing metagenomics analysis platforms, while providing insights into the prokaryotic taxonomic diversity and functional potential for individual samples, lack the tools that enable discoverability across samples and industrial innovation.
This task will focus on the evaluation and implementation of new tools and pipelines in order to accelerate research, discoverability and innovation, reducing time to market for new products.

In combination with new standards and databases developed in Task 6.1 and Task 6.2, respectively, new tools for community structure (microbial biodiversity), genetic and functional potential will be evaluated and implemented for environmental applications.
For industrial application tools and pipelines for the identification of gene products (e.g. enzymes and drug targets) and pathways will be implemented and made publicly available.

The evaluation and implementation will be performed in near collaboration with end-users (research groups, environmental centres, biotech companies) to ensure usability for the end user community in order to improve quality, productivity and functionality, as well as reduction of costs for the end-users.
New tools and pipelines will be made publicly available through the e.g. META-pipe (ELIXIR-NO), EBI Metagenomics Portal (EMBL-ELIXIR) and/or EMBL Embassy cloud technology.
Technical requirements will be mapped by WP3 and implemented to meet the requirements of the ELIXIR community.

The continued advancement of sequencing technologies and the growing number of public marine metagenomics projects means that it is becoming increasingly difficult to mine these vast datasets.
In this task, initially a web-based search engine will be developed for the interrogation of marine metagenomics results available from the EBI Metagenomics Portal, based on combinations of queries to our web services (already in existence, or to be built as part of existing projects outside ELIXIR-EXCELERATE) for the discovery of data through metadata, taxonomic and functional fields.
This will extend the back-end search functionality that is to be developed as part of ongoing efforts.
In addition to being downloadable, we will enable search results to flow into an expanded comparison tool (currently limited to gene ontology terms from samples in the same project), to allow more indepth analysis of a user selected datasets, allowing functional and taxonomic comparisons.

In the second phase of this task, the search engine will build upon the data exchange formats in Task 6.1, and federate the search across different pipeline results sets (e.g. META-pipe), so that different results based on the same underlying dataset, can be amalgamated into a single search.
This will dramatically enhance the discoverability across different marine datasets, allowing the identification of common trends and/or differences.
These tools will be developed using user-experience testing and in collaboration with end users to ensure they are fit for purpose.

### Task 6.4: Training workshops for end users
In this task training workshops will be established, in collaboration with WP11 “ELIXIR Training Programme”, for end-users with the aim to facilitate accessibility, by training European researchers and industry to more effectively exploit the data, tools and pipelines, and compute infrastructure provided by the ELIXIR marine metagenomics infrastructure. These training workshops and materials will be converted to online training resources, extending the reach of the workshop.