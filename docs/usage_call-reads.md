### Type from aligned HiFi reads
`call-reads` accepts aligned HiFi reads in BAM format and calls HLA alleles directly from reads, without assembly.
```
Call HLA loci from an aligned BAM of HiFi reads

Usage: hifihla call-reads [OPTIONS] --abam <ALIGNED_READS>

Options:
  -j, --threads <THREADS>      Analysis threads [default: 1]
  -v, --verbose...             Enable verbose output
      --log-level <LOG_LEVEL>  Alternative to repeated -v/--verbose: set log level via key. [default: Warn]
  -h, --help                   Print help
  -V, --version                Print version

Input Options:
  -a, --abam <ALIGNED_READS>     Input assembly aligned to GRCh38 (no alts)
  -l, --loci [<LOCI>...]         Input comma-sep loci to extract [default: all]
  -d, --max_depth <MAX_DEPTH>    Maximum reads per locus [default: 50]
  -p, --partial                  Include partially-spanning reads
  -t, --haplotypes <HAPLOTYPES>  Haplotypes in sample [default: 2] [possible values: 1, 2]
  -s, --seed <SEED>              Random number seed for downsampling to max_depth [default: 42]

Output Options:
  -o, --out_prefix <OUT_PREFIX>  Output prefix
      --outdir <OUTDIR>          Output directory [deprecated]
  -f, --full_length              Full length IMGT records only (exclude exon-only records)
  -x, --max_matches <MATCHES>    Maximum matches in output report [default: 10]
  -m, --min_allele_freq <MAF>    Minimum allele frequency [default: 0.1]
  -b, --min_cdf <MINCDF>         Minimum binomial CDF to call het/hom [default: 0.001]

Presets:
  --preset <PRESET>  Sequence type presets [possible values: te, wgs]
```
#### Input Options Description
* `--abam` HiFi reads aligned to [GRCH38 no alts](ftp://ftp.ncbi.nlm.nih.gov/genomes/all/GCA/000/001/405/GCA_000001405.15_GRCh38/seqs_for_alignment_pipelines.ucsc_ids/GCA_000001405.15_GRCh38_no_alt_analysis_set.fna.gz).
* `--loci` HLA loci to call. Currently limited to HLA-A,HLA-B,HLA-C.
* `--max_depth` Maximim reads to use per locus. Reads are randomly downsampled if coverage > d.
* `--partial` Include HiFi reads that do not fully span locus, but still span exon 2 (minimum requirement).
* `--haplotypes` Expected number of haploytypes in sample.  
* `--seed` Random number seed for downsampling and clustering.

### Output Options Description
* `--out_prefix` Output prefix, accepts a directory or a directory + prefix. 
* `--outdir` Output directory \[deprecated\].
* `--full_length` Restrict allele matches to full length IMGT records (exclude exon-only accessions).
* `--max_matches` Maximum number of equivalent matches to list per query sequence.
* `--min_allele_freq` Minimum fraction of reads for minor allele.  Clusters with lower frequency will be ignored.
* `--min_cdf` Minimum binomial CDF for minor allele.  Clusters with lower CDF will be ignored.

Presets are convenience options for whole genome sequencing with long (>10kb) HiFi Reads`wgs` or shorter (~5kb) Target Enrichment HiFi Reads `te`.
The only preset option at this time sets the `-p` flag for wgs, and no `-p` for te.  Target Enrichment datasets tend to have higher coverage, so we filter for fully spanning reads.

### Examples
Type Class I (HLA-A/-B/-C) alleles from [HPRC](https://github.com/human-pangenomics/HPP_Year1_Data_Freeze_v1.0) HG00733 aligned WGS HiFi reads using 8 threads:
```
hifihla call-reads \
        --preset wgs \
        -j 8 \
        -a HG00733.GRCh38_no_alts.bam \
        -o out_dir/my_sample

column -t out_dir/my_sample_hifihla_summary.tsv

queryId  qLen  nMatches  gType              gPctId  gPctCov  gEdit  cdnaType        exCovered        exEdit  coverage  errRate  Type
HLA-A_1  3502  1         HLA-A*24:02:01:01  100.0   100.0    0      HLA-A*24:02:01  1,2,3,4,5,6,7,8  0       9         0.00346  HLA-A*24:02:01:01
HLA-A_0  3503  1         HLA-A*30:02:01:01  100.0   100.0    0      HLA-A*30:02:01  1,2,3,4,5,6,7,8  0       25        0.00293  HLA-A*30:02:01:01
HLA-B_1  4081  1         HLA-B*18:01:01:01  100.0   100.0    0      HLA-B*18:01:01  1,2,3,4,5,6,7    0       16        0.00281  HLA-B*18:01:01:01
HLA-B_0  4085  1         HLA-B*35:02:01:02  100.0   100.0    0      HLA-B*35:02:01  1,2,3,4,5,6,7    0       18        0.00250  HLA-B*35:02:01:02
HLA-C_0  4264  1         HLA-C*04:01:01:06  100.0   100.0    0      HLA-C*04:01:01  1,2,3,4,5,6,7,8  0       20        0.00280  HLA-C*04:01:01:06
HLA-C_1  4303  1         HLA-C*05:01:01:01  100.0   100.0    0      HLA-C*05:01:01  1,2,3,4,5,6,7,8  0       12        0.00335  HLA-C*05:01:01:01
```
Type Class I alleles from targeted [Twist Alliance Panels](https://www.pacb.com/wp-content/uploads/Application-Brief-HiFi-Target-Enrichment-Best-Practices.pdf) sequenced with [PacBio HiFi](https://downloads.pacbcloud.com/public/dataset/HiFiTE_SqIIe/Oct_2022/TwistAllianceLongReadPGx/):
```
hifihla call-reads \
        --preset te \
        -j 8 \
        -a HG01190.GRCh38_noalt.deepvariant.haplotagged..bam \
        -o my_output_dir

column -t my_output_dir/hifihla_summary.tsv

queryId  qLen  nMatches  gType              gPctId  gPctCov  gEdit  cdnaType        exCovered        exEdit  coverage  errRate  Type
HLA-A_1  3517  1         HLA-A*02:01:01:01  100.0   100.0    0      HLA-A*02:01:01  1,2,3,4,5,6,7,8  0       19        0.00108  HLA-A*02:01:01:01
HLA-A_0  3502  1         HLA-A*24:02:01:01  100.0   100.0    0      HLA-A*24:02:01  1,2,3,4,5,6,7,8  0       28        0.00213  HLA-A*24:02:01:01
HLA-B_1  3327  1         HLA-B*15:20        100.0   100.0    0      HLA-B*15:20     1,2,3,4,5,6,7    0       18        0.00107  HLA-B*15:20
HLA-B_0  4081  1         HLA-B*18:01:01:83  100.0   100.0    0      HLA-B*18:01:01  1,2,3,4,5,6,7    0       10        0.00059  HLA-B*18:01:01:83
HLA-C_0  4304  1         HLA-C*01:02:01:01  100.0   100.0    0      HLA-C*01:02:01  1,2,3,4,5,6,7,8  0       24        0.00121  HLA-C*01:02:01:01
HLA-C_1  4318  1         HLA-C*07:01:01:16  100.0   100.0    0      HLA-C*07:01:01  1,2,3,4,5,6,7,8  0       26        0.00205  HLA-C*07:01:01:16
```
Force call only a single, full-length HLA-A haplotype using at most 15 reads:
```
hifihla call-reads \
        -d 15 \
        -t 1 \
        -f \
        -l HLA-A \
        -a NA12889.GRCH38.haplotagged.bam \
        -o out_dir/my_sample

cat out_dir/my_sample_hifihla_report.json
{
  "sample_id": "NA12889.GRCh38.haplotagged",
  "version": "hifihla 0.3.0;IPD-IMGT/HLA 3.55 (2024-01)",
  "hla_calls": {
    "HLA-A": [
      {
        "HLA-A_0": [
          {
            "allele_id": "HLA00037",
            "star_name": "HLA-A*03:01:01:01",
            "length": 3502,
            "match_name": "*03:01:01:01",
            "query_start": 0,
            "query_end": 3502,
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
              "error_rate": 0.0023986294,
              "coverage": 15,
              "reads": [
                "m54329U_230312_191124/80677293/ccs",
                "m84046_230327_223715_s3/52499911/ccs",
                "m54329U_230314_044350/92145583/ccs",
                "m54329U_230311_094504/134349275/ccs",
                "m84046_230327_223715_s3/41027584/ccs",
                "m84046_230327_223715_s3/80090755/ccs",
                "m84046_230327_223715_s3/132322013/ccs",
                "m84046_230327_223715_s3/80871474/ccs",
                "m84046_230327_223715_s3/41226855/ccs",
                "m54329U_230312_191124/63046676/ccs",
                "m54329U_230311_094504/31064736/ccs",
                "m54329U_230312_191124/56625320/ccs",
                "m84046_230327_223715_s3/38012954/ccs",
                "m84046_230327_223715_s3/23856426/ccs",
                "m54329U_230314_044350/129892658/ccs"
              ],
              "differences": []
            }
         ]
       }
     ]
   }
}
  ```
