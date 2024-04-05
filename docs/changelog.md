# v0.3.1: 04/05/24
## Changes
- Add output prefix option (takes directory or directory + prefix name)
- Deprecate `outdir` (maintain backwards compatibility until v1.0)
- Fix bug in call-reads where a read with partial exon2 (only) coverage blows up candidate pool  
- Catch error from aligned inputs with wrong reference

# v0.3.0: 03/21/24 
## Changes
- New tool `call-reads` to call from HiFi reads (limited to class I)
- Extend reports for read-based calls
- Update IPD-IMGT/HLA to version 3.55 (2024-01)
- Add option to limit calls to genomic (full-length) records
- Update README

# v0.2.3: 12/22/23
## Changes
- Fix reporting bug for cdna calls
- Add option to require exon 2 to make a call
- Improved thread control and error reporting

# v0.2.2: 11/17/23
## Changes
- Update database to IPD-IMGT/HLA Version: 3.54 (2023-10)

# v0.2.1: 11/15/23
## Changes
- Call-contigs hap2 change to optional
- Improve output determinism
- Update README 
- Fix validation bugs

# v0.2.0: 9/25/23
## Changes
- Update README for initial release
- Latest database version v3.53
- Bug fixes

# v0.1.0: 5/31/23
## Changes
- Report results in tsv and json format
