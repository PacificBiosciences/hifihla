### Type consensus sequences
`call-consensus` accepts a FASTA file of sequences and attempts to find the closest matching allele in the [IMGT/HLA database](https://www.ebi.ac.uk/ipd/imgt/hla/).  It is assumed that each FASTA record is a single locus.
Optionally call star alleles using exon sequence only.
```
Call HLA Star (*) alleles from consensus sequences

Usage: hifihla call-consensus [OPTIONS] --fasta <FASTA>

Options:
  -f, --fasta <FASTA>            Input fasta file of consensus sequences
  -o, --out_prefix <OUT_PREFIX>  Output prefix
      --outdir <OUTDIR>          Output directory [deprecated]
  -c, --cdna                     Enable cDNA-only calling
  -e, --exon2                    Require Exon2 in query sequence
  -l, --full_length              Full length IMGT records only
  -j, --threads <THREADS>        Analysis threads [default: 1]
  -x, --max_matches <MATCHES>    Maximum equivalent matches per query in report [default: 10]
  -v, --verbose...               Enable verbose output
      --log-level <LOG_LEVEL>    Alternative to repeated -v/--verbose: set log level via key.
  -h, --help                     Print help
  -V, --version                  Print version
```
#### Options Description
* `--fasta` Fasta file of consensus query sequences. Only one allele per query sequence.
* `--out_prefix` Output prefix, accepts a directory or a directory + prefix.
* `--outdir` Output directory \[deprecated\].
* `--cdna` Call and report only coding regions (cdna).  Can be used for either DNA or RNA sequences.
* `--exon2` Require exon 2 in query. This may reduce search space.
* `--max_matches` Only report up to this number of matches in the json report.

### Example
Type HLA consensus sequences, for example from [HiFi amplicon consensus with pbaa](https://downloads.pacbcloud.com/public/dataset/pbAmpliconAnalysis_HLA/pbaa/12878-HG001):
```
hifihla call-consensus \
        --fasta pbaa_12878-HG001_passed_cluster_sequences.fasta \
        --out_prefix out_dir/my_sample

column -t out_dir/my_sample_hifihla_summary.tsv

queryId                                                     qLen  nMatches  gType                 gPctId  gPctCov  gEdit  cdnaType           exCovered        exEdit  coverage  errRate  Type
sample-12878-HG001_guide-HLA-A_cluster-0_ReadCount-1023     3098  5         HLA-A*01:01:01:01     100.0   88.43    0      HLA-A*01:01:01     1,2,3,4,5,6,7,8  0       1         N/A      HLA-A*01:01:01
sample-12878-HG001_guide-HLA-A_cluster-1_ReadCount-999      3098  3         HLA-A*11:01:01:01     100.0   88.43    0      HLA-A*11:01:01     1,2,3,4,5,6,7,8  0       1         N/A      HLA-A*11:01:01
sample-12878-HG001_guide-HLA-B_cluster-1_ReadCount-399      3354  7         HLA-B*08:01:01:01     100.0   81.69    0      HLA-B*08:01:01     1,2,3,4,5,6,7    0       1         N/A      HLA-B*08:01:01
sample-12878-HG001_guide-HLA-B_cluster-0_ReadCount-481      3363  1         HLA-B*56:01:01:04     100.0   86.53    0      HLA-B*56:01:01     1,2,3,4,5,6,7    0       1         N/A      HLA-B*56:01:01:04
sample-12878-HG001_guide-HLA-C_cluster-0_ReadCount-541      3384  6         HLA-C*01:02:01:01     100.0   78.62    0      HLA-C*01:02:01     1,2,3,4,5,6,7,8  0       1         N/A      HLA-C*01:02:01
sample-12878-HG001_guide-HLA-C_cluster-1_ReadCount-484      3389  6         HLA-C*07:01:01:01     100.0   78.48    0      HLA-C*07:01:01     1,2,3,4,5,6,7,8  0       1         N/A      HLA-C*07:01:01
sample-12878-HG001_guide-HLA-DPB1_cluster-1_ReadCount-348   5824  33        HLA-DPB1*04:01:01:01  100.0   50.52    0      HLA-DPB1*04:01:01  2,3,4,5          0       1         N/A      HLA-DPB1*04:01:01
sample-12878-HG001_guide-HLA-DPB1_cluster-0_ReadCount-475   5760  7         HLA-DPB1*14:01:01:01  100.0   50.23    0      HLA-DPB1*14:01:01  2,3,4,5          0       1         N/A      HLA-DPB1*14:01:01
sample-12878-HG001_guide-HLA-DQB1_cluster-1_ReadCount-2032  4076  12        HLA-DQB1*02:01:01:01  100.0   54.49    0      HLA-DQB1*02:01:01  2,3,4            0       1         N/A      HLA-DQB1*02:01:01
sample-12878-HG001_guide-HLA-DQB1_cluster-0_ReadCount-3740  3703  14        HLA-DQB1*05:01:01:02  100.0   52.22    0      HLA-DQB1*05:01:01  2,3,4            0       1         N/A      HLA-DQB1*05:01:01
sample-12878-HG001_guide-HLA-DRB1_cluster-1_ReadCount-1011  3900  15        HLA-DRB1*01:01:01:01  100.0   35.13    0      HLA-DRB1*01:01:01  2,3,4            0       1         N/A      HLA-DRB1*01:01:01
sample-12878-HG001_guide-HLA-DRB1_cluster-0_ReadCount-2022  3646  14        HLA-DRB1*03:01:01:01  100.0   26.21    0      HLA-DRB1*03:01:01  2,3,4            0       1         N/A      HLA-DRB1*03:01:01
```
Note that query sequences that match >1 allele are not labeled as four-field matches because the actual call is ambiguous.  This can happen when differences between alleles are outside the amplified region.
For example, HLA-A_cluster-1 above matches three alleles perfectly over the amplified range.  All three matches are listed in the json.

```
jq '.. | objects | to_entries | .[] | select(.key == "sample-12878-HG001_guide-HLA-A_cluster-1_ReadCount-999")' my_sample_hifihla_report.json

{
  "key": "sample-12878-HG001_guide-HLA-A_cluster-1_ReadCount-999",
  "value": [
    {
      "allele_id": "HLA00043",
      "star_name": "HLA-A*11:01:01:01",
      "length": 3503,
      "match_name": "*11:01:01",
      "query_start": 182,
      "query_end": 3280,
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
    {
      "allele_id": "HLA32930",
      "star_name": "HLA-A*11:01:01:75",
      "length": 3503,
      "match_name": "*11:01:01",
      "query_start": 182,
      "query_end": 3280,
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
    {
      "allele_id": "HLA38435",
      "star_name": "HLA-A*11:01:01:88",
      "length": 3503,
      "match_name": "*11:01:01",
      "query_start": 182,
      "query_end": 3280,
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
    }
  ]
}
```
