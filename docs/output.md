## Output <a name="output"></a>
`call-reads`, `call-consensus` and `call-contigs` all generate three reports containing HLA star-allele type calls.  Additionally, `call-contigs` produces fasta files of extracted sequences from the assembly.  If `out_prefix` is given as a directory _+ prefix/samplename_, output files will be joined to the prefix with underscore `_`. 

| File                                         | Description |
| -------------------------------------------- | ----------- |
| {out_prefix}\[_/\]hifihla_summary.tsv             | Detailed file listing best call for each locus    |
| {out_prefix}\[_/\]hifihla_report.tsv              | Simple tsv file listing calls for each locus      |
| {out_prefix}\[_/\]hifihla_report.json             | Detailed results file, see below for example      |
| {out_prefix}\[_/\]asm.contigs.h[12].fasta         | Extracted (full) assembly contigs aligning to MHC |
| {out_prefix}\[_/\]asm.contigs.h[12].fasta.fai     | FASTA index for contigs                           |
| {out_prefix}\[_/\]extracted.targets.h[12].fasta   | Extracted targets used for star-typing            |

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
| coverage     | HiFi read count of allele (call-reads only)          |
| errRate      | Total error rate of HiFi reads (call-reads only)     |
| Type         | Final type call                                      |

Final type call is determined by edit distance and exact matches to the query (nMatches).  The final call is four field if the query sequences matches exactly and is the only exact match returned.

