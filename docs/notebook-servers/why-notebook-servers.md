# Why Notebook Servers?

A Notebook Server is a lightweight virtualised installation of Linux
that users interact with through [JupyterLab](https://jupyterlab.readthedocs.io/),
which grants access to various applications (e.g. 
[terminals](using-the-terminal.md),
[Jupyter notebooks](using-jupyter.md),
[RStudio](using-jupyter.md),
[Nextflow](using-nextflow.md))
and enables users to run complex data science and bioinformatics tasks.
The possibilities are demonstrated in the walkthroughs in the sidebar.

## Advantages of the Notebook Server

Just as if you have your own physical machine, you'll have terminal access and a filesystem. Your home directory is persistent, even when your server is terminated, and you'll have out-of-the-box access to fantastic features like a shared team volume, S3 buckets and more.

|   | Benefits                                             | Details                                                                                                                                                                       |
|---|------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|  ![Checked yes](../img/checked.png) | Accessible anywhere in the world  | Eliminates key loss and VM lockouts. Access notebook servers through a user-friendly web interface, with time-limited sharing links for convenience.                                           |
| ![Checked yes](../img/checked.png)  | Flexible resource usage                              | Tier-based access to minimum and maximum vCPUs and memory for notebook servers. Upgrade for larger servers or use K8s for additional cluster resources. No need to reinstall. |
|  ![Checked yes](../img/checked.png) | GPU access                                           | Containers enable equitable GPU sharing, making them affordable compared to the VM model. CLIMB-BIG-DATA base image is pre-configured for easy A100 utilization.              |
| ![Checked yes](../img/checked.png)  | Sandboxed environment for teaching and training      | Simplifies workshops: create a team, invite attendees, and share materials on team drive or S3. No SSH login hassles.                                                         |
| ![Checked yes](../img/checked.png)  | Pre-installed software and tools                     | CLIMB-BIG-DATA container has pre-installed Conda, Nextflow, and CLI tools, ready to use, simplifying OS setup and team integration.                                           |

## Key differences to standard Linux installation

The notebook server is a virtualised installation of Linux, and as such there are some differences to a standard Linux installation.

| Restriction                     | Solution                                                                                                                 |
|---------------------------------|--------------------------------------------------------------------------------------------------------------------------|
| No system wide superuser (sudo) | Install software within home directory with package managers (e.g. conda) rather than apt/yum                             |
| No running of web services      | Static results (including HTML) can be hosted via S3.                                                                    |
| No support for opening ports    | This is out of scope for notebooks, but CLIMB-BIG-DATA does have short term leases for more traditional virtual machines |

## Is this just for beginners?

Absolutely not. Whilst the new service is certainly far easier to get started with, it is also far more powerful for advanced users. We've ensured that each team gets a pre-mounted Kubernetes service user with permissions scoped to your team's namespace. This allows users to run Nextflow workflows on the external K8s execution environment, but also to run other containers in pods within their namespace via `kubectl`.
