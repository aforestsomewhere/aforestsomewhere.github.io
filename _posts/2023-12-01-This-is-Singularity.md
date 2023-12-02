## And, we have containers!
Gave Singularity another try, after letting the dust settle on a previous traumatic experience, expecting it not to work AT ALL and being astounded and also unsurprised that it actually worked with minimal effort.

The target: the wonderful ggCaller (https://github.com/samhorsfield96/ggCaller).

A Singularity image (.sif) was already provided by the developer (https://zenodo.org/records/7870950), although it should be possible to wrangle Docker images into Singularity format with a theoretically simple conversion.
```tsql
#load Singularity
module load singularity/3.10.5
singularity -h

Linux container platform optimized for High Performance Computing (HPC)                                                            and
Enterprise Performance Computing (EPC)

Usage:
  singularity [global options...]

Description:
  Singularity containers provide an application virtualization layer en                                                           abling
  mobility of compute via both application and environment portability.                                                            With
  Singularity one is capable of building a root file system that runs o                                                           n any
  other Linux system where Singularity is installed.

Options:
  -c, --config string   specify a configuration file (for root or
                        unprivileged installation only) (default
                        "/install/software/restart/depos/singularity/in                                                           stallation//etc/singularity/singularity.conf")
  -d, --debug           print debugging information (highest verbosity)
  -h, --help            help for singularity
      --nocolor         print without color output (default False)
  -q, --quiet           suppress normal output
  -s, --silent          only print errors
  -v, --verbose         print additional information
      --version         version for singularity

Available Commands:
  build       Build a Singularity image
  cache       Manage the local cache
  capability  Manage Linux capabilities for users and groups
  completion  Generate the autocompletion script for the specified shel                                                           l
  config      Manage various singularity configuration (root user only)
  delete      Deletes requested image from the library
  exec        Run a command within a container
  help        Help about any command
  inspect     Show metadata for an image
  instance    Manage containers running as services
  key         Manage OpenPGP keys
  oci         Manage OCI containers
  overlay     Manage an EXT3 writable overlay image
  plugin      Manage Singularity plugins
  pull        Pull an image from a URI
  push        Upload image to the provided URI
  remote      Manage singularity remote endpoints, keyservers and OCI/D                                                           ocker registry credentials
  run         Run the user-defined default command within a container
  run-help    Show the user-defined help for an image
  search      Search a Container Library for images
  shell       Run a shell within a container
  sif         Manipulate Singularity Image Format (SIF) images
  sign        Attach digital signature(s) to an image
  test        Run the user-defined tests within a container
  verify      Verify cryptographic signatures attached to an image
  version     Show the version for Singularity

Examples:
  $ singularity help <command> [<subcommand>]
  $ singularity help build
  $ singularity help instance start


For additional help or support, please visit https://www.sylabs.io/docs                                                           /
```
The first issue, which had cropped up previously, related to the absence of suitable cache dir:
```tsql
FATAL:   Failed to create an image cache handle: failed initializing caching directory: couldn't create cache directory /home/ulrich.schnauss/.singularity/cache: mkdir /home/ulrich.schnauss/.singularity: disk quota exceeded
```
which could be resolved by redefining the environmental variable to point towards a new folder on my analysis drive.
```tsql
export SINGULARITY_CACHEDIR="/path/to/my/analysis/folder/singularity/"
```
Then, it was possible to smash the bottle against the hull and pull my first container:
```tsql
wget https://zenodo.org/records/7870950/files/samhorsfield96_ggcaller_latest-2023-04-27-6c0a454e1c5c.sif?download=1
mv https://zenodo.org/records/7870950/files/samhorsfield96_ggcaller_latest-2023-04-27-6c0a454e1c5c.sif?download=1 https://zenodo.org/records/7870950/files/samhorsfield96_ggcaller_latest-2023-04-27-6c0a454e1c5c.sif
```
Tried to build it per the wiki instructions:
```tsql
singularity shell --writable samhorsfield96_ggcaller_latest-2023-04-27-6c0a454e1c5c.sif
FATAL:   no SIF writable overlay partition found in /path/to/my/analysis/folder/singularity/samhorsfield96_ggcaller_latest-2023-04-27-6c0a454e1c5c.sif
```
Thankfully, someone had experienced a similiar issue before and left their documentation up in the annals of the interwebs #shouldersofgiants: https://groups.google.com/a/lbl.gov/g/singularity/c/HhwetRXIfYI/m/6RP4N5zBAAAJ
```tsql
singularity build --sandbox samhorsfield96_ggcaller_latest-2023-04-27-6c0a454e1c5c.sif
Build target 'samhorsfield96_ggcaller_latest-2023-04-27-6c0a454e1c5c.sif' already exists and will be deleted during the build process. Do you                                                            want to continue? [N/y] y
INFO:    Starting build...
INFO:    Creating sandbox directory...
INFO:    Build complete: samhorsfield96_ggcaller_latest-2023-04-27-6c0a454e1c5c.sif
```
That was... it? 100% believing it would not work, proceeded to amend PATH as per the instructions and navigated inside the folder
```tsql
PATH=$PATH:/opt/conda/bin
cd samhorsfield96_ggcaller_latest-2023-04-27-6c0a454e1c5c.sif
#I think the naming/structuring could be improved here for future, on my part
```
 ![](https://media.giphy.com/media/G4GPgzIlcNlr8glbhb/giphy.gif)
```tsql
ggcaller -h
usage: ggcaller [-h] [--graph GRAPH] [--colours COLOURS] [--not-ref]
                [--refs REFS] [--reads READS] [--query QUERY]
                [--codons CODONS] [--kmer KMER] [--save]
                [--data DATA] [--all-seq-in-graph] [--out OUT]
                [--max-path-length MAX_PATH_LENGTH]
                [--min-orf-length MIN_ORF_LENGTH]
                [--score-tolerance SCORE_TOLERANCE]
                [--max-ORF-overlap MAX_ORF_OVERLAP]
                [--min-path-score MIN_PATH_SCORE]
                [--min-orf-score MIN_ORF_SCORE]
                [--max-orf-orf-distance MAX_ORF_ORF_DISTANCE]
                [--query-id QUERY_ID] [--no-filter] [--no-write-idx]
                [--no-write-graph] [--repeat] [--no-clustering]
                [--no-refind] [--identity-cutoff IDENTITY_CUTOFF]
                [--len-diff-cutoff LEN_DIFF_CUTOFF]
                [--family-threshold FAMILY_THRESHOLD]
                [--merge-paralogs]
                [--clean-mode {strict,moderate,sensitive}]
                [--annotation {none,fast,sensitive,ultrasensitive}]
                [--diamonddb ANNOTATION_DB] [--hmmdb HMM_DB]
                [--evalue EVALUE]
                [--truncation-threshold TRUNCATION_THRESHOLD]
                [--search-radius SEARCH_RADIUS]
                [--refind-prop-match REFIND_PROP_MATCH]
                [--min-trailing-support MIN_TRAILING_SUPPORT]
                [--trailing-recursive TRAILING_RECURSIVE]
                [--edge-support-threshold EDGE_SUPPORT_THRESHOLD]
                [--length-outlier-support-proportion LENGTH_OUTLIER_SUP                                                           PORT_PROPORTION]
                [--min-edge-support-sv MIN_EDGE_SUPPORT_SV]
                [--no-clean-edges] [--alignment {core,pan}]
                [--aligner {def,ref}] [--core-threshold CORE]
                [--no-variants] [--ignore-pseduogenes] [--quiet]
                [--threads THREADS] [--version]

Generates ORFs from a Bifrost graph.

optional arguments:
  -h, --help            show this help message and exit

Input/Output options:
  --graph GRAPH         Bifrost GFA file generated by Bifrost build.
  --colours COLOURS     Bifrost colours file generated by Bifrost
                        build.
  --not-ref             If using existing graph, was not graph built
                        exclusively with assembled genomes. [Default
                        = False]
  --refs REFS           List of reference genomes (one file path per
                        line).
  --reads READS         List of read files (one file path per line).
  --query QUERY         List of unitig sequences to query (either
                        FASTA or one sequence per line)
  --codons CODONS       JSON file containing start and stop codon
                        sequences.
  --kmer KMER           K-mer size used in Bifrost build (bp).
                        [Default = 31]
  --save                Save graph objects for sequence querying.
                        [Default = False]
  --data DATA           Directory containing data from previous
                        ggCaller run generated via "--save"
  --all-seq-in-graph    Retains all DNA sequence for each gene
                        cluster in the Panaroo graph output. Off by
                        default as it uses a large amount of space.
  --out OUT             Output directory

ggCaller traversal and gene-calling cut-off settings:
  --max-path-length MAX_PATH_LENGTH
                        Maximum path length during ORF finding (bp).
                        [Default = 20000]
  --min-orf-length MIN_ORF_LENGTH
                        Minimum ORF length to return (bp). [Default =
                        90]
  --score-tolerance SCORE_TOLERANCE
                        Length probability tolerance for shorter
                        alternative start sites. If within
                        tolerance,ggCaller will check if start
                        coverage and BALROG score are both higher in
                        shorter ORF. [Default = 0.2]
  --max-ORF-overlap MAX_ORF_OVERLAP
                        Maximum overlap allowed between overlapping
                        ORFs. [Default = 60]
  --min-path-score MIN_PATH_SCORE
                        Minimum total Balrog score for a path of ORFs
                        to be returned. [Default = 100]
  --min-orf-score MIN_ORF_SCORE
                        Minimum individual Balrog score for an ORF to
                        be returned. [Default = 100]
  --max-orf-orf-distance MAX_ORF_ORF_DISTANCE
                        Maximum distance for graph traversal during
                        ORF connection (bp). [Default = 10000]
  --query-id QUERY_ID   Ratio of query-kmers to required to match in
                        graph. [Default = 0.8]

Settings to avoid/include algorithms:
  --no-filter           Do not filter ORF calls using Balrog. Will
                        return all ORF calls. [Default = False]
  --no-write-idx        Do not write FMIndexes to file. [Default =
                        False]
  --no-write-graph      Do not write Bifrost GFA and colours to file.
                        [Default = False]
  --repeat              Enable traversal of nodes multiple times.
                        [Default = False]
  --no-clustering       Do not cluster ORFs. [Default = False]
  --no-refind           Do not refind uncalled genes [Default =
                        False]

Gene clustering options:
  --identity-cutoff IDENTITY_CUTOFF
                        Minimum identity at amino acid level between
                        two ORFs for clustering. [Default = 0.98]
  --len-diff-cutoff LEN_DIFF_CUTOFF
                        Minimum ratio of length between two ORFs for
                        clustering. [Default = 0.98]
  --family-threshold FAMILY_THRESHOLD
                        protein family sequence identity threshold
                        [Default = 0.7]
  --merge-paralogs      don't split paralogs[Default = False]

Panaroo run mode options:
  --clean-mode {strict,moderate,sensitive}
                        R|The stringency mode at which to run
                        panaroo. Must be one of 'strict', 'moderate'
                        or 'sensitive'. Each of these modes can be
                        fine tuned using the additional parameters in
                        the 'Graph correction' section. strict:
                        Requires fairly strong evidence (present in
                        at least 5% of genomes) to keep likely
                        contaminant genes. moderate: Requires
                        moderate evidence (present in at least 1% of
                        genomes) to keep likely contaminant genes.
                        sensitive: Does not delete any genes and only
                        performes merge and refinding operations.
                        Useful if rare plasmids are of interest as
                        these are often hard to disguish from
                        contamination. Results will likely include
                        higher number of spurious annotations.

Panaroo gene cluster annotation options:
  --annotation {none,fast,sensitive,ultrasensitive}
                        Annotate genes using diamond default (fast),
                        diamond sensitive (sensitive) or diamond and
                        HMMscan (ultrasensitive). Specify 'none' if
                        annotation not required.Default = 'fast'
  --diamonddb ANNOTATION_DB
                        Diamond database. Defaults are 'Bacteria' or
                        'Viruses'. Can also specify path to fasta
                        file for custom database generation
  --hmmdb HMM_DB        HMMER hmm profile file. Default is Uniprot
                        HAMAP. Can alsospecify path to pre-built hmm
                        profile file generated using hmmbuild
  --evalue EVALUE       Maximum e-value to return for DIAMOND and
                        HMMER searches during annotation[Default =
                        0.001]
  --truncation-threshold TRUNCATION_THRESHOLD
                        Sequences in a gene family cluster below this
                        proportion of the length of thecentroid will
                        be annotated as 'potential
                        pseudogene'[Default = 0.8]

Panaroo gene refinding options:
  --search-radius SEARCH_RADIUS
                        the distance in nucleotides surronding the
                        neighbour of an accessory gene in which to
                        search for it
  --refind-prop-match REFIND_PROP_MATCH
                        the proportion of an accessory gene that must
                        be found in order to consider it a
                        match[Default = 0.2]

Panaroo graph correction stringency options:
  --min-trailing-support MIN_TRAILING_SUPPORT
                        minimum cluster size to keep a gene called at
                        the end of a contig
  --trailing-recursive TRAILING_RECURSIVE
                        number of times to perform recursive trimming
                        of low support nodes near the end of contigs
  --edge-support-threshold EDGE_SUPPORT_THRESHOLD
                        minimum support required to keep an edge that
                        has been flagged as a possible mis-assembly
  --length-outlier-support-proportion LENGTH_OUTLIER_SUPPORT_PROPORTION
                        proportion of genomes supporting a gene with
                        a length more than 1.5x outside the
                        interquatile range for genes in the same
                        cluster.Genes failing this test will be re-
                        annotated at the shorter length[Default =
                        0.1]
  --min-edge-support-sv MIN_EDGE_SUPPORT_SV
                        minimum edge support required to call
                        structural variants in the presence/absence
                        sv file
  --no-clean-edges      Turn off edge filtering in the final output
                        graph.[Default = False]

Alignment options:
  --alignment {core,pan}
                        Output alignments of core genes or all genes.
                        Options are 'core' and 'pan'. [Default =
                        'None'
  --aligner {def,ref}   Specify an aligner. Options: 'ref' for
                        reference-guided MSA and 'def' for default
                        standard MSA
  --core-threshold CORE
                        Core-genome sample threshold.[Default = 0.95]
  --no-variants         Do not call variants using SNP-sites after
                        alignment.[Default = False]
  --ignore-pseduogenes  Ignore ORFs annotated as 'potential
                        pseudogenes' in alignment[Default = False]

Misc. options:
  --quiet               suppress additional output[Default = False]
  --threads THREADS     Number of threads to use. [Default = 1]
  --version, -v         show program's version number and exit
```
 ![](https://media.giphy.com/media/3o85fV0sAw01OSN8Bi/giphy.gif)

 So there you have it: what took the bones of a day to install through the refiners fire that is Conda dependency hell, resolved in 10 mins. Still need to suss out how to use Docker images where the devs haven't already generated a Singularity file...
