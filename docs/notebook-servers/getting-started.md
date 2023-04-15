# Getting started with Jupyter Notebook Servers

## _A guided tour_

## How to launch and access a notebook server

1. First, [log in to Bryn](/getting-started/authentication)
2. Using the navigation menu on the left hand side, select 'Notebook servers' under the 'Compute' subheading
3. Click the 'Launch notebook server' green action button on the right hand side
4. Select a profile, for example 'Standard server' or 'GPU server' (tier dependent)
5. Click 'Launch Server' and monitor the progress bar
6. Once ready, click the url beneath the 'User notebook server'
7. On first login, you may be asked to authorize access to your Bryn account. Click 'Authorize'
8. The JupyterLab interactive computing interface should open in a new tab.

## Finding your way around the JupyterLab interface

We highly recommend spending a few moments familiarising yourself with the basic JupyterLab interface. Head on over to the [JupyterLab interface docs](https://jupyterlab.readthedocs.io/en/stable/user/interface.html) to get started.

Fundamentally, your screen is divided into a few areas. You'll see context menus at the top (File, Edit, View, Run etc.), a file browser pane on the left, and an activity area that initially displays a launcher interface with tiles. Clicking on one of these tiles will open a new tab in the activity area. Since most users are familiar with using a terminal to access a system shell (just like on a VM!), lets start there.

## Jump into the terminal

Select `File -> New -> Terminal`, or click the Terminal icon on the launcher pane. You'll get a new terminal tab in the activity bar, and find yourself in a bash shell.

<!-- prettier-ignore -->
!!! info "Who is jovyan?"
    Looking at your bash prompot, you'll notice that your username is `jovyan` (there's a backstory, but it means 'related to Jupyter'). Why is everyone's username the same? Your notebook server is running as a [container](https://cloud.google.com/learn/what-are-containers). The container instance is private and linked to your Bryn user's storage, but the image it runs is the same for everyone. As a result, it is not necessary or desirable to have unique system users.

    TLDR: don't worry about it. Inside your notebook server, your username is `jovyan`

## Where am I? Who am I?

By default, you're in a bash shell running against the base operating system of the climb-jupyterhub container image (which is based on Ubuntu). You'll see in your bash prompt that you're in your home directory (represented by the tilde character `~`).

```console
jovyan:~$ pwd
/home/jovyan
```

<!-- prettier-ignore -->
!!! info "What about sudo?"
    jovyan doesn't have sudo privileges. This may seem restrictive, but we've pre-configured the climb-jupyter base image with everything you'd likely need sudo for pre-installed. Everything else should be installable via package managers, such as conda. You'll also be able to run Nextflow against out K8s execution environment 'out-of-the-box'.

## Understanding storage

### Home directory

Your home directory `/home/jovyan` (~) is actually a mount that maps to persistant storage for your CLIMB user. Anything written here will be persisted, even after the container is stopped and restarted. Home directories are intentionally small (20GB by default, depending on your tier) and are not intended for large conda environments or databases, for example. This will make more sense once you understand the other storage options available to you.

Your home directory is also the default/base location for the file browser pane on the left.

### Team share

You'll find a writable 'team share' mounted at `/shared/team`, and symlinked to your home directory by default as `shared-team` (hence it shows in the file browser).

This is a larger storage location (1GB+ depending on teir) that all team members share simultaneus read/write access to. It's also SSD-backed, so its extremely fast.

### S3 Buckets

If you've started using S3 buckets via Bryn, your access keys will have already been injected into the notebook server as environment variables. We've also pre-configured `aws cli` and `s3cmd` to use the CLIMB S3 endpoints by default. As a result, you should be able to access buckets with no additional setup.

### Read-only shares

In addition to the team share, you may also notice additional mounts under `/shared/`, including at least `/shared/public`. Here you will find read-only data and resources provided by CLIMB, that may be useful to microbial bioinformatics workflows. Initially we have populated these shares with a few key resources:

  * [Ben Langmead's Kraken2/Bracken Refseq Databases](https://benlangmead.github.io/aws-indexes/k2) - in `/shared/public/db/kraken2`
  * [The NCBI GenBank non-redundant protein BLAST database](https://ftp.ncbi.nlm.nih.gov/blast/db/) - in `/shared/public/db/blast`

## Installing software with Conda

Its critical to understand how conda works with notebook servers, and the caveats vs using it locally or on a VM.

Firstly, understand that **you cannot install new software to the base conda environment**. Lets have a look at why.

```console
$ conda info --envs
base                     /opt/conda
```

The base conda env is installed at `/opt/conda`. Since we are running instide a container, any changes made to this part of the filesystem will not be retained once the container is stopped and restarted (unlike your home dir and shares, which are persisted).

We've made the base environment read only to prevent any confustion.

<!-- prettier-ignore -->
!!! tip
    You *must* create a new conda environment before installing software!

### Default .condarc

When you first launch a notebook server, we generate a default `.condarc` in your home directory. This sets the path for your new environments to `/shared/team/conda/$JUPYTER_USERNAME`. Why? As mentioned above, your home directory is relatively small vs your team share, so it makes sense to use the larger mount. In additon, it becomes easy to share conda environments with other team members.

```console
jovyan:~$ cat ~/.condarc
envs_dirs:
  - /shared/team/conda/andysmith.andy-bryn-dev-t
[...]
```

### Creating new conda environments

Understanding the above, you can create new conda environments in the usual way. The only caveat is that if you wish to use this environment with ipython notebooks, you must install `ipykernel`.

<!-- prettier-ignore -->
!!! warning
    If you don't install `ipykernel` in a new conda environment, it won't show up on the launcher or be available to select within the python notebooks interface. However, you can use the environment just fine within a terminal.

Lets go ahead and install [bactopia](https://bactopia.github.io/v2.2.0/quick-start/) as an example.

If you try listing channels, you'll see you already have conda-forge and bioconda set.

```console
jovyan:~$ conda create -y -n bactopia bactopia ipykernel
...grab a coffee...
Downloading and Extracting Packages

Preparing transaction: done
Verifying transaction: done
Executing transaction: done
#
# To activate this environment, use
#
#     $ conda activate bactopia
#
# To deactivate an active environment, use
#
#     $ conda deactivate
```

Done. Listing your envs again to confirm the location:

```console
jovyan:~$ conda info --envs
# conda environments:
#
base                     /opt/conda
bactopia                 /shared/team/conda/andysmith.andy-bryn-dev-t/bactopia
```

And finally lets activate the environment

```console
jovyan:~$ conda activate bactopia
(bactopia) jovyan:~$ bactopia --version
bactopia 2.2.0
```
