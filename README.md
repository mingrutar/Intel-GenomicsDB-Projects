## GenomicsDB Performance Profiling

GenomicsDB Profiler is a self-start project for profiling GenomicsDB performance, see GenomicsDB wiki at https://github.com/Intel-HLS/GenomicsDB

![png](docs/gdb_profiler_all.jpg)

The profiling targets are GenomicsDB utilities <b>‘vcf2tiledb’</b> (data loader) and <b>‘gt_mpi_gather’</b> 
(data retriever). Both utilities require a json config file as input, see detals ay https://github.com/Intel-HLS/GenomicsDB. 
The initial motivation was to automatically generate the labor-intense-to-craft JSON input files that were needed for finding the 
optimal values for loader (vcf2tiledb) input parameters, such as various buffer sizes,  and combination of the parameters. 
Over the time, the profiler has been improved to generate hundreds JSON configure files with various running configurations; launch 
profiling on multiple nodes and visualize profiling results in various ways. 

A single run of vcf2tiledb or gt_mpi_gather is a very simple task. However managing thousands runs with similar configurations on many nodes become very challenging. A single run of vcf2tiledb often take hours. For efficience, the code needs to enable quick test, dry run, reuse existing DB, automatically process results and so on. To address all the issues, GenomicsDB Profiler has a complex name scheme for JSON configuration files and test result files to avoid conflicts and various mechanisn for quick testing. See [GenomicsDB Profiling Document](docs/GenomicsDBProfiling.pdf) for details. 

