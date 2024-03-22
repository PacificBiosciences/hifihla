## Usage <a name="usage"></a>

`hifihla` has four subcommands: 
* [call-reads](usage_call-reads.md)
* [call-consensus](usage_call-consensus.md)
* [call-contigs](usage_call-contigs.md)
* [align-imgt](usage_align-imgt.md)

```
Usage: hifihla <COMMAND>

Commands:
  call-reads      Call HLA loci from an aligned BAM of HiFi reads
  call-contigs    Extract HLA loci from assembled MHC contigs & call star alleles on extracted sequences
  call-consensus  Call HLA Star (*) alleles from consensus sequences
  align-imgt      Align queries to IMGT/HLA genomic accession sequences
  help            Print this message or the help of the given subcommand(s)

Options:
  -h, --help     Print help
  -V, --version  Print version
```

## Subcommand Inputs
| Subcommand     | Input Type                          | File types      |Description |
|----------------|-------------------------------------|-----------------|------------|
| call-reads     | Aligned HiFi reads                  | BAM             | Call Class I (ABC) from HiFi reads aligned to GRCh38 (no alts)                               | 
| call-contigs   | Aligned assembly; unaligned contigs | BAM, FASTA(.gz) | Extract and Call HLA loci from assembled MHC contigs                                         |
| call-consensus | Amplicon/Isoseq consensus           | FASTA           | Call HLA alleles from consensus sequences (e.g. amplicon assays)                             |
| align-imgt     | Sequence/IMGT accessions            | FASTA           | Compare sequences in fasta format or database sequences to specific IMGT/HLA genomic alleles |
