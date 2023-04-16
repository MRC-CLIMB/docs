# Using Nextflow

## What is nextflow and how does it fit into the CLIMB-BIG-DATA platform?

In their words, _"[Nextflow](https://nextflow.io) enables scalable and reproducible scientific workflows using software containers. It allows the adaptation of pipelines written in the most common scripting languages."_

We've made Nextflow a first class citizen in CLIMB-BIG-DATA. You'll find an up-to-date stable version pre-installed in your notebook server and, more importantly, pre-configured to take advantage of our scalable kubernetes infrastructure.

## How can I start using nextflow?

[Create a notebook server](quick-start.md) and start a [terminal session](using-the-terminal.md). Now type:

```console
jovyan:~$ nextflow -v
[...]
nextflow version 23.04.0.5857
```

Your version may be newer than the above, but the point is that nextflow is pre-installed and you should usually use this binary rather than downloading another. You don't need to follow the installation instructions in the nextflow docs.

## Tutorial using nf-core

[nf-core](https://nf-co.re/) is a community-curated set of analysis pipelines that use nextflow. We'll try running nf-core/rnaseq as an example, to demonstrate some features of how nextflow is configured to work on CLIMB.

We'll be using `-profile test`, which includes links to appropriate data. As a result, we'll only need to specify `--outdir` for now.

```console
jovyan:~$ nextflow run nf-core/rnaseq -profile test --outdir nfout
[...]
[-        ] process > NFCORE_RNASEQ:RNASEQ:ALIGN_STAR:BAM_SORT_STATS_SAMTOOLS:BAM_STATS_SAMTOOLS:SAMTOOLS_IDXSTATS -
[c5/3af707] process > NFCORE_RNASEQ:RNASEQ:QUANTIFY_STAR_SALMON:SALMON_QUANT (RAP1_UNINDUCED_REP1)                 [ 50%] 1 of 2
[-        ] process > NFCORE_RNASEQ:RNASEQ:QUANTIFY_STAR_SALMON:SALMON_TX2GENE                                     -
[-        ] process > NFCORE_RNASEQ:RNASEQ:QUANTIFY_STAR_SALMON:SALMON_TXIMPORT                                    -
[-        ] process > NFCORE_RNASEQ:RNASEQ:QUANTIFY_STAR_SALMON:SALMON_SE_GENE                                     -
[-        ] process > NFCORE_RNASEQ:RNASEQ:QUANTIFY_STAR_SALMON:SALMON_SE_GENE_LENGTH_SCALED                       -
[-        ] process > NFCORE_RNASEQ:RNASEQ:QUANTIFY_STAR_SALMON:SALMON_SE_GENE_SCALED                              -
[-        ] process > NFCORE_RNASEQ:RNASEQ:QUANTIFY_STAR_SALMON:SALMON_SE_TRANSCRIPT                               -
[-        ] process > NFCORE_RNASEQ:RNASEQ:DESEQ2_QC_STAR_SALMON                                                   -
[9f/b3b437] process > NFCORE_RNASEQ:RNASEQ:BAM_MARKDUPLICATES_PICARD:PICARD_MARKDUPLICATES (RAP1_UNINDUCED_REP2)   [  0%] 0 of 2
[-        ] process > NFCORE_RNASEQ:RNASEQ:BAM_MARKDUPLICATES_PICARD:SAMTOOLS_INDEX
[...etc...]

```

### What is going on?

The pipeline is executing interdependent process in parallel, using the [Kubernetes executor](https://www.nextflow.io/docs/latest/executor.html#kubernetes). That is to say, rather than running inside your notebook container directly, new Kubernetes pods are being created on the fly and spinning up containers for each process.

Open another terminal tab alongside, while the workflow is still executing, and try:

```console
jovyan:~$ kubectl get pods
NAME                                         READY   STATUS              RESTARTS   AGE
jupyter-demouser-2eclimb-2dbig-2ddata-2dd   1/1     Running             0          12m
nf-0e32425fc6d3dd42c9a3cbb8dd3ccc8c          0/1     Pending             0          4s
nf-6171723bfd88a03f1417e2aead99f180          0/1     Terminating         0          12s
nf-0e69c114a366b717ad115431277c01d7          1/1     Running             0          9s
nf-31f1f1f534f51863f2e19320ca7447e0          0/1     ContainerCreating   0          4s
nf-75dc1d907794e300ff2117a49be85c63          0/1     Pending             0          4s
nf-8491621dd73c44811c49bee448771ae5          0/1     Completed           0          13s
nf-9aad711fc9476c27e510b9402e3089d5          0/1     ContainerCreating   0          3s
nf-12656c698dd83e7dc2a31e3c88818227          0/1     ContainerCreating   0          4s
nf-a57472aaef0073c9e77a0a7e6001a849          0/1     ContainerCreating   0          3s
nf-f8d94c52e9120b7b8ba117d530f77c3a          0/1     Completed
```

[kubectl](https://kubernetes.io/docs/reference/kubectl/) is the kubernetes command line tool. It's also pre-installed in the CLIMB notebook environment, and pre-configured with credentials that map to a ServiceUser for your team.

This service user has certain privileges within your _[namespace](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/)_, or in other words an isolated part of our cluster created specifically for your team. The command `kubectl get pods` above is returning a list of pods currently running in your namespace. You'll see your notebook server (`jupyter-demouser-2eclimb-2dbig-2ddata-2dd`) and a number of nextflow pods that are running workflow process containers. These will be in various states as they execute and then disappear.

Once the workflow has finished, run the above command again:

```console
jovyan:~$ kubectl get pods
NAME                                         READY   STATUS    RESTARTS   AGE
jupyter-demouser-2eclimb-2dbig-2ddata-2dd   1/1     Running   0          17m
jovyan:~$
```

... and you're back to just your notebook server (you may also see those belonging to others in your Bryn team).

### Where did my output data go?

Once the workflow completes you'll see something like:

```console
-[nf-core/rnaseq] Pipeline completed successfully with skipped sampl(es)- -[nf-core/rnaseq] Please check MultiQC report: 1/5 samples failed strandedness check.-
Completed at: 16-Apr-2023 13:26:51
Duration : 5m 50s
CPU hours : 0.4
Succeeded : 196
```

We specified `nfout` as out outdir and you'll see the directory in the file browser on the left hand side of the JupyterLab interface. Take a look in `nfout/pipeline_info/` inside your file browser. Try double clicking on the various html, yaml and csv files here and you'll see that they open in new tabs for immediate reading.

One thing to note here, when opening `html` files such as `execution_report_[date].html`, javascript is disabled in the tab by default. Right click the file and select `Open in New Browser Tab` from the context window to see the full report.

### Where are nextflow assets and temporary/intermediate (workdir) outputs stored?

By default, the CLIMB nextflow config sets nextflow 'home' to `/shared/team/nxf_work/$JUPYTERHUB_USER`. You'll see a number of subdirectories exist at that location, including `assets`, which will now contain the rnaseq workflow we just used, and `work`: where the intermediate outputs are located.

```console
jovyan:~$ ls /shared/team/nxf_work/demouser.climb-big-data-d/
assets  capsule  framework  plugins  secrets  tmp  work
```

## CLIMB nextflow config defaults

We have tried to make it as easy as possible to use nextflow on CLIMB, and to make full use of available resources via our Kubernetes infrastructure. Out-of-the box, we set a number of configuration defaults.

- Nextflow home is set to `/shared/team/nxf_work/$JUPYTERHUB_USER`
- WorkDir is set to `/shared/team/nxf_work/$JUPYTERHUB_USER/work`
- Executor is set to `k8s` (plus some supporting config)
- `/shared/team` and `/shared/public` (read only) are mounted as PVCs to all nextflow pods
- A K8s ServiceUser is pre-mounted (no credentials setup required)
- S3 bucket path-style access is enabled, with `s3.climb.ac.uk` set as the endpoint
- S3 keys have also been injected from Bryn
