variant-filter
==============

A false-positive filter for variants called from massively parallel sequencing

Quick start
-----------

Download the `fpfilter.pl` script, and view the detailed usage manual:

    curl -LO https://github.com/ckandoth/variant-filter/archive/master.zip; unzip master.zip
    perl variant-filter-master/fpfilter.pl --help

Given `snvs.vcf` a single sample VCF listing SNVs, generate a list of variant loci in a subfolder:

    mkdir fpfilter
    perl -ane 'print join("\t",@F[0,1,1])."\n" unless(m/^#/)' snvs.vcf > fpfilter/snvs.var

Given `tumor.bam`, a BAM file containing the SNVs, and its reference FASTA `grch37.fa`, run [bam-readcount](https://gist.github.com/ckandoth/87ba44948cb747916f8d#file-build_bam_readcount-txt):

    bam-readcount -q1 -b15 -w1 -l fpfilter/snvs.var -f grch37.fa tumor.bam > fpfilter/snvs.readcount

Run the `fpfilter.pl` script using the readcounts as an input:

    perl fpfilter.pl --var-file snvs.vcf --readcount-file fpfilter/snvs.readcount --output-file fpfilter/snvs.fpfilter

The last two columns of the resulting file `snvs.fpfilter` contains a VCF friendly `FILTER` code, and an extra column with details.

Provenance
----------

The original `fpfilter.pl` script for filtering Single Nucleotide Variants (SNVs) was written by [Dan Koboldt](http://genome.wustl.edu/people/individual/dan-koboldt/) for [VarScan](http://sourceforge.net/projects/varscan/files/scripts/), and [Dave Larson](http://genome.wustl.edu/people/individual/david-larson/) for [SomaticSniper](https://github.com/genome/somatic-sniper/tree/master/src/scripts). The latest version of the script is maintained by Dan, Dave, and other developers at the [The Genome Institute](http://genome.wustl.edu/people/), as a Perl module named [FilterFalsePositives.pm](https://github.com/genome/genome/blob/master/lib/perl/Genome/Model/Tools/Somatic/FilterFalsePositives.pm). They even wrote a module named [FilterFalseIndels.pm](https://github.com/genome/genome/blob/master/lib/perl/Genome/Model/Tools/Somatic/FilterFalseIndels.pm) for small indels. However, those modules are not very portable, and have [dependencies](https://github.com/genome/gms) on their internal codebase. So this repository is my attempt to remove those dependencies, make it easier to use, and add other improvements like VCF support.

Authors
-------

    Cyriac Kandoth (ckandoth@gmail.com)
    Dan Koboldt, Analysis Manager, Human Genetics Group, The Genome Institute
    David Larson, Director of Bioinformatics, The Genome Institute
