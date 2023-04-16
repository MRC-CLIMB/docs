# CLIMB-BIG-DATA: Metagenomics in Brum

In this tutorial we will look at some metagenomics sequences that were sequenced from DNA extracted from the Worcester and Birmingham Canal at the University of Birmingham. In this case the data was generated on the Oxford Nanopore sequencing platform.

![image.png](https://s0.geograph.org.uk/geophotos/03/42/44/3424466_b680b84c_original.jpg)

## Setting up the environment

When working on CLIMB-BIG-DATA with Conda, always work in a new Conda environment. You will not be able to add software to the base environment.

We speed the process along by using `mamba`, a drop-in replacement for `conda` to create a new environment.

Our environment will be called `metagenomics-tutorial`.
   
In order for this tutorial to function correctly within JupyterHub, also install `ipykernel`.


```python
!mamba create -y -n metagenomics-tutorial ipykernel
```

Next, we will need the following software:

   * `kraken2` - a taxonomic profiler for metagenomics data
   * `krona` - an interactive visualiser for the output of Kraken2
   * `krakentools` - some useful scripts for manipulating Kraken2 output


```python
!mamba install -y kraken2 krona blast krakentools 
```

Krona reminds us to run `ktUpdateTaxonomy.sh` before it can be used.


```python
!ktUpdateTaxonomy.sh
```

Let's grab the CanalSeq data.

This is available from the [following link](https://loman-labz-public-datasets.s3.climb.ac.uk/canalseq.fasta).

Note that this link is served via the CLIMB S3 service. You can serve your own public data the same way simply by creating a public S3 bucket and uploading files to it!


## Running Kraken2

You can test `kraken2` was installed correctly by running it with no command-line options.


```python
!kraken2
```

Pre-computed Kraken2 databases are available on the `/shared/public` file system within CLIMB-BIG-DATA. These databases are downloaded from [Ben Langmad's publicly available Kraken2 indexes page](https://benlangmead.github.io/aws-indexes/k2). These datasets are updated monthly and we will keep the latest versions available.

The `/shared/public` area is designed to store frequently used, important databases for the microbial genomics community. Please contact us with suggestions for other databases you would like to see here.

We can take a look at the databases that are available, and their sizes:


```python
!du -h -d1 /shared/public/db/kraken2
```

    7.5G	/shared/public/db/kraken2/k2_pluspf_08gb
    9.1G	/shared/public/db/kraken2/k2_minusb
    556M	/shared/public/db/kraken2/k2_viral
    69G	/shared/public/db/kraken2/k2_pluspf
    7.5G	/shared/public/db/kraken2/k2_standard_08gb
    15G	/shared/public/db/kraken2/k2_pluspf_16gb
    65G	/shared/public/db/kraken2/k2_standard
    15G	/shared/public/db/kraken2/k2_standard_16gb
    264G	/shared/public/db/kraken2/downloads
    7.6G	/shared/public/db/kraken2/k2_pluspfp_08gb
    15G	/shared/public/db/kraken2/k2_pluspfp_16gb
    145G	/shared/public/db/kraken2/k2_pluspfp
    619G	/shared/public/db/kraken2


We can run Kraken2 directly within this JupyterHub notebook which is running in a container. A standard container has 8 CPu cores and 64Gb of memory. Kraken2 doesn't run well unless the database fits into memory, so we can use one of the smaller databases for now such a `k2_standard_16gb` which contains archaea, bacteria, viral, plasmid, human and UniVec_Core sequences from RefSeq, but subsampled down to a 16Gb database. This will be fast, but we trade off specificity and sensitivity against bigger databases.


```python
!kraken2 --threads 8 \
   --db /shared/public/db/kraken2/k2_standard_16gb \
   --output canalseq.hits.txt \
   --report canalseq.report.txt \
   canalseq.fasta
```

    Loading database information... done.
    37407 sequences (91.32 Mbp) processed in 4.876s (460.3 Kseq/m, 1123.76 Mbp/m).
      12486 sequences classified (33.38%)
      24921 sequences unclassified (66.62%)


About a third of sequences were classified and two-thirds were not.

The `canalseq.report.txt` gives a human-readable output from Kraken2.


```python
!cat canalseq.report.txt
```

People worry about getting Leptospirosis if they swim in the canal. Any evidence of _Leptospira sp._ in these results?


```python
!grep Leptospira canalseq.report.txt
```

      0.03	10	0	O	1643688	          Leptospirales
      0.03	10	0	F	170	            Leptospiraceae
      0.02	9	2	G	171	              Leptospira
      0.01	2	2	S	173	                Leptospira interrogans
      0.00	1	1	S	28182	                Leptospira noguchii
      0.00	1	1	S	28183	                Leptospira santarosai
      0.00	1	1	S	1917830	                Leptospira kobayashii
      0.00	1	1	S	2564040	                Leptospira tipperaryensis
      0.00	1	0	G1	2633828	                unclassified Leptospira
      0.00	1	1	S	1513297	                  Leptospira sp. GIMC2001


## Eek!

It's easier to look at Kraken2 results visually using a Krona plot:


```python
!ktImportTaxonomy -q 2 -t 3 canalseq.hits.txt -o KronaReport.html
```

We can look at the Krona report directly within the browser by using the file navigator to the left - open up the KronaReport.html within the `shared-team` directory where we are working. Click around the Krona report to see what is in there.

With the `extract_kraken_reads.py` script in `krakentools` we can quite easily extract a set of reads that we are interested in for further exploration: perhaps to use a more specific method like BLAST against a large protein database, or to extract for de novo assembly.


```python
!extract_kraken_reads.py -k canalseq.hits.txt -s canalseq.fasta -r canalseq.report.txt -t 171 -o leptospira.fasta --include-children
```


```python
!cat leptospira.fasta
```

If you wished you could go and take these 9 reads and BLAST them over at NCBI-BLAST. There is also the `nr` BLAST database available on CLIMB-BIG-DATA if you wanted to run `blastx` on them. The BLAST databases are found in `/shared/team/db/blast`.

It was a bit disappointing that only 33% of the reads in our dataset were assigned. We could try a much bigger database than `k2_standard_16hb` such as `k2_pluspfp` which contains protozoal and fungal sequences as well as the other ones. 

To do this we will need to use the Kubernetes cluster in CLIMB-BIG-DATA. With the Kubernetes cluster we can run much bigger tasks requiring much more CPU power, or more memory than in a notebook container like this.

The easiest way to get started with Kubernetes is using Nextflow.

When you run a Nextflow script it will automagically use Kubernetes.

There are a few Kraken2 Nextflow scripts, the simplest one I have found is [metashot/kraken2](https://github.com/metashot/kraken2). This will also do a few extra steps like running Bracken.

If you wanted to run Kraken2 through Nextflow the same way as before you could run:


```python
!nextflow run metashot/kraken2 \
  -c /etc/nextflow.config \
  --reads canalseq.fasta \
  --kraken2_db /shared/public/db/kraken2/k2_standard_16gb \
  --read_len 100 \
  --outdir canalseq-standard \
  --single_end
```

But - and for our final trick - we would like to use a much bigger database `k2_pluspfp`. We can ask Nextflow to give us 200 gigabytes of RAM when running this container, to ensure this database fits in memory (it is 145Gb). We could also ask for a lot more CPUs to speed things along further!


```python
!nextflow run metashot/kraken2 \
  -c /etc/nextflow.config \
  --reads canalseq.fasta \
  --kraken2_db /shared/public/db/kraken2/k2_pluspfp \
  --read_len 100 \
  --outdir canalseq-pluspfp \
  --single_end \
  --max_memory 200.G \
  --max_cpus 64 \
```

Ah, OK this gives 80% assignment! We can go and take a look at this in Krona again.


```python
!ktImportTaxonomy -t 5 -m 3 -o krona-canalseq-pluspfp.html canalseq-pluspfp/kraken2/canalseq.kraken2.report
```

       [ WARNING ]  Score column already in use; not reading scores.
    Loading taxonomy...
    Importing canalseq-pluspfp/kraken2/canalseq.kraken2.report...
       [ WARNING ]  The following taxonomy IDs were not found in the local
                    database and were set to root (if they were recently added to
                    NCBI, use updateTaxonomy.sh to update the local database):
                    42857
    Writing krona-canalseq-pluspfp.html...

