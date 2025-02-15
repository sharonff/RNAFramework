## [2.8.2] - 2022-09-01
### Changed
- Fixed a bug in rf-json2rc introduced in the last version, causing the last base of a transcript to be omitted
- Fixed the count of covered chromosomes in the rf-count-genome output
- Fixed strandedness of paired-end reads in rf-count-genome
- Fixed handling of RT-stops falling before the current memory block
- Fixed a bug in rf-fold introduced in the last version, causing pseudoknots of lenght 1 bp to be discarded
- Fixed a bug in rf-fold during the evaluation of pseudoknotted helices when overlapping with lonely pairs in the MFE structure
- Fixed a naming issue in rf-fold temporary pseudoknot constraint files
- Fixed parsing of Bowtie logs by rf-map, which would have caused rf-map to erroneously report 0% mapped reads with newer versions of Bowtie due to a change in the output log format
- Fixed rf-structextract to avoid redundancies in the output motifs

## API Changes
- Added the updateBytewise() method to RF::Data::IO::RC 
- Updated the Core::Mathematics::isreal() function to use Scalar::Util::looks_like_number() for improved performances

## [2.8.0] - 2022-05-31
### Added
- Added the rf-structextract module to automatically extract relevant RNA structure elements from larger transcripts (e.g. low reactivity - low Shannon regions, structures with free energy lower than expected by chance, etc.)
- Added the rf-count-genome module to process genome-level SAM/BAM alignments

### Changed
- Fixed a bug in rf-correlate when getting the positions of the bases being covered, for ratio calculation
- Added the "-c" (or "--constraints") parameter to rf-fold to allow providing base-pair constraints
- Changed rf-fold logic to perform pseudoknot prediction as the last step, after having built the secondary structure model
- Fixed the "--vienna-rnafold" parameter of rf-mutate to take an argument
- Fixed a bug in rf-peakcall causing the program to crash with 0 read count in the IP sample
- ADDED the "extract" command to rf-rctools to allow extracting transcripts, provided a BED or GTF annotation
- Changed the rf-wiggle module to process RC files in blocks, hence avoiding to keep in memory the full RC entry (useful for genome-level RC files)

## API Changes
- Added a third argument to the Core::Statistics::pearson() function, to allow removing NaN values, outliers, or capping values to a maximum prior to calculating the correlation
- Added the Core::Utils::isGzipped() function to evaluate whether a file is gzipped
- Fixed the read() method of the Data::IO class to automatically skip empty lines
- Fixed a bug in the \_findFormat() method of the Data::IO::Sequence class causing the whole file to be loaded in memory to guess the file format, rather than just a few lines
- Renamed the \_validate() method of the Interface::Generic class to \_makeTmpDir()
- Added the Interface::Aligner::STAR class, in preparation for the next release of RNAFramework
- Added the id() and revcomp() methods to the RF::Data::RC class
- Changed the compression/decompression of the sequence in the RF::Data::IO::RC and RF::Data::IO::MM modules for increased speed
- Added the readBytewis() and writeBytewise() methods to the RF::Data::IO::RC class to allow reading/writing count/coverage data at specific positions of a given transcript/chromosome (useful for the processing of genome-level RC files)

## [2.7.2] - 2021-07-22
### Added
- Added function "extract" to rf-rctools to extract a set of regions specified in a BED file, from an input RC file
- Added the rf-duplex module to enable the analysis of direct RNA-RNA interaction mapping experiments (COMRADES, SPLASH, PARIS, etc.)
- Added the "--only-mut" parameter to rf-count, to allow counting only specific mutation events

### Changed
- Fixed rf-count to use Core::Process::Queue instead of threads, which resulted in a significant speedup of parallel BAM processing

### API Changes
- The Core::Process and Core::Process::Queue modules now use Storable instead of pipes for IPC, to allow children to return even complex data structures
- Added several new methods to Data::IO for enabling control over the filehandle (seek, tell, eof, goToEof), as well as the forceReopenFh method to allow a Data::IO object that has been cloned in a thread (or child process) to be fully independent

## [2.7.1] - 2021-04-03
### Added
- Added parameter "-mcp" (or "--meta-coding-plot") to rf-peakcall to generate protein-coding-only meta-gene plots, by aligning the TSS, start codon, stop codon, and TES
- Added the rf-motifdiscovery module to perform motif discovery from RIP peaks
- Added the rf-json2rc module to post-process DRACO JSON output files into RC format
- Added support for direct comparison of RC files in rf-correlate
- Every RNA Framework module will now notify the user if a new version is available

### Changed
- Fixed a bug in the way rf-norm and rf-wiggle handled reactive bases from IUPAC codes

### API Changes
- The Graphics::Object::Ruler can now take user-defined labels
- Fixed the nt2iupac function of Data::Sequence::Utils