`call-reads` summary for HG002 (Class I):
```
queryId  qLen  nMatches  gType              gPctId  gPctCov  gEdit  cdnaType        exCovered        exEdit  coverage  errRate  Type
HLA-A_1  3503  1         HLA-A*01:01:01:01  100.0   100.0    0      HLA-A*01:01:01  1,2,3,4,5,6,7,8  0       15        0.00276  HLA-A*01:01:01:01
HLA-A_0  3517  1         HLA-A*26:01:01:01  100.0   100.0    0      HLA-A*26:01:01  1,2,3,4,5,6,7,8  0       13        0.00249  HLA-A*26:01:01:01
HLA-B_0  3855  1         HLA-B*35:08:01:01  100.0   100.0    0      HLA-B*35:08:01  1,2,3,4,5,6,7    0       18        0.00231  HLA-B*35:08:01:01
HLA-B_1  4070  1         HLA-B*38:01:01:01  100.0   100.0    0      HLA-B*38:01:01  1,2,3,4,5,6,7    0       10        0.00204  HLA-B*38:01:01:01
HLA-C_0  4264  1         HLA-C*04:01:01:06  100.0   100.0    0      HLA-C*04:01:01  1,2,3,4,5,6,7,8  0       12        0.00135  HLA-C*04:01:01:06
HLA-C_1  4303  1         HLA-C*12:03:01:01  100.0   100.0    0      HLA-C*12:03:01  1,2,3,4,5,6,7,8  0       8         0.00215  HLA-C*12:03:01:01
```
`call-contigs` summary for HG00733:
```
queryId                                  qLen   nMatches  gType                 gPctId  gPctCov  gEdit  cdnaType           exCovered        exEdit  coverage  errRate  Type
HG00733#1#h1tg000070l_29911131_29915604  4474   1         HLA-A*24:02:01:01     100.0   100.0    0      HLA-A*24:02:01     1,2,3,4,5,6,7,8  0       1         N/A      HLA-A*24:02:01:01
HG00733#2#h2tg000008l_1854867_1859341    4475   1         HLA-A*30:02:01:01     100.0   100.0    0      HLA-A*30:02:01     1,2,3,4,5,6,7,8  0       1         N/A      HLA-A*30:02:01:01
HG00733#1#h1tg000070l_31324234_31329311  5078   1         HLA-B*35:02:01:02     100.0   100.0    0      HLA-B*35:02:01     1,2,3,4,5,6,7    0       1         N/A      HLA-B*35:02:01:02
HG00733#2#h2tg000008l_440231_445304      5074   1         HLA-B*18:01:01:01     100.0   100.0    0      HLA-B*18:01:01     1,2,3,4,5,6,7    0       1         N/A      HLA-B*18:01:01:01
HG00733#1#h1tg000070l_31239869_31245142  5274   1         HLA-C*04:01:01:06     100.0   100.0    0      HLA-C*04:01:01     1,2,3,4,5,6,7,8  0       1         N/A      HLA-C*04:01:01:06
HG00733#2#h2tg000008l_525397_530669      5273   1         HLA-C*05:01:01:01     100.0   100.0    0      HLA-C*05:01:01     1,2,3,4,5,6,7,8  0       1         N/A      HLA-C*05:01:01:01
HG00733#1#h1tg000070l_33003286_33014048  10763  1         HLA-DPA1*01:03:01:02  100.0   100.0    0      HLA-DPA1*01:03:01  1,2,3,4          0       1         N/A      HLA-DPA1*01:03:01:02
HG00733#2#h2tg000060l_26807522_26818289  10768  1         HLA-DPA1*02:01:01:01  100.0   100.0    0      HLA-DPA1*02:01:01  1,2,3,4          0       1         N/A      HLA-DPA1*02:01:01:01
HG00733#1#h1tg000070l_33014638_33027155  12518  1         HLA-DPB1*04:01:01:01  100.0   100.0    0      HLA-DPB1*04:01:01  1,2,3,4,5        0       1         N/A      HLA-DPB1*04:01:01:01
HG00733#2#h2tg000060l_26794480_26806932  12453  1         HLA-DPB1*11:01:01:01  100.0   100.0    0      HLA-DPB1*11:01:01  1,2,3,4,5        0       1         N/A      HLA-DPB1*11:01:01:01
HG00733#1#h1tg000070l_32583472_32591044  7573   1         HLA-DQA1*05:05:01:01  100.0   100.0    0      HLA-DQA1*05:05:01  1,2,3,4          0       1         N/A      HLA-DQA1*05:05:01:01
HG00733#2#h2tg000060l_27233443_27240904  7462   1         HLA-DQA1*05:01:01:01  100.0   100.0    0      HLA-DQA1*05:01:01  1,2,3,4          0       1         N/A      HLA-DQA1*05:01:01:01
HG00733#1#h1tg000070l_32600790_32608991  8202   1         HLA-DQB1*03:01:01:02  100.0   100.0    0      HLA-DQB1*03:01:01  1,2,3,4,5,6      0       1         N/A      HLA-DQB1*03:01:01:02
HG00733#2#h2tg000060l_27214264_27222723  8460   1         HLA-DQB1*02:01:01:01  100.0   100.0    0      HLA-DQB1*02:01:01  1,2,3,4,5,6      0       1         N/A      HLA-DQB1*02:01:01:01
HG00733#1#h1tg000070l_32526722_32541633  14912  1         HLA-DRB1*11:04:01:01  100.0   100.0    0      HLA-DRB1*11:04:01  1,2,3,4,5,6      0       1         N/A      HLA-DRB1*11:04:01:01
HG00733#2#h2tg000060l_27282742_27297652  14911  1         HLA-DRB1*03:01:01:02  100.0   100.0    0      HLA-DRB1*03:01:01  1,2,3,4,5,6      0       1         N/A      HLA-DRB1*03:01:01:02
HG00733#1#h1tg000070l_32462971_32477576  14606  1         HLA-DRB3*02:02:01:04  99.99   100.0    1      HLA-DRB3*02:02:01  1,2,3,4,5,6      0       1         N/A      HLA-DRB3*02:02:01
HG00733#2#h2tg000060l_27346699_27361303  14605  1         HLA-DRB3*02:02:01:01  100.0   100.0    0      HLA-DRB3*02:02:01  1,2,3,4,5,6      0       1         N/A      HLA-DRB3*02:02:01:01
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
                        error_rate:     <total HiFi error rate (call-reads only)>,
                        coverage:       <Number of reads covering allele (call-reads only)>,
                        reads:          <HiFi read names used in analysis of allele (call-reads only)>,
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
jq '.hla_calls | with_entries(select(.key == "HLA-DRB3"))' tmp2/hifihla_report.json

{
    "HLA-DRB3": [
          {
            "HG00733#1#h1tg000070l_32462971_32477576": [
              {
                "allele_id": "HLA22697",
                "star_name": "HLA-DRB3*02:02:01:04",
                "length": 12905,
                "match_name": "*02:02:01",
                "query_start": 0,
                "query_end": 12905,
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
                "noncode_eddist": 1,
                "error_rate": null,
                "coverage": 1,
                "reads": null,
                "differences": [
                  {
                    "kind": "Mismatch",
                    "pos": 8158,
                    "size": 1,
                    "feat": "Intron_2"
                  }
                ]
              }
            ],
            "HG00733#2#h2tg000060l_27346699_27361303": [
              {
                "allele_id": "HLA00895",
                "star_name": "HLA-DRB3*02:02:01:01",
                "length": 13588,
                "match_name": "*02:02:01:01",
                "query_start": 0,
                "query_end": 13588,
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
                "error_rate": null,
                "coverage": 1,
                "reads": null,
                "differences": []
            }
         ]
      }
   ]
}
```
