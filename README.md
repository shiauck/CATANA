# README for CATANA (v1.0)

Time-stamp: <2018-08-15 Cheng-Kai Shiau>

### Introduction

In higher eukaryotes, the generation of RNA isoforms,
including alternative splicing events (AS) and alternative
transcript products (AT), from one single gene increases
functional and regulatory diversities. Identification of
these RNA isoforms is essential for genomic studies.
However, no tool can provide comprehensive alternative event
annotation including both AS and AT. Here we develop a tool
named Comprehensive Alternative Transcripts Atlas based oN
Annotation (CATANA) to identify all 10 known AS and AT
events.

### Installation

The only one requirement is Perl 5.8 or later version.
Additional packages, modules, or dependency are Not required!

Please check the following website for installation of Perl:
https://www.perl.org/get.html

### Usage of CATANA

```

  perl CATANA.pl [-i] <Input GTF/GFF3 file name>
                 [-o] <Folder name for output, optional>
                 [-f] <format of gene annotation, optional>
                 [-l] <Log file name, optional>
                 [-d] <Debug message, optional>
```

> Options

```
-i/--infile FILENAME
```

This is the only one required parameter for CATANA. The
gene annotation stored in GTF/GFF3 format will be parsed.
CATANA will automatically recognize which format it is to
prevent manual error.

```
-o/--outdir DIRNAME
```

This is optional parameter. The default output directory
for holding all 10 alternative events output is named
'alternative_event_annotation'. CATANA will stop if the
folder exists to prevent any accidental mistake.

```
-f/--format
```

Assign the format of gene annotation. The default is auto.
CATANA can automatic guess format of gene annotation file
by having "=" symbol or not. However, after analyses of
some transcriptome reconstruction tools, the output GTF
file might contain "=" symbols. In this case, the assignment
of format is required.

```
-l/--log
```

This is optional parameter. By default, the log will not
be reported. The log file is created when the log file name
is given.

```
-d/--debug
```

This is optional parameter. By default, the debug message
is turned off. Set this parameter to 1 to dump out debug
message.

### Example

```
perl CATANA.pl -i manually_created_alternative_events/simulated_SE.gff3 -l test.txt
```

This command execute CATANA to process simulated skipped
exon example GFF3 contained under folder named
'manually_created_alternative_events' with default rather
than given output folder name, and then dump processing log
into file named 'test.txt'.

### Supplementary tools under CATANA

There are two tools under CATANA folder. One is named
'counting_gene_IDs.sh', and the other one is GFF3 simulator.

> counting_gene_IDs.sh

'counting_gene_IDs.sh' is a BASH shell script using 'awk'
to calculate the number of unique gene names under the
given output folder.

> GFF3 simulator

GFF3 simulator is separated into 10 short R scripts. Each
R script is used to simulate a specific AS or AT event.
We have already generated a set of all 10 AS and AT events
with their graphic gene models shown by IGV. All the R
scripts, simulated GFF files and figures are stored under
folder named 'manually_created_alternative_events'.

### Tutorial

