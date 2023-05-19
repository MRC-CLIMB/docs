# Understanding storage

## Home directory

Your home directory `/home/jovyan` (~) is actually a mount that maps to persistent storage for your CLIMB user. Anything written here will be persisted, even after the container is stopped and restarted. Home directories are intentionally small (20GB by default, depending on your tier) and are not intended for large conda environments or databases, for example. This will make more sense once you understand the other storage options available to you.

Your home directory is also the default/base location for the file browser pane on the left.

## Team share

You'll find a writable 'team share' mounted at `/shared/team`, and symlinked to your home directory by default as `shared-team` (hence it shows in the file browser).

This is a larger storage location (1TB+ depending on tier) that all team members share simultaneous read/write access to. It's also SSD-backed, so its extremely fast.

## S3 Buckets

If you've started using S3 buckets via Bryn, your access keys will have already been injected into the notebook server as environment variables. We've also pre-configured `aws cli` and `s3cmd` to use the CLIMB S3 endpoints by default. As a result, you should be able to access buckets with no additional setup.

Your S3 bucket storage is pre-configured for you through the $AWS_SECRET_ACCESS_KEY and $AWS_ACCESS_KEY_ID variables and the configuration file ~/.s3cfg. Do not change any of these unless you are very confident with s3 and can use it without support. The commandline tool [s3cmd](https://s3tools.org/s3cmd-howto) provides convenient access for moving files around. Here are some commands to get you started:

List the buckets that you have access to:

`s3cmd ls`

Retrieve a file from a bucket:

`s3cmd get s3://teamname-bucketname1/path_to_file/filename`

Copy a file to a bucket:

`s3cmd put local_file_name s3://teamname-bucketname1/path_to_copy_to/`

Users may wish to create a local folder to synchronise with an S3 bucket. Here we create a test file and use sync to copy it to S3. This method has the advantage of showing the S3 sync folder in the notebook file browser:

`mkdir ~/mys3bucket`

`echo "this is local" > ~/mys3bucket/testLocal.txt`

`s3cmd sync ~/mys3bucket/ s3://teamname-bucketname1/path_to_sync_with/`

## Read-only shares

In addition to the team share, you may also notice additional mounts under `/shared/`, including at least `/shared/public`. Here you will find read-only data and resources provided by CLIMB, that may be useful to microbial bioinformatics workflows. Initially we have populated these shares with a few key resources:

- [Ben Langmead's Kraken2/Bracken Refseq Databases](https://benlangmead.github.io/aws-indexes/k2) - in `/shared/public/db/kraken2`
- [The NCBI GenBank non-redundant protein BLAST database](https://ftp.ncbi.nlm.nih.gov/blast/db/) - in `/shared/public/db/blast`
