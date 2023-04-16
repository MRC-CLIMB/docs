# Understanding storage

## Home directory

Your home directory `/home/jovyan` (~) is actually a mount that maps to persistent storage for your CLIMB user. Anything written here will be persisted, even after the container is stopped and restarted. Home directories are intentionally small (20GB by default, depending on your tier) and are not intended for large conda environments or databases, for example. This will make more sense once you understand the other storage options available to you.

Your home directory is also the default/base location for the file browser pane on the left.

## Team share

You'll find a writable 'team share' mounted at `/shared/team`, and symlinked to your home directory by default as `shared-team` (hence it shows in the file browser).

This is a larger storage location (1TB+ depending on tier) that all team members share simultaneous read/write access to. It's also SSD-backed, so its extremely fast.

## S3 Buckets

If you've started using S3 buckets via Bryn, your access keys will have already been injected into the notebook server as environment variables. We've also pre-configured `aws cli` and `s3cmd` to use the CLIMB S3 endpoints by default. As a result, you should be able to access buckets with no additional setup.

## Read-only shares

In addition to the team share, you may also notice additional mounts under `/shared/`, including at least `/shared/public`. Here you will find read-only data and resources provided by CLIMB, that may be useful to microbial bioinformatics workflows. Initially we have populated these shares with a few key resources:

- [Ben Langmead's Kraken2/Bracken Refseq Databases](https://benlangmead.github.io/aws-indexes/k2) - in `/shared/public/db/kraken2`
- [The NCBI GenBank non-redundant protein BLAST database](https://ftp.ncbi.nlm.nih.gov/blast/db/) - in `/shared/public/db/blast`
