# Jump into the terminal

Select `File -> New -> Terminal`, or click the Terminal icon on the launcher pane. You'll get a new terminal tab in the activity bar, and find yourself in a bash shell.

<!-- prettier-ignore -->
!!! info "Who is jovyan?"
    Looking at your bash prompt, you'll notice that your username is `jovyan` (there's a backstory, but it means 'related to Jupyter'). Why is everyone's username the same? Your notebook server is running as a [container](https://cloud.google.com/learn/what-are-containers). The container instance is private and linked to your Bryn user's storage, but the image it runs is the same for everyone. As a result, it is not necessary or desirable to have unique system users.

    TLDR: don't worry about it. Inside your notebook server, your username is `jovyan`

![By jove comic](../img/by-jove.jpg)

## Where am I? Who am I?

By default, you're in a bash shell running against the base operating system of the `climb-jupyterhub` container image (which is based on Ubuntu). You'll see in your bash prompt that you're in your home directory (represented by the tilde character `~`).

```console
jovyan:~$ pwd
/home/jovyan
```

<!-- prettier-ignore -->
!!! info "What about sudo?"
    `jovyan` doesn't have sudo privileges. This may seem restrictive, but we've pre-configured the `climb-jupyter` base image with everything you'd likely need sudo for pre-installed. Everything else should be installable via package managers, such as Conda. You'll also be able to run Nextflow against out K8s execution environment 'out-of-the-box'.

## How do I install software?

In the first instance, check out [installing software with Conda](installing-software-with-conda.md).

## What next?

You many want to read the reference material on using the Linux command line

* [Linux Commands](../reference/linux-commands.md)
* [Linux Pipelines](../reference/linux-pipelines.md)
* [File Permissions in Linux](../reference/file-permissions.md)
* [Copying files with `scp`](../reference/scp.md)
* [Copying files with `rsync`](../reference/rsync.md)
