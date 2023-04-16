# Installing software with Conda

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

## Default .condarc

When you first launch a notebook server, we generate a default `.condarc` in your home directory. This sets the path for your new environments to `/shared/team/conda/$JUPYTER_USERNAME`. Why? As mentioned above, your home directory is relatively small vs your team share, so it makes sense to use the larger mount. In additon, it becomes easy to share conda environments with other team members.

```console
jovyan:~$ cat ~/.condarc
envs_dirs:
  - /shared/team/conda/andysmith.andy-bryn-dev-t
[...]
```

## Creating new conda environments

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
