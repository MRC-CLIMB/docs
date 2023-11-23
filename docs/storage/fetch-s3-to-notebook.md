# Transferring within your CLIMB-BIG-DATA notebook server (S3 to notebook)

The easiest and ready way to transfer to/from your notebook to the S3 buckets is to use `s3cmd` which is already installed on your notebook server.
`s3cmd` is a command-line tool used to interact with Amazon Simple Storage Service (S3). It allows you to manage your S3 buckets and objects, including uploading, downloading, and deleting files. 

If you've started using S3 buckets via Bryn, your access keys will have already been injected into the notebook server as environment variables. We've also pre-configured `aws cli` and `s3cmd` to use the CLIMB S3 endpoints by default. As a result, you should be able to access buckets with no additional setup.

Your S3 bucket storage is pre-configured for you through the `$AWS_SECRET_ACCESS_KEY` and `$AWS_ACCESS_KEY_ID` variables and the configuration file `~/.s3cfg`. Do not change any of these unless you are very confident with S3 and can use it without support. The command line tool [`s3cmd`](https://s3tools.org/s3cmd-howto) provides convenient access for moving files around. Here are some commands to get you started.

If you are moving from an older CLIMB VM to the new notebook model, [you can read a dedicated guide here](transfer-from-vm-to-s3.md).

#### List S3 buckets
To list all your S3 buckets, use the following command:

```
s3cmd ls
```

#### Upload a file
To upload a file to an S3 bucket, use the following command:

```
s3cmd put /path/to/local/file s3://bucket-name/destination/path/
```

#### Download a file
To download a file from an S3 bucket, use the following command:

```
s3cmd get s3://bucket-name/path/to/s3/file /path/to/local/destination/
```

#### Copy a file within S3
To copy a file from one location to another within the same S3 bucket, use the following command:

```
s3cmd cp s3://bucket-name/source/path/to/s3/file s3://bucket-name/destination/path/
```

#### Delete a file
To delete a file from an S3 bucket, use the following command:

```
s3cmd del s3://bucket-name/path/to/s3/file
```

#### Synchronize local directory with S3 bucket
To synchronize a local directory with an S3 bucket (upload only the changed files), use the following command:

```
mkdir ~/mys3bucket
echo "this is local" > ~/mys3bucket/testLocal.txt
s3cmd sync ~/mys3bucket/ s3://teamname-bucketname1/path_to_sync_with/
```

#### Additional Options

`s3cmd` provides many more options and features. You can explore them by running:

```
s3cmd --help
```

