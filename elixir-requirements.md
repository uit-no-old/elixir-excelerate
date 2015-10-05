META-pipe Elixir Excelerate
==================

Notes
-----

Figure out the numbers: 
	how much storage do we need?
	how much do we need to transfer?

Contact EBI and ask for the numbers, but first figure out what to ask for.
ENA is 2.5 PByte compressed.
But if you cut humans ans mice there may not be much left, and they are not relevant for the marine biology.

Ask Roy for requirements documents to compile.

Version History
---------------

Terminology
-----------

[ELIXIR](http://www.elixir-europe.org/): European infrastructure for life sciences.

[ELIXIR Excelrate](documents/ELIXIR-EXCELERATE_PartB_1-3.pdf): the infrastructure project in Elixir where META-pipe is in a use case.

EMBRIC:

MIRRI:

Overview
--------

3 years timeline. (Sept. 2018)

Requirements and Limitations
----------------------------

Task 6.1: 
1. Maintain the meta-data/provenance data of MetaPipe runs properly (NorStore/EUDAT?).
    Keep some metadata on the runs of MetaPipe.


WP6 task 6.2: create a marine metagenomic reference database:
1. Replicate the marine reference database from Tromsø to EBI.
	- But before we need to pull data from ENA (NorStore) or other data storage sites such as NCBI. 
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

Task 6.3: gold standard for pipelines:
1. Copy private data from ENA (NorStore) to MetaPipe (UiT or elsewhere)

2. Scale to the biggest dataset available in ENA, Tara Ocean or OCD.
	- Figure out how long can it take to process both on Stallo.

3. MetaPipe needs to access private data on behalf of the user, hence AAI.

4. Compare/combine/integrate/interrogate new results with old results from both MGP and MetaPipe or maybe others.

5. Figure out if/how the two pipeline can be interconnected. Output from a pipeline can be input of the other. Or even interconnection among stages of the pipelines.

Goals
-----

*   META-pipe should become ELIXIR Named Service.
    *   For now, marine reference database should be part of the Named Service.


Dependencies
------------

Deliverables
------------

Open Issues
-----------

Detailed Description
--------------------

Operational Scenario

User Interface

Architecture

Performance Considerations 

Data Sources and Structure 

Processing Pipeline

Data Storage

Workflow Manager

Stallo Integration

...


Programming Interface(s)
------------------------

Evaluation
----------

References
----------

Future Directions
-----------------

Old stuff
---------

Action Points:

1. Main goal: integrate storage with the MetaPipe (or any other pipeline).

2. Get numbers from EBI: 
	- How much of ENA is marine?
	- How can we figure it out ourselves?
	- Ask Alex Mitchell or one of the Rob's people.

3. Figure out how MetaPipe (or any other pipeline) can use the Storage. 
	- User raw data.
	- MetaPipe provenance and intermediate data. Maybe some meta data on the pipeline runs.
	- MetaPipe  results (to the archive).
	- Reference database (task 6.2).

4. Figure out if we can just pull data to NorStore, EUDAT links: Geant.
	As per task 6.2 actions.