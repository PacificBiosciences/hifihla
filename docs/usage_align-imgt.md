### Align queries to specific alleles in IPD-IMGT/HLA
`align-imgt` is meant to check differences between alleles in the database and any query sequence (including other database accessions).  The output is json format.
```
Align queries to IMGT/HLA genomic accession sequences

Usage: hifihla align-imgt [OPTIONS]

Options:
  -f, --fasta <FASTA>           Fasta with query sequence(s)
  -q, --qids [<QIDS>...]        Comma-sep query IDs
  -t, --targets [<TARGETS>...]  Comma-sep target IDs (map refs)
  -n, --tnames [<TNAMES>...]    Comma-sep target Names (map refs)
  -e, --exact                   Exact target name matches only (default starts-with)
  -j, --threads <THREADS>       Analysis threads [default: 1]
  -v, --verbose...              Enable verbose output
      --log-level <LOG_LEVEL>   Alternative to repeated -v/--verbose: set log level via key.
                                Equivalence to -v/--verbose:
                                      => "Warn"
                                   -v => "Info"
                                  -vv => "Debug"
                                 -vvv => "Trace" [default: Warn]
  -h, --help                    Print help
  -V, --version                 Print version
```

### Examples
Compare query to specific alleles by name or accession number
```
hifihla align-imgt \
        --fasta HG001_HLA_A_11_01_01_01.fasta \
        -n HLA-A*11:01:01:03 \
        -t HLA00043
{
  "id": "Query HLA-A_11-01-01-01; Targets HLA00043,HLA15502",
  "hla_alignments": {
    "HLA-A_11-01-01-01": {
      "HLA00043": {
        "allele_id": "HLA00043",
        "star_name": "HLA-A*11:01:01:01",
        "length": 3503,
        "match_name": "*11:01:01:01",
        "query_start": 162,
        "query_end": 3301,
        "covered_feat": [
          "UTR_5",
          "Exon_1",
          "Intron_1",
          "Exon_2",
          "Intron_2",
          "Exon_3",
          "Intron_3",
          "Exon_4",
          "Intron_4",
          "Exon_5",
          "Intron_5",
          "Exon_6",
          "Intron_6",
          "Exon_7",
          "Intron_7",
          "Exon_8",
          "UTR_3"
        ],
        "not_covered": [],
        "coding_diffs": 0,
        "noncode_eddist": 0,
        "error_rate": null,
        "coverage": 1,
        "reads": null,
        "differences": []
      },
      "HLA15502": {
        "allele_id": "HLA15502",
        "star_name": "HLA-A*11:01:01:03",
        "length": 3503,
        "match_name": "*11:01:01",
        "query_start": 162,
        "query_end": 3301,
        "covered_feat": [
          "UTR_5",
          "Exon_1",
          "Intron_1",
          "Exon_2",
          "Intron_2",
          "Exon_3",
          "Intron_3",
          "Exon_4",
          "Intron_4",
          "Exon_5",
          "Intron_5",
          "Exon_6",
          "Intron_6",
          "Exon_7",
          "Intron_7",
          "Exon_8",
          "UTR_3"
        ],
        "not_covered": [],
        "coding_diffs": 0,
        "noncode_eddist": 1,
        "error_rate": null,
        "coverage": 1,
        "reads": null,
        "differences": [
          {
            "kind": "Mismatch",
            "pos": 849,
            "size": 1,
            "feat": "Intron_2"
          }
        ]
      }
    }
  }
}
```
Compare two IMGT alleles by accession ID:
```
hifihla align-imgt -q HLA15502 -t HLA00043

{
  "id": "Query HLA15502; Targets HLA00043",
  "hla_alignments": {
    "HLA15502": {
      "HLA00043": {
        "allele_id": "HLA00043",
        "star_name": "HLA-A*11:01:01:01",
        "length": 3503,
        "match_name": "*11:01:01",
        "query_start": 0,
        "query_end": 3503,
        "covered_feat": [
          "UTR_5",
          "Exon_1",
          "Intron_1",
          "Exon_2",
          "Intron_2",
          "Exon_3",
          "Intron_3",
          "Exon_4",
          "Intron_4",
          "Exon_5",
          "Intron_5",
          "Exon_6",
          "Intron_6",
          "Exon_7",
          "Intron_7",
          "Exon_8",
          "UTR_3"
        ],
        "not_covered": [],
        "coding_diffs": 0,
        "noncode_eddist": 1,
        "error_rate": null,
        "coverage": 1,
        "reads": null,
        "differences": [
          {
            "kind": "Mismatch",
            "pos": 849,
            "size": 1,
            "feat": "Intron_2"
          }
        ]
      }
    }
  }
}
```
