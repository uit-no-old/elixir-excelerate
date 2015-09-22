
EliXir Requirements
==================

3 years timeline. (Sept. 2018)

Figure out the numbers: 
	how much storage do we need?
	how much do we need to transfer?

Contact EBI and ask for the numbers, but first figure out what to ask for.
ENA is 2.5 PByte compressed.
But if you cut humans ans mice there may not be much left, and they are not relevant for the marine biology.

Ask Roy for requirements documents to compile.


Task 6.1: 
---------
1. Maintain the meta-data/provenance data of MetaPipe runs properly (NorStore/EUDAT?).


WP6 task 6.2: create a marine metagenomic reference database:
----
1. Replicate the marine reference database from Tromsø to EBI.
	But before we need to pull data from ENA (NorStore) or other data storage sites such as NCBI. 
	Public information.
	Content/Size: ?

2. Check if ENA (NorStore) can notify updates to pull new data automatically.
	In alternative scan ENA (NorStore) regularly for new data.
	Public information.
	Content/Size: ?
	Keep copies of the releases of the data and allow MetaPipe to run on each release.

	
3. API for programmatic access (pipeline) and WEB search (human).

4. The storage should be provided by NorStore.
	Can they do it?

Task 6.3: gold standard for pipelines:
--------------------------------------
1. Copy private data from ENA (NorStore) to MetaPipe (UiT or elsewhere)

2. Scale to the biggest dataset available in ENA, Tara Ocean.

3. MetaPipe needs to access private data on behalf of the user, hence AAI.

4. Compare/combine/integrate/interrogate new results with old results from both MGP and MetaPipe or maybe others.


Action Points.
--------------

1. Main goal: integrate NorStore with the MetaPipe (or any other pipeline).

2. Get numbers from EBI: 
	how much of ENA is marine?
	how can we figure it out ourselves?

3. Figure out how MetaPipe (or any other pipeline) can use NorStore. 
	- User raw data.
	- MetaPipe provenance and intermediate data (for example the SHA1 of the idividual files).
	- MetaPipe  results (to the archive).
	- Reference database (task 6.2).

4. Figure out if we can just pull data to NorStore, EUDAT links: Geant.
	As per task 6.2 actions.







