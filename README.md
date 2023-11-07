<h1 align="center"><img width="300px" src="figures/logo_HiFiHLA.svg"/></h1>

<h1 align="center">HiFiHLA</h1>

An HLA star-calling tool for PacBio HiFi data types.  

IPD-IMGT/HLA Version: 3.53 (July 7, 2023)[1]

Authors: [John Harting](https://github.com/jrharting), [Zev Kronenberg](https://github.com/zeeev), [Daniel Baker](https://github.com/dnbaker), [Matt Holt](https://github.com/holtjma)


## Table of Contents ##
1. [Install](#install)
2. [Genes](#genes)
3. [Usage](#usage)
4. [Output](#output)
5. [Examples](#examples)
6. [Changelog](#changelog)
7. [References](#references)

## Install <a name="install"></a>

Please refer to our [official pbbioconda page](https://github.com/PacificBiosciences/pbbioconda) for information on Installation, Support, License, Copyright, and Disclaimer.

## Genes <a name="genes"></a>

Genes called by `hifihla`:
```
HLA-A             HLA-DQA1          HLA-DRB7          HLA-P
HLA-B             HLA-DQA2          HLA-DRB8          HLA-S
HLA-C             HLA-DQB1          HLA-E             HLA-T
HLA-DMA           HLA-DQB2          HLA-F             HLA-U
HLA-DMB           HLA-DRA           HLA-G             HLA-V
HLA-DOA           HLA-DRB1          HLA-H             HLA-W
HLA-DOB           HLA-DRB2          HLA-HFE           HLA-Y
HLA-DPA1          HLA-DRB3          HLA-J             MICA
HLA-DPA2          HLA-DRB4          HLA-K             MICB
HLA-DPB1          HLA-DRB5          HLA-L             TAP1
HLA-DPB2          HLA-DRB6          HLA-N
```

## Usage <a name="usage"></a>

`hifihla` has three subcommands: 
* `call-contigs`
* `call-consensus`
* `align-imgt`

```
Usage: hifihla <COMMAND>

Commands:
  call-contigs    Extract HLA loci from assembled MHC contigs & call star alleles on extracted sequences
  call-consensus  Call HLA Star (*) alleles from consensus sequences
  align-imgt      Align queries to IMGT/HLA genomic accession sequences
  help            Print this message or the help of the given subcommand(s)

Options:
  -h, --help     Print help
  -V, --version  Print version
```

### Type consensus sequences
`call-consensus` accepts a FASTA file of sequences and attempts to find the closest matching allele in the [IMGT/HLA database](https://www.ebi.ac.uk/ipd/imgt/hla/).  It is assumed that each FASTA record is a single locus.
Optionally call star alleles using exon sequence only.
```
Call HLA Star (*) alleles from consensus sequences

Usage: hifihla call-consensus [OPTIONS] --fasta <FASTA> --outdir <OUTDIR>

Options:
  -f, --fasta <FASTA>          Input fasta file of consensus sequences
  -o, --outdir <OUTDIR>        Output directory
  -c, --cdna                   Enable cDNA-only calling
  -j, --threads <THREADS>      Analysis threads [default: 1]
  -x, --max_matches <MATCHES>  Maximum equivalent matches per query in report [default: 10]
  -v, --verbose...             Enable verbose output
      --log-level <LOG_LEVEL>  Alternative to repeated -v/--verbose: set log level via key.
                               Equivalence to -v/--verbose:
                                     => "Warn"
                                  -v => "Info"
                                 -vv => "Debug"
                                -vvv => "Trace" [default: Warn]
  -h, --help                   Print help
  -V, --version                Print version
```


### Type HLA from MHC assembled contigs
`call-contigs` requires three input files and an output directory:
 * Assembled contigs aligned to GRCH38 (no alts)
 * Assembled haplotype fasta[.gz] files (2)  

Options:
 * Define the loci to be extracted and called (see [IMGT genes](https://hla.alleles.org/genes/index.html)) [default all loci]
 * Limit equivalent matches in report -- This is useful to limit reporting of common alleles with shared cDNA.
 * Require assembly alleles to be of a minimum length
```
Extract HLA loci from assembled MHC contigs & call star alleles on extracted sequences

Usage: hifihla call-contigs [OPTIONS] --abam <ALIGNED_ASSEMBLY> --hap1 <HAP1_FA> --hap2 <HAP2_FA> --outdir <OUTDIR>

Options:
  -a, --abam <ALIGNED_ASSEMBLY>  Input assembly aligned to GRCh38
  -p, --hap1 <HAP1_FA>           Input hap1 assembly fa(.gz)
  -m, --hap2 <HAP2_FA>           Input hap2 assembly fa(.gz)
  -o, --outdir <OUTDIR>          Output directory
  -l, --loci [<LOCI>...]         Input comma-sep loci to extract [default: all]
  -s, --min_length <MINLENGTH>   Minimum length of extracted targets [default: 2000]
  -x, --max_matches <MATCHES>    Maximum equivalent matches per query in report [default: 10]
  -j, --threads <THREADS>        Analysis threads [default: 1]
  -v, --verbose...               Enable verbose output
      --log-level <LOG_LEVEL>    Alternative to repeated -v/--verbose: set log level via key.
                                 Equivalence to -v/--verbose:
                                       => "Warn"
                                    -v => "Info"
                                   -vv => "Debug"
                                  -vvv => "Trace" [default: Warn]
  -h, --help                     Print help
  -V, --version                  Print version
```

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

## Output <a name="output"></a>
`call-consensus` and `call-contigs` both generate three reports containing HLA star-allele type calls.  Additionally, `call-contigs` produces fasta files of extracted sequences from the assembly.

| File                                         | Description |
| -------------------------------------------- | ----------- |
| {output_dir}/hifihla_summary.tsv             | Detailed file listing best call for each locus    |
| {output_dir}/hifihla_report.tsv              | Simple tsv file listing calls for each locus      |
| {output_dir}/hifihla_report.json             | Detailed results file, see below for example      |
| {output_dir}/asm.contigs.h[12].fasta         | Extracted (full) assembly contigs aligning to MHC |
| {output_dir}/asm.contigs.h[12].fasta.fai     | FASTA index for contigs                           |
| {output_dir}/extracted.targets.h[12].fasta   | Extracted targets used for star-typing            |

### Detailed summary tsv
This file reports the single best call and statistics for each query sequence in the sample.

| Column Name  | Description                                          |
|--------------|------------------------------------------------------|
| queryId      | Query sequence name                                  |
| qLen         | Length of query sequence                             |
| nMatches     | Number of equivalent matches with same edit distance |
| gType        | Closest matching genomic accession                   |
| gPctId       | Percent matching bases of closest genomic match      |
| gPctCov      | Percent coverage of closest genomic match            |
| gEdit        | Edit distance to closest genomic match               |
| cdnaType     | Closest matching coding sequence                     |
| exCovered    | Exons included in alignment                          |
| exEdit       | Edit distance to cDNA match                          |
| Type         | Final type call                                      |

Final type call is determined by edit distance and exact matches to the query (nMatches).  The final call is four field if the query sequences matches exactly and is the only exact match returned.

```
queryId               qLen  nMatches  gType                 gPctId  gPctCov  gEdit  cdnaType           exCovered        exEdit  Type
HLA-A_01-01-01-01     3140  3         HLA-A*01:01:01:01     100.0   89.60    0      HLA-A*01:01:01     1,2,3,4,5,6,7,8  0       HLA-A*01:01:01
HLA-A_26-01-01        3154  1         HLA-A*26:01:01:01     100.0   89.65    0      HLA-A*26:01:01     1,2,3,4,5,6,7,8  0       HLA-A*26:01:01:01
HLA-B_35-08-01        3401  1         HLA-B*35:08:01:01     99.97   87.10    1      HLA-B*35:08:01     1,2,3,4,5,6,7    0       HLA-B*35:08:01
HLA-B_38-01-01        3386  2         HLA-B*38:01:01:01     100.0   82.13    0      HLA-B*38:01:01     1,2,3,4,5,6,7    0       HLA-B*38:01:01
HLA-C_04-01-01        3427  1         HLA-C*04:01:01:06     100.0   80.30    0      HLA-C*04:01:01     1,2,3,4,5,6,7,8  0       HLA-C*04:01:01:06
HLA-C_12-03-01-01     3427  1         HLA-C*12:03:01:01     100.0   79.57    0      HLA-C*12:03:01     1,2,3,4,5,6,7,8  0       HLA-C*12:03:01:01
HLA-DPB1_04-01-01     5850  7         HLA-DPB1*04:01:01:03  100.0   50.80    0      HLA-DPB1*04:01:01  2,3,4,5          0       HLA-DPB1*04:01:01
HLA-DPB1_04-01-01-01  5866  31        HLA-DPB1*04:01:01:01  100.0   50.87    0      HLA-DPB1*04:01:01  2,3,4,5          0       HLA-DPB1*04:01:01
HLA-DQB1_05-01-01     3745  1         HLA-DQB1*05:01:01:05  99.97   52.79    1      HLA-DQB1*05:01:01  2,3,4            0       HLA-DQB1*05:01:01
HLA-DQB1_03-02-01     3770  11        HLA-DQB1*03:02:01:01  99.97   52.87    1      HLA-DQB1*03:02:01  2,3,4            0       HLA-DQB1*03:02:01
HLA-DRB1_04-02-01     4819  1         HLA-DRB1*04:02:01     99.91   33.16    4      HLA-DRB1*04:02:01  2,3,4            0       HLA-DRB1*04:02:01
HLA-DRB1_10-01-01     3659  2         HLA-DRB1*10:01:01:03  100.0   26.50    0      HLA-DRB1*10:01:01  2,3,4            0       HLA-DRB1*10:01:01
```


### Simple tsv format
This file is formatted for a quick view of top-level results.  It is appropriate as input for downstream interpretation tools like [pharmCAT](https://pharmcat.org/).

```
$ column -t hifihla_report.tsv
HLA-A     *01:01:01:01/*26:01:01:01
HLA-B     *38:01:01:01/*35:08:01:01
HLA-C     *12:03:01:01/*04:01:01:06  
```

### Detailed results json
Detailed results contain information on calls for each sequence typed. The json is structured in the following way:

```
 { sample_id: <sample_id>,
   hla_calls: {
        <locus>: {
                    <Record_id>: [
                       {
                        allele_id:      <imgt_accession>,
                        star_name:      <full_star_name>,
                        length:         <accession_seq_length>,
                        match_name:     <match_up_to_fields>,
                        query_start:    <query_aln_start>,
                        query_end:      <query_aln_end>,
                        covered_feat:   <aligned_features>,
                        not_covered:    <features_no_coverage>,
                        coding_diffs:   <total_exon_differences>,
                        noncode_eddist: <edit_dist_introns_utr>,
                        differences: [
                             {
                                kind: <mismatch/insertion/deletion>,
                                pos:  <position>,
                                size: <variant_length>,
                                feat: <feature_containing_variant>                            
                             },
                             ...                        
                            ]                     
                        }                     
                    ],
                    ...
                }
                ...
            }
 }   
```

Extracting detailed results for a given locus can be done with the [jq](https://jqlang.github.io/jq/) tool:
```
$ jq '.hla_calls | with_entries(select(.key == "HLA-DRB1"))' hifihla_report.json
{
  "HLA-DRB1": {
    "h1tg000036l_26660055_26675279": [
      {
        "allele_id": "HLA00687",
        "star_name": "HLA-DRB1*04:02:01",
        "length": 14499,
        "match_name": "*04:02:01",
        "query_start": 0,
        "query_end": 14499,
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
          "UTR_3"
        ],
        "not_covered": [],
        "coding_diffs": 0,
        "noncode_eddist": 4,
        "differences": [
          {
            "kind": "Insertion",
            "pos": 5394,
            "size": 1,
            "feat": "Intron_1"
          },
          {
            "kind": "Insertion",
            "pos": 6607,
            "size": 1,
            "feat": "Intron_1"
          },
          {
            "kind": "Insertion",
            "pos": 8819,
            "size": 2,
            "feat": "Intron_2"
          }
        ]
      }
    ],
    "h2tg000067l_26524014_26537902": [
      {
        "allele_id": "HLA17236",
        "star_name": "HLA-DRB1*10:01:01:03",
        "length": 13800,
        "match_name": "*10:01:01:03",
        "query_start": 0,
        "query_end": 13800,
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
          "UTR_3"
        ],
        "not_covered": [],
        "coding_diffs": 0,
        "noncode_eddist": 0,
        "differences": []
      }
    ]
  }
}
```

## Examples <a name="examples"></a>
Type [HLA consensus sequences](https://downloads.pacbcloud.com/public/dataset/pbAmpliconAnalysis_HLA/pbaa/12878-HG001/pbaa_12878-HG001_passed_cluster_sequences.fasta) and print info to stdout:
```
$ hifihla call-consensus \
          --fasta pbaa_12878-HG001_passed_cluster_sequences.fasta \
          --outdir my_output_dir/ \
          -v
```
Type PacBio [HiFi WGS dataset](https://s3-us-west-2.amazonaws.com/human-pangenomics/index.html?prefix=working/HPRC_PLUS/HG002/) and print details including alignment information to stdout:
```
$ hifihla call-contigs \
          --abam HG002.asm.GRCh38.bam \
          --hap1 HG002.asm.bp.hap1.p_ctg.fasta.gz \
          --hap2 HG002.asm.bp.hap2.p_ctg.fasta.gz \
          --outdir my_output_dir \
          -vv
```
Compare query to specific alleles by name or accession number
```
$ hifihla align-imgt \
          --fasta HG001_HLA_A_11_01_01_01.fasta \
          -n HLA-A*11:01:01:03 \
          -t HLA00043
{                                                                                                                                              [38/1857]
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
        "differences": []
      },
     "HLA15502": {                                                                                                                             [3/1857]
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

## Changelog <a name="changelog"></a>
Changelog - PacBio HiFi HLA Typing - hifihla

## v0.1.0: 5/31/23
### Changes
- Report results in tsv and json format

## v0.2.0: 9/25/23
### Changes
- Update README for initial release
- Latest database version v3.53
- Bug fixes

## References <a name="references"></a>
[1]: Barker DJ, Maccari G, Georgiou X, Cooper MA, Flicek P, Robinson J, Marsh SGE. _The IPD-IMGT/HLA Database_. Nucleic Acids Research (2023) 51:D1053-60.

## DISCLAIMER
THIS WEBSITE AND CONTENT AND ALL SITE-RELATED SERVICES, INCLUDING ANY DATA, ARE PROVIDED "AS IS," WITH ALL FAULTS, WITH NO REPRESENTATIONS OR WARRANTIES OF ANY KIND, EITHER EXPRESS OR IMPLIED, INCLUDING, BUT NOT LIMITED TO, ANY WARRANTIES OF MERCHANTABILITY, SATISFACTORY QUALITY, NON-INFRINGEMENT OR FITNESS FOR A PARTICULAR PURPOSE. YOU ASSUME TOTAL RESPONSIBILITY AND RISK FOR YOUR USE OF THIS SITE, ALL SITE-RELATED SERVICES, AND ANY THIRD PARTY WEBSITES OR APPLICATIONS. NO ORAL OR WRITTEN INFORMATION OR ADVICE SHALL CREATE A WARRANTY OF ANY KIND. ANY REFERENCES TO SPECIFIC PRODUCTS OR SERVICES ON THE WEBSITES DO NOT CONSTITUTE OR IMPLY A RECOMMENDATION OR ENDORSEMENT BY PACIFIC BIOSCIENCES.