We selected a gene ENSMUSG00000005364 as an example for
illustration of CATANA workflow. The gene structure stored
in GFF3 format was extracted from Ensembl mouse v90 annotation.
The file could be found at example/ENSMUSG00000005364.gff3
From IGV, the snapshot of the gene structure:
![ENSMUSG00000005364](https://github.com/shiauck/CATANA/blob/master/example/ENSMUSG00000005364.png)

In the bottom of the figure, we marked the regions of known
alternative events. Of note, the gene is at negative strand
(as shown by arrow head marks on the blue line by IGV),
so that the ATSS and AFE should be at the right-hand side
of the figure and ATTS should be at the left-hand side.

> How to use CATANA

To use CATANA for annotating alternative events, please use
the following command:

```
perl CATANA.pl -i example/ENSMUSG00000005364.gff3 -o example/annotation -f gff -l example/annotation/annotation.log
```

This command tells CATANA to:

(1) use file example/ENSMUSG00000005364.gff3 as input file

(2) create a folder named example/annotation and put
    alternative annotation files under the folder

(3) the format of input file is GFF

(4) dump the processing information into the file named
    example/annotation/annotation.log

During running, CATANA would pop up the following message
for status tracking:

```
Create folder: /example/annotation

Loading file example/ENSMUSG00000005364.gff3...
100.00%
Loading file example/ENSMUSG00000005364.gff3 Finished

Computing exon usage patterns...
100.00%
Computing exon usage patterns finished

Detecting alternative events...
100.00%
Detecting alternative events finished

Merging identical alternative events...
100.00%
Merging identical alternative events finished

Generating annotation file for alternative events...

Total gene number: 1
1 of 1 (100.00%)
```

> The format of result GFF3

CATANA separates alternative events by three hierarchical
labels, including gene, mRNA, and exon. The details are
listed in the following as well as the inclusion form part
inside example/annotation/SE.gff3 for example:

(1) gene label holds different alternative events happened
    in different or the same genes. The event ID is created
    as combination of Ensembl gene ID, alternative event
    type, and ordinal number beginning from zero. In this
    example, in the 9th column a unique "ID" for GFF3 is
    created as "gene:ENSMUSG00000005364.SE.0". The "name"
    and "gid" labels hold the original Ensembl gene ID.

```
chr6	SE	gene	106715619	106731931	.	-	.	ID=gene:ENSMUSG00000005364.SE.0;Name=gene:ENSMUSG00000005364;gid=gene:ENSMUSG00000005364
```

(2) mRNA labels separate inclusion form from exclusion form.
    Inclusion form is labeled as A, while exclusion form is B.
    In this case, the inclusion forms of SE under gene:ENSMUSG00000005364
    are ENSMUST00000167925 and ENSMUST00000204659, while the 
    exclusion form is ENSMUST00000205004. In the 9th column,
    CATANA creates a unique "ID" in GFF3 for inclusion as
    "gene:ENSMUSG00000005364.SE.0.A". The "Parent" holds the
    parental ID under this hierarchical structure, so here
    is "gene:ENSMUSG00000005364.SE.0" same as the "ID" in gene
    label. The "gid" stores the Ensembl gene ID. The "tid" is
    merged transcript IDs of the two inclusion forms as
    "transcript:ENSMUST00000167925,transcript:ENSMUST00000204659"
    separated by comma symbol.

```
chr6	SE	mRNA	106715619	106731931	.	-	.	ID=gene:ENSMUSG00000005364.SE.0.A;Parent=gene:ENSMUSG00000005364.SE.0;gid=gene:ENSMUSG00000005364;tid=transcript:ENSMUST00000167925,transcript:ENSMUST00000204659
```

(3) exon labels hold detailed genomic structure of exons,
    same as normal GFF3 file did. In addition, for separation
    of each exon, the exon ID is starting from "up" for
    upstream (5’ directional) shared exon, ordinal numbers
    for variable exons, and "dn" for downstream (3’ directional)
    shared exon. Because GFF3 requires gene structure been
    reported by genome coordination rather than 5' to 3' direction,
    In this case the first reported exon is the 3' exon, which
    has "ID" as "gene:ENSMUSG00000005364.SE.0.A.dn". The "Parent"
    holds the parental mRNA lable ID as "gene:ENSMUSG00000005364.SE.0.A".
    The "gid" holds the Ensembl gene ID. The "tid" stores the
    combination of the two inclusion forms transcript ID as
    "transcript:ENSMUST00000167925,transcript:ENSMUST00000204659".

```
chr6	SE	exon	106715619	106715703	.	-	.	ID=gene:ENSMUSG00000005364.SE.0.A.dn;Parent=gene:ENSMUSG00000005364.SE.0.A;gid=gene:ENSMUSG00000005364;tid=transcript:ENSMUST00000167925,transcript:ENSMUST00000204659
chr6	SE	exon	106716697	106716790	.	-	.	ID=gene:ENSMUSG00000005364.SE.0.A.SE1;Parent=gene:ENSMUSG00000005364.SE.0.A;gid=gene:ENSMUSG00000005364;tid=transcript:ENSMUST00000167925,transcript:ENSMUST00000204659
chr6	SE	exon	106731793	106731931	.	-	.	ID=gene:ENSMUSG00000005364.SE.0.A.up;Parent=gene:ENSMUSG00000005364.SE.0.A;gid=gene:ENSMUSG00000005364;tid=transcript:ENSMUST00000167925,transcript:ENSMUST00000204659
```

![ENSMUSG00000005364](https://github.com/shiauck/CATANA/blob/master/example/ENSMUSG00000005364.png)

Note: The format design is following MISO.

