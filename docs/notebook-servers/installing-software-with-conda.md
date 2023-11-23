# Installing software with Conda

It's critical to understand how Conda works with notebook servers, and the caveats compared to using it locally or on a VM.

Firstly, understand that **you cannot install new software to the base Conda environment**. Let's have a look at why.

```console
$ conda info --envs
base                     /opt/conda
```


The base Conda environment is installed at `/opt/conda`. Since we are running inside a container, any changes made to this part of the filesystem will not be retained once the container is stopped and restarted (unlike your home dir and shares, which are persisted).

We've made the base environment read only to prevent any confusion.

<!-- prettier-ignore -->
!!! tip
    You *must* create a new Conda environment before installing software!

## Default .condarc

When you first launch a notebook server, we generate a default `.condarc` in your home directory. This sets the path for your new environments to `/shared/team/conda/$JUPYTER_USERNAME`. Why? As mentioned above, your home directory is relatively small compared with your team share, so it makes sense to use the larger mount. In addition, it becomes easy to share Conda environments with other team members.

```console
jovyan:~$ cat ~/.condarc
envs_dirs:
  - /shared/team/conda/demouser.andy-bryn-dev-t
[...]
```

## Creating new Conda environments

Understanding the above, you can create new Conda environments in the usual way. The only caveat is that if you wish to use this environment with Jupyter notebooks, you must install `ipykernel`.

<!-- prettier-ignore -->
!!! warning
    If you don't install `ipykernel` in a new Conda environment, it won't show up on the launcher or be available to select within the python notebooks interface. However, you can use the environment just fine within a terminal.

Let's go ahead and install [bactopia](https://bactopia.github.io/v2.2.0/quick-start/) as an example.

If you try listing channels, you'll see you already have `conda-forge` and `bioconda` set:

```console
jovyan:~$ conda config --show channels
channels:
  - conda-forge
  - bioconda
  - defaults
```

Now, lets install `bactopia`:

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

Done. Listing your environments again to confirm the location:

```console
jovyan:~$ conda info --envs
# conda environments:
#
base                     /opt/conda
bactopia                 /shared/team/conda/demouser.andy-bryn-dev-t/bactopia
```

And finally let's activate the environment:

```console
jovyan:~$ conda activate bactopia
(bactopia) jovyan:~$ bactopia --version
bactopia 2.2.0
```

## Using Conda environments in Jupyter notebooks

If you installed `ipykernel` when you created a Conda environment,
as in the example above, then it should be available to use in your Jupyter notebooks.
Near the top-right of any Jupyter notebook will be a text box with something like
"Python [conda env:root]" or "Python 3 (ipykernel)", depending on which launcher
icon you clicked on to start the notebook.  This specifies which Python kernel is
being used.  To switch to you Conda environment, click on this text field and
select the kernel associated with your Conda environment from the options under
**Start Preferred Kernel**.  E.g., "Python [conda env:bactopia]" in the example above.

After changing the kernel, it's worth restarting the kernel and
running your code cells again.

If the environment doesn't appear in the list of kernels, you may have not installed
`ipykernel` when you created it, in which case you can install it later.  To do so,
open a new terminal, activate the environment and install `ipykernel` with e.g.
```console
jovyan:~$ conda activate bactopia
(bactopia) jovyan:~$ conda install -y ipykernel
Collecting package metadata (current_repodata.json): done
Solving environment: done
...
Downloading and Extracting Packages

Preparing transaction: done
Verifying transaction: done
Executing transaction: done
(bactopia) jovyan:~$
```
The kernel `Python [conda env:bactopia]` should now appear in the
dropdown list, including in Jupyter notebooks that are already
running.