## [2.7.0] - 2021-02-27
### Added
- Added parameter "-l" (or "--log-transform") to rf-combine (reactivity profiles will be averaged after having been log-transformed)
- Added rf-wiggle support for XML files generated by rf-modcall
- Added the rf-mutate tool to design structure mutants/rescues
- Added rf-count parameters "-ld" (or "--left-deletion") and "-rd" (or "--right-deletion") to re-align deletions of multiple consecutive nucleotides and "-la" (or "--left-align") to re-align ambiguously aligned deletion to their left-most position
- Added rf-count parameter "-mv" (or "--max-coverage") to downsample reads to a target coverage on transcript
- Added rf-count parameter "-pn" or ("--primary-only") to only process primary alignments
- Added rf-count parameter "-mm" or ("--mutation-map") to generate MM files for downstream processing with DRACO (Deconvolution of RNA Alternative COnformations)
- Added rf-combine and rf-correlate parameter "-S" (or "--spearman) to use Spearman rather than Pearson to evaluate correlation
- Added rf-correlate "-i" parameter (or "--ignore-sequence") to ignore sequence differences (e.g. due to SNVs) between compared transcripts
- Added support for direct comparison of XML files with different file names to rf-correlate
- Added rf-peackcall "-l" (or "--whitelist") parameter to restrict the analysis to certain transcripts only

### Changed
- Fixed rf-count to handle sequence IDs containing slashes ("/"), causing errors at later stages
- Coordinates in mask files (for rf-count) need now to be specified as 0-based
- Fixed a bug in rf-count causing non-ambiguously mapped deletions to be discarded under certain conditions
- Made right realignment of ambiguously aligned deletion the default in rf-count and removed the "-ra" (or "--right-align") parameter of rf-count
- The "-ds" (or "--discard-shorter") parameter of rf-count can now be set to "MEDIAN" to automatically use the median read length
- Changed the default 3' adapter sequence for the "-ca3" (or "--cutadapt-3adapter") of rf-map to the sequence of the NEBNext Small RNA 3' adapter
- Fixed a bug in rf-norm causing regions with insufficient coverage to be erroneously reported as 0 reactivities

## [2.6.9] - 2020-01-18
### Added
- Added possibility to only combine reactivity profiles exceeding a given correlation cutoff in rf-combine. Parameters "-c" (or "--min-correlation) and "-m" (or "--min-values") respectively control the correlation threshold and the minimum number/fraction of covered bases to calculate correlation
- Added the rf-correlate tool to calculate pairwise correlations between structure probing experiments
- Added parameter "-la" (or "--list-annotations") to rf-index to list available UCSC data tables containing gene annotations
- Added parameters "-H" (or "--host") and "-P" (or "--port") to rf-index to allow specifying and alternative UCSC server hostname and port
- Added parameters "-ctn" (or "--cutadapt-trim-N") and "-cmn" (or "--cutadapt-max-N") to rf-map to respectively allow trimming Ns from read ends and discarding reads containing more than a specified number of Ns
- Added parameter "-ds" (or "--discard-shorter") to rf-count to discard reads shorter than a given length (excluding clipped bases)
- Added the possibility to specify one or more transcripts to visualize with rf-rctools "view"
- Added a check to rf-norm to allow a maximum window size of 30,000 nt when dynamic windowing is enabled
- Added the "-g" (or "--img") and "-mp" (or "--meta-plot") parameters to rf-peakcall to respectively allow generating normalized gene coverage plots or meta-gene plots of coverage/peaks distribution
- Added the "-r" (or "--refine"), "-x" (or "--relaxed") and "-s" (or "--summit") parameters to rf-peakcall to allow refining peak boundaries and to allow generating a BED file with the coordinates of peak summits

### Changed
- Fixed rf-combine to report combined score and ratio when combining rf-modcall XML files
- Changed rf-count to ignore N-only deletions when calculating edit distance
- Removed the "-nm" (or "--no-mapped-count") parameter of rf-count (now total read count is automatically computed as the sum of reads covering each transcript in the dataset)
- Changed the "-l" (or "--list") parameter of rf-index to "-lp" (or "--list-prebuilt")
- Fixed a bug in rf-map causing mapping to fail with filenames containing dots in paired-end experiments
- Cutadapt v2.1 or greater is now required (multithread support)

### API changes
- Added the mround() function to Core::Mathematics to allow rounding to the nearest multiple
- Added support for an extra "structure" tag in RNA Framework's XML files, allowing storing dot-bracket structures inside XML reactivity files

## [2.6.8] - 2019-02-20
### Added
- Added flag "--max-mutation-rate" to rf-norm to allow excluding bases with mutation rate > than a user-defined threshold (for MaP experiments)

### API changes
- Changed rf-norm algorithm to exclude non-covered bases and bases with too high mutation rates from normalization

## [2.6.7] - 2018-11-27
### Added
- Added flag "-i" to rf-compare to allow comparison between structures with different sequences (useful in case of SNVs)
- Added flags "-ksl" and "-kin" to allow using a different set of folding parameters for pseudoknots prediction
- Added RF Norm support for dynamic windows

### Changed
- Fixed a bug in rf-fold causing the software to not report pseudoknots even if present (in certain windows the pseudoknot was predicted as a regular helix, causing its rejection due to the requirement of being present in at least 50% of the analyzed windows)

### API changes
- Replaced the File::Path rmtree() function with a thread-safe version (introduced in the Core::Utils library)
- Added a third parameter to the pearson() and spearman() functions from Core::Statistics to allow handling of arrays containing NaNs (when TRUE, only non-NaN elements from both arrays are kept)
- Modified the pearson() and spearman() functions from Core::Statistics to only return the correlation coefficient when called in scalar context
- Added the split parameter to the helices() method from Data::Sequence::Structure (when TRUE, allows splitting helices with single-nucleotide bulges)
- Fixed a bug causing the RF::Data::IO::XML module to fail reading XML files when a newline was missing between values and tags
- Fixed a bug in the rmpseudoknots() function of RNA::Utils causing any scoring subroutine passed to the function to be overriden by the Core::Mathematics::sum() function

## [2.6.6] - 2018-10-19
### Added
- Added mask file support to rf-count

### Changed
- Fixed a bug in rf-fold causing the software to get stuck when generating graphical reports for transcripts with only 0/NaN reactivity values (thanks to Uciel Pablo Chorostecki for reporting the bug)

## [2.6.5] - 2018-08-09
### Added
- Added rf-rctools utility for RC files manipulation

### API changes
- API modified to use HTTP::Tiny (CORE) instead of LWP::UserAgent

## [2.6.1] - 2018-06-12
### Added
- Added mutated reads statistics to rf-count output

### Changed
- Fixed a bug in rf-fold causing folding to fail with pseudoknotted structures under windowed folding mode
- Fixed a bug in rf-fold causing the software to crash when the first/last windows are < 50 nt

### API changes
- Added haszero() function to Core::Mathematics
- Added forceyrange parameter to Graphics::Object::Yaxis objects to force y-scale over the maximum/minimum Y values in the dataset
- Modified RF::Data::RC module to handle reference sequences with degerated bases

## [2.6] - 2018-05-17
### Added
- Introduced rf-map support for quality-based trimming of reads
- Introduced new rf-count parameters to enable quality filtering of reads/mappings
- Introduced rf-count support for insertions
- Introduced rf-count support for the re-alignment of ambiguously aligned deletions in SHAPE-MaP experiments
- Added sampling of Zuker suboptimal structures to rf-fold pseudoknots detection algorithm

### API changes
- Added the $VERSION variable to the Core::Utils package

## [2.5.5] - 2018-04-25
### Changed
- Added rf-map support for gzipped FastQ files (function request issued by Omar Wagih)

## [2.5.4] - 2018-03-15
### Changed
- Modified all modules to avoid use of Thread::Queue (in some cases we encountered a deadlock, especially with rf-fold, that should be solved now)
- Fixed 90% Wisorizing to esclude values below the 5<sup>th</sup> percentile as outliers
- Now rf-compare can generate comparison plots of structures in SVG format (reference vs. predicted)

### API changes
- Now control over errors verbosity is granted through any of the following environment variables: "verbose", "verbosity", "VERBOSE", or "VERBOSITY"
- We have now avoided use of XML::LibXML for RNA Framework's XML file input handling (thread unsafe)

## [2.5.3] - 2018-02-04
### Changed
- Modified rf-fold to avoid partition function from being computed when not performing windowed folding (or when explicitly required by -dp or -sh)

### API changes
- Modified the rmpseudoknots() function to take an array of array refs of 3 elements [i, j, score], and a code ref to use to compute overall score of the helix

## [2.5.2] - 2018-01-29
### Added
- Rewritten rf-fold engine to make it faster (and to make code maintenance easier)
- Fixed an issue in rf-fold causing a crash when no reactivity data was available for a given pseudoknotted helix
- Fixed a bug in rf-fold causing the software to report unfolded structures for RNAs with length < than the allowed minimum window length (50 nt) in windowed folding mode
- Fixed a severe bug in rf-fold in windowed mode causing the program to crash due to the presence of incompatible base-pairs with probability >= 0.99
- Introduced in rf-fold the generation of SVG graphical summaries (reactivity data, Shannon entropy, base-pairing probabilities and MEA structure)

### API changes
- Data::IO::XML has been replaced by a Data::XML class for XML construction, that is then passed to a generic Data::IO object for writing
- Introduced Graphics libraries for SVG graphics generation (currently supports bar plots, paths, and RNA arc plots)

## [2.5.1] - 2018-01-07
### Added
- Introduced CHANGELOG
- Added back support for Siegfried *et al*., 2014 normalzation method (now the method allows also the analysis of experiments lacking a denatured sample)

## [2.5.0] - 2018-01-05
### Added
- Introduced support for pseudoknots
- Introduced rf-jackknife for slope/intercept grid search
- Changed RC format (from now on backward compatibility is supported, but RC files created before this release are no longer supported)
- rf-peakcall now supports the analysis of experiments lacking control/input sample

### Removed
- Temporarily removed Siegfried *et al*., 2014 normalzation method support
