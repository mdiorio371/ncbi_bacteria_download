# NCBI Bacteria Download

These scripts are used to download the latest non-redundant set of complete genomes from NCBI including Refseq and Genbank. The R script can be used to either download all complete sequences, or only the representative complete sequences. These sequences can be downloaded to a single file, or piped directly into a blast database. Operations using only the representative database will be much faster and less memory intensive than the full database.

### Usage
Run the Rscript to generate a text file with the links to each of the fasta files. The output will be database/all_seqs.txt or database/representative_seqs.txt

```
## To download accessions from the complete database
Rscript ncbi_bacteria_ftps.R \
  all \
  database
## To only download the representative sequences
Rscript ncbi_bacteria_ftps.R \
  representative \
  database
```
Each file can be downloaded in parallel
```
## I used gnu parallel https://www.gnu.org/software/parallel/
# for the complete database
mkdir -p database/all_seqs/fna
parallel --progress --bar -j 8 --gnu wget -q -P database/all_seqs/fna < database/all_seqs.txt
# for the representative database
mkdir -p database/representative_seqs/fna
parallel --progress --bar -j 8 --gnu wget -q -P database/representative_seqs/fna < database/representative_seqs.txt
```

To unzip each file and put them directly into a local blast database:
```
## for the complete database
mkdir -p database/all_seqs/blastdb
find database/all_seqs/fna/ -type f -name "*.gz" -print0 | xargs -0 gunzip -c | \
  makeblastdb -in - -out database/all_seqs/blastdb/all_seqs_db -title all_seqs \
  -dbtype nucl -parse_seqids

## for the representative database
mkdir -p database/representative_seqs/blastdb
find database/representative_seqs/fna/ -type f -name "*.gz" -print0 | xargs -0 gunzip -c | \
  makeblastdb -in - -out database/representative_seqs/blastdb/representative_seqs_db -title all_seqs \
  -dbtype nucl -parse_seqids
```
  


