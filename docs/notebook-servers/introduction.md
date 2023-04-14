# Introduction to Jupyter Notebook Servers

## What is a notebook server?

CLIMB hosts a bespoke installation of JupyterHub, which allows users to interact with our powerful compute infrastructure through a web interface. Rather than creating Virtual Machines and accessing them via SSH, users in a team can each spin up their own 'notebook server', and access it directly via a URL.

But what actually _is_ a notebook server? In the CLIMB environment, notebook servers are container instances that run inside a pod on a Kubernetes cluster. Don't worry too much about that though. You can think of them as your own server. Just like a VM, you'll have terminal access and a filesystem. Your home directory is persisted, even when your server is terminated, and you'll have out-of-the-box access to things like a shared team volume, s3 buckets and more.

## Advantages of containers and notebook servers

So, why would you want to use these over Virtual Machines?

- **Ease of setup**: rather than being faced with a blank base OS with nothing installed, you'll find that the base CLIMB-BIG-DATA container image comes pre-configured and ready to use. Conda, nextflow, and a variety of command line tools are pre-installed and configured to work with your other team resources.
- **Flexible resource usage**: depending on your tier, you'll have access to a minimum and maximum amount of resource for your notebook servers (vCPUs and memory) for each team member. In addition, you can access huge additional cluster resource via. nextflow running against our Kubernetes (K8s) execution environment. If you prefer larger notebook servers to run things locally, you can upgrade your tier and gain access immediately: no need to start again and re-install everything!
- **Ease of access**: No more losing your keys and getting locked out of your VM. All you need is Bryn access, and you can always access your notebook servers via our simple web interface. You can even create time-limited sharing links that don't require a password login!
- **Affordable GPU access**: Under the VM model, GPUs were an expensive resource because they were 'locked up' in individual VMs until terminated. Using containers, we can share GPU access more equitably, making them more affordable. Not only that, but the CLIMB-BIG-DATA base image is pre-configured with the necessary tools and drivers needed to use our powerful A100s out of the box.
- **Easier collaboration and sharing**: The virtual machine usage model tended to produce siloed data on volumes that could only be attached to one VM at a time. The collection of software installed on a VM was also siloed, in the sense that it was not easily reproduced from scratch. Under the new usage model of container notebooks, you'll find a a pre-mounted share at `/shared/team` that every team member has read/write access to, and backed by our high speed SSDs. Your S3 bucket keys are also pre-injected as environment variables, making bucket access a breeze. Need to share something with an external collaborator? Just make a public bucket and upload your files.
- **The ideal teaching environment**: Running workshops becomes a breeze. Simply create a team on Bryn, invite attendees and prepare your materials on the shared team drive or S3 buckets. No more losing half a day getting everyone logged in via SSH.

## Is this just for begginners?

Absolutely not. Whilst the new service is certainly far easier to get started with, it is also far more powerful for advanced users. We've ensured that each team gets a pre-mounted kubernetes service user with permissions scoped to your team's namespace. This allows users to run nextflow workflows on the external K8s execution environment, but also to run other containers in pods within their namespace via kubectl.

## How do I get started?

Head over to our getting started guide, or check out some of the tutorials.
