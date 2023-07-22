# Tutorial: Secure Copy (SCP) - Transfer Files Securely Over SSH

**SCP** (Secure Copy) is a command-line utility that allows you to securely transfer files and directories between a local host and a remote host or between two remote hosts over a **Secure Shell (SSH)** connection. It provides a secure and encrypted way to transfer data, making it a valuable tool for remote file transfers and backups.

In this tutorial, you'll learn how to use SCP to transfer files between various scenarios:

1. Copy files from your local machine to a remote server.
2. Copy files from a remote server to your local machine.
3. Copy files between two remote servers.

## Prerequisites

Before proceeding with the tutorial, make sure you have the following:

1. Access to a local machine with SCP installed (SCP comes pre-installed on most Unix-based systems).
2. Access to a remote server with SSH enabled.

## Syntax

The basic syntax of SCP is as follows:

```bash
scp [options] source destination
```

- `source`: The file or directory you want to copy. It can be a local file/directory or a remote file/directory in the format `username@remote_host:file_or_directory`.
- `destination`: The target location where the file/directory should be copied. It can be a local path or a remote path.

## Examples

### 1. Copy local file to a remote server:

```bash
scp /path/to/local/file.txt username@remote_host:/path/on/remote/server/
```

Replace `/path/to/local/file.txt` with the local file you want to transfer and `username@remote_host` with your remote server's SSH credentials. Also, specify the destination directory on the remote server where you want to copy the file.

### 2. Copy a remote file to your local machine:

```bash
scp username@remote_host:/path/on/remote/server/file.txt /path/to/local/
```

Replace `username@remote_host` with your remote server's SSH credentials, and specify the path of the remote file you want to copy. Also, specify the destination directory on your local machine where you want to save the file.

### 3. Copy a directory from local to remote server:

```bash
scp -r /path/to/local/directory username@remote_host:/path/on/remote/server/
```

Use the `-r` option to recursively copy a directory and its contents.

### 4. Copy a directory from remote to local machine:

```bash
scp -r username@remote_host:/path/on/remote/server/directory /path/to/local/
```

Use the `-r` option for recursive copying when transferring directories.

### 5. Copy files between two remote servers:

```bash
scp username@remote_host1:/path/to/source/file.txt username@remote_host2:/path/to/destination/
```

Replace `username@remote_host1` and `username@remote_host2` with the respective remote servers' SSH credentials. Specify the source file on the first remote server and the destination directory on the second remote server.

## Additional Options

SCP provides some useful options for different scenarios. Here are a few commonly used ones:

- `-P`: Specify a custom SSH port.
- `-i`: Use a specific identity file (private key) for SSH authentication.
- `-C`: Compresses data during the transfer, reducing the transfer time.
- `-q`: Suppress SCP's progress meter and non-error messages.
- `-p`: Preserve file attributes, such as timestamps and permissions.
