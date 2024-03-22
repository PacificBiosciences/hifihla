### Type HLA from MHC assembled contigs
`call-contigs` requires 2 or 3 input files and an output directory:
 * Assembled contigs must be aligned to [GRCH38 no alts](ftp://ftp.ncbi.nlm.nih.gov/genomes/all/GCA/000/001/405/GCA_000001405.15_GRCh38/seqs_for_alignment_pipelines.ucsc_ids/GCA_000001405.15_GRCh38_no_alt_analysis_set.fna.gz)
 * Assembled haplotype fasta[.gz] files (1 or 2)  

Options:
 * `hap2` is optional -- the fasta argument to `hap1` may contain one or two MHC haplotig sets.  If `hap2` is not set, alleles will be phased by contig name.
 * Define the `loci` to be extracted and called (see [IMGT genes](https://hla.alleles.org/genes/index.html)) [default all loci]
 * Limit equivalent matches in report with `--max_matches` -- This is useful to limit reporting of common alleles with shared cDNA.
 * Require assembly alleles to be of a `--min_length`.
 * Limit output matches to full genomic IMGT accessions with `--full_length`.
```
Extract HLA loci from assembled MHC contigs & call star alleles on extracted sequences
 
Usage: hifihla call-contigs [OPTIONS] --abam <ALIGNED_ASSEMBLY> --hap1 <HAP1_FA> --outdir <OUTDIR>

Options:
  -a, --abam <ALIGNED_ASSEMBLY>  Input assembly aligned to GRCh38
  -p, --hap1 <HAP1_FA>           Input hap1 assembly fa(.gz)
  -m, --hap2 <HAP2_FA>           Input hap2 assembly fa(.gz) (optional)
  -o, --outdir <OUTDIR>          Output directory
  -l, --loci [<LOCI>...]         Input comma-sep loci to extract [default: all]
  -s, --min_length <MINLENGTH>   Minimum length of extracted targets [default: 1000]
  -f, --full_length              Full length IMGT records only
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

### Examples
Type HG00733 HPRC [assembly](https://s3-us-west-2.amazonaws.com/human-pangenomics/working/HPRC_PLUS/HG00733/assemblies/year1_freeze_assembly_v2/):

#### Align contigs to reference
The following command using [minimap2](https://github.com/lh3/minimap2/) is recommended for assembly alignment:
```
minimap2 -t 12 \
         -L --secondary=no --eqx -ax asm5\
         -R '@RG\tID:HG00733\tSM:HG00733' \
         GRCh38_no_alts.fasta \
         HG00733.paternal.f1_assembly_v2.fa.gz HG00733.maternal.f1_assembly_v2.fa.gz  | \
         samtools sort -@ 3 \
                       -T /scratch \
                       -m 100M \
                       -O BAM > HG00733.asm.GRCh38_no_alts.bam
samtools index HG00733.asm.GRCH38_no_alts.bam
```
#### Call HLA alleles
Call all available loci from separated haplotigs:
```
hifihla call-contigs \
        --abam HG00733.asm.GRCh38_no_alts.bam \
        --hap1 HG00733.paternal.f1_assembly_v2.fa.gz \
        --hap2 HG00733.maternal.f1_assembly_v2.fa.gz \
        --outdir my_output_dir

head -7 my_output_dir/hifihla_summary.tsv | column -t
 
queryId                                  qLen  nMatches  gType              gPctId  gPctCov  gEdit  cdnaType        exCovered        exEdit  coverage  errRate  Type
HG00733#1#h1tg000070l_29911131_29915604  4474  1         HLA-A*24:02:01:01  100.0   100.0    0      HLA-A*24:02:01  1,2,3,4,5,6,7,8  0       1         N/A      HLA-A*24:02:01:01
HG00733#2#h2tg000008l_1854867_1859341    4475  1         HLA-A*30:02:01:01  100.0   100.0    0      HLA-A*30:02:01  1,2,3,4,5,6,7,8  0       1         N/A      HLA-A*30:02:01:01
HG00733#1#h1tg000070l_31324234_31329311  5078  1         HLA-B*35:02:01:02  100.0   100.0    0      HLA-B*35:02:01  1,2,3,4,5,6,7    0       1         N/A      HLA-B*35:02:01:02
HG00733#2#h2tg000008l_440231_445304      5074  1         HLA-B*18:01:01:01  100.0   100.0    0      HLA-B*18:01:01  1,2,3,4,5,6,7    0       1         N/A      HLA-B*18:01:01:01
HG00733#1#h1tg000070l_31239869_31245142  5274  1         HLA-C*04:01:01:06  100.0   100.0    0      HLA-C*04:01:01  1,2,3,4,5,6,7,8  0       1         N/A      HLA-C*04:01:01:06
HG00733#2#h2tg000008l_525397_530669      5273  1         HLA-C*05:01:01:01  100.0   100.0    0      HLA-C*05:01:01  1,2,3,4,5,6,7,8  0       1         N/A      HLA-C*05:01:01:01
```
Call subset of loci from contigs of just one haplotype:
```
hifihla call-contigs \
        --abam HG00733.asm.GRCh38_no_alts.bam \
        --hap1 HG00733.paternal.f1_assembly_v2.fa.gz \
        --loci HLA-DQA1,HLA-DPA1,HLA-DRB1 \
        --outdir my_output_dir

column -t my_output_dir/hifihla_summary.tsv

queryId                                  qLen   nMatches  gType                 gPctId  gPctCov  gEdit  cdnaType           exCovered    exEdit  coverage  errRate  Type
HG00733#1#h1tg000070l_33003286_33014048  10763  1         HLA-DPA1*01:03:01:02  100.0   100.0    0      HLA-DPA1*01:03:01  1,2,3,4      0       1         N/A      HLA-DPA1*01:03:01:02
HG00733#1#h1tg000070l_32583472_32591044  7573   1         HLA-DQA1*05:05:01:01  100.0   100.0    0      HLA-DQA1*05:05:01  1,2,3,4      0       1         N/A      HLA-DQA1*05:05:01:01
HG00733#1#h1tg000070l_32526722_32541633  14912  1         HLA-DRB1*11:04:01:01  100.0   100.0    0      HLA-DRB1*11:04:01  1,2,3,4,5,6  0       1         N/A      HLA-DRB1*11:04:01:01
HG00733#1#h1tg000070l_32462971_32477576  14606  1         HLA-DRB3*02:02:01:04  99.99   100.0    1      HLA-DRB3*02:02:01  1,2,3,4,5,6  0       1         N/A      HLA-DRB3*02:02:01
```
Call class I from un-separated contigs:
```
cat HG00733.paternal.f1_assembly_v2.fa.gz HG00733.maternal.f1_assembly_v2.fa.gz > HG00733.both.fasta.gz

hifihla call-contigs \
        --abam HG00733.asm.GRCh38_no_alts.bam \
        --hap1 HG00733.both.fasta.gz \
        --loci HLA-A,HLA-B,HLA-C \
        --outdir my_output_dir
        
column -t my_output_dir/hifihla_summary.tsv
 
queryId                                  qLen  nMatches  gType              gPctId  gPctCov  gEdit  cdnaType        exCovered        exEdit  coverage  errRate  Type
HG00733#1#h1tg000070l_29911131_29915604  4474  1         HLA-A*24:02:01:01  100.0   100.0    0      HLA-A*24:02:01  1,2,3,4,5,6,7,8  0       1         N/A      HLA-A*24:02:01:01
HG00733#2#h2tg000008l_1854867_1859341    4475  1         HLA-A*30:02:01:01  100.0   100.0    0      HLA-A*30:02:01  1,2,3,4,5,6,7,8  0       1         N/A      HLA-A*30:02:01:01
HG00733#1#h1tg000070l_31324234_31329311  5078  1         HLA-B*35:02:01:02  100.0   100.0    0      HLA-B*35:02:01  1,2,3,4,5,6,7    0       1         N/A      HLA-B*35:02:01:02
HG00733#2#h2tg000008l_440231_445304      5074  1         HLA-B*18:01:01:01  100.0   100.0    0      HLA-B*18:01:01  1,2,3,4,5,6,7    0       1         N/A      HLA-B*18:01:01:01
HG00733#1#h1tg000070l_31239869_31245142  5274  1         HLA-C*04:01:01:06  100.0   100.0    0      HLA-C*04:01:01  1,2,3,4,5,6,7,8  0       1         N/A      HLA-C*04:01:01:06
HG00733#2#h2tg000008l_525397_530669      5273  1         HLA-C*05:01:01:01  100.0   100.0    0      HLA-C*05:01:01  1,2,3,4,5,6,7,8  0       1         N/A      HLA-C*05:01:01:01
```
