# Annotate GO terms from BLAST
Annotate sequences with GO terms using the UniProt-GOA database and results from BLAST against UniProt. 

Annotation is conducted in two steps:
## Step 1: Load GO terms into sqlite database
Download the GOA database which maps UniProt IDs to GO terms. 
```
wget ftp://ftp.ebi.ac.uk/pub/databases/GO/goa/UNIPROT/goa_uniprot_all.gaf.gz
wget ftp://ftp.ebi.ac.uk/pub/databases/GO/goa/current_release_numbers.txt
```
Load UniProt-GOterm mappings into a sqlite database. Will take >60 mins to run and produces a database of ~20GB.
```
gunzip -c test_goa_uniprot_all.gaf.gz | python Load_GOterms_into_SQLite.py --db GOterms_UniProtKB_Mapping.sqlite3
```

## Step 2: Annotate GO terms from blast output.
This step assumes you already have the output from blast against the UniProt database (using -outfmt 6).
```
python Annotate_GO_From_BLAST_topGO_v2.py --blast blastp_UniProt.outfmt6 --out blastp_UniProt.GOterm_annotations --db GOterms_UniProtKB_Mapping.sqlite3
```
This script will annotate a query sequence with GO terms using the hits in the blast output.
For each query sequence the script loads all hits (in the order they appear in the file) into a list and checks the list (from top to bottom) to see if any of the hit targets have GO terms annotated in the GOA database (created in Step 1). If the first hit in the list does not have associated GO terms then the script will check the next hit, and then the next hit, until a hit is found that has associated GO terms or we run out of hits to check (in that case the query will not have any GO term annotated).


