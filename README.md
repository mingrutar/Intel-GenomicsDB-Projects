Wrote the following projects:
* [GenomicsDB Performance Profiling](#perf)
* [Docker for Building GenomicsDB utilities](#gdb_builder)
* [GenomicsDB Wrapping Dockers](#util_dockers)

<a id='perf'></a>
### GenomicsDB-Benchmark

GenomicsDB Profiler, or Genomics-Benchmark as late name,d is a self-start project for profiling performance of GenomicsDB utillities, see GenomicsDB wiki at https://github.com/Intel-HLS/GenomicsDB for details about GenomicsDB utilities.

![png](images/gdb_profiler_all.jpg)

The profiling targets are GenomicsDB utilities <b>‘vcf2tiledb’</b> (data loader) and <b>‘gt_mpi_gather’</b> 
(data retriever). Both utilities require a json config file as input, see detals ay https://github.com/Intel-HLS/GenomicsDB. Initially the work was manually creating a config file then profile a GenomicsDB utility against the file. I created GenomicsDB Profiler that automatically generates different config files, then profile GenimicsDB utilities with the config files. I have profiled __2000+__ in a batch. Technically speaking, the number of runs in a batch is unlimited. GenomicsDB Profiler also has __visualization__ for profiling results, 2-D and 3-D results comparisons and query geno position distribution.

A single run of vcf2tiledb or gt_mpi_gather is a very simple task. However managing thousands runs with similar configurations on many nodes become very challenging. To avoid conflicts, GenomicsDB Profiler has a complex name scheme for JSON configuration files, DB names and result file names. Because running the loader often takes hours, the code enable quick test, dry run, option of reuse existing DB, automatically process results and so on.

<a id="gdb_builder"></a>
### Docker for Building GenomicsDB Utilities
This is a self-started project. Building GenomicsDB could be quite time consuming, see GenomicsDB wiki. The project offers scripts for building docker image and run the docker. The created docker image is ready for building GenomicsDB utilities. Running the run script will launch a docker contain that pulls fresh source code from [GenomicsDB github repo](https://github.com/Intel-HLS/GenomicsDB) and builds the GenomicsDB utilities. By default, the container pulls from master branch. However it will pull code from the brach if a branch name is passed in. 

The source code of making the docker could be found at GenomicsDB github branch "[GenomicsDB_dockers](https://github.com/Intel-HLS/GenomicsDB/tree/GenomicsDB_dockers/docker)"

The docker image "genomicsdb_builder" is published at https://hub.docker.com/r/intelhlsgenomicsdb/genomicsdb_builder/.

<a id="util_dockers"></a>
### Docker for Building GenomicsDB utilities and Dockers for GenomicsDB utilities

Two GenomicsDB utility dockers are available: importer and querier, see source code at GenomicsDB github branch "[GenomicsDB_dockers](https://github.com/Intel-HLS/GenomicsDB/tree/GenomicsDB_dockers/docker)"

##### How to build dockers
To build importer docker, run:
<code>$ bash docker_src/VCFImporter_builder/build_docker.bash </code>

To build querier docker, run:
<code>$ bash docker_src/GDBQuerier_builder/build_docker.bash </code>

##### How to use the dockers

Assum we have the following directory and files layout:
```
ROOT
  |_ mapping_data
        |_ customer_vid.json            # vid mapper file
      [ |_ callsets.json  ]             # callsets file. if not preset, code will generates one
  |_ Homo_sapiens_assembly19.fasta      # reference file
  |_ vcfs
        |_ xxx.gz                       # block compressed VCF file
        |_ xxx.gz.tbi                   # index file
        |_ ....
  |_ genomicsdb_ws                      # where genomicsDB will be
```

See https://github.com/Intel-HLS/GenomicsDB/wiki/Importing-VCF-data-into-GenomicsDB for more details on how to make xxx.gz and xxx.gz.tbi from xxx.vcf file.

For syntax,
```
$ docker run vcf_importer:0.6
or
$ docker run genomicsdb_querier:0.9.2-93da4b0-0.5
```
##### example of importing VCF files into GenomicsDB
```
$ docker run -v $ROOT:/myenv vcf_importer:0.6 vcf_importer.py -R /myenv/Homo_sapiens_assembly19.fasta -V /myenv/mapping_data/customer_vid.json  -C /myenv/mapping_data/ -o /myenv/genomicsdb_ws/ -i /myenv/vcfs --range 1:1-249250621
```

<b> example of querying positions with GenomicsDB</b>
```
$ docker run -v $ROOT:/myenv genomicsdb_querier:0.9.2-93da4b0-0.5 genomicsdb_querier.py -R /myenv/Homo_sapiens_assembly19.fasta -V /myenv/mapping_data/customer_vid.json -C /myenv/mapping_data/callsets.json -o /myenv/genomicsdb_ws/ --print-AC --positions /examples/query_1_10_f1.json
```

/examples/query_1_10_f1.json is an example query position file. You can find more by

```
$ docker run genomicsdb_querier:0.9.2-93da4b0-0.5 ls /examples
```

The docker images, "vcf_importer", "vcf_combiner" and "genomicsdb_querier" are published at https://hub.docker.com/u/intelhlsgenomicsdb/
