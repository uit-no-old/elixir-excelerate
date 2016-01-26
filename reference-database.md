#Marine Refreence Database Design

##Summary

This design document describes the marine reference database to be developed in the Elixir Excelerate project (described in detila in the [[Background chapter in META-pipe]]). The reference database is closely integrated with the [[META-pipe pipeline]], the EBI Metagenomics Portal, and the Elixir technical services.

## Dependencies

Standards:
-Metagenomic data standards environment (developed as part of Elixir Excelerate marine use case).

Databases:
-ENA: for retrieving annotated genomes, and metagenomics raw sequence reads.
-EBI MGP: ?

Pipelines:
-META-pipe: to assemble ENA metagenomes.

Other infrastructure projects:
-MIRRI

Elixir services:
-Storage: backup, versioning
-Transfer: synchronize databases at EBI and NO
-Cloud: computation resources for assembly and other computation required to populate database

## Deliverables

-Database design and requiremetns by 07.03.2016
-Beta-version by Christmas 2016

## Open Issues

Critical issues:
-ENA public data access
-Computational resources and cost required to assemble metagenomes
-Dependency on Elixir services for backup, replication, and versioning

See also the [[META-pipe issue tracker]].

