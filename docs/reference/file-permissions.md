# File permissions in Linux
File permissions in Linux are a crucial aspect of the operating system's security model. They determine who can access files and directories and what actions they can perform on them. Understanding and managing file permissions are essential skills for any Linux user and system administrator.

## File Permissions Basics

In Linux, every file and directory has three sets of permissions, corresponding to three different categories of users:

1. **Owner**: The user who created the file or directory.
2. **Group**: A group to which the file or directory belongs. Each user can be a member of multiple groups, and one of these groups is the primary group for that user.
3. **Others**: Any user on the system who is not the owner and does not belong to the group associated with the file or directory.

For each of these categories, there are three basic types of permissions:

1. **Read (r)**: Allows the user to view the contents of a file or list the contents of a directory.
2. **Write (w)**: Allows the user to modify the contents of a file or create, delete, and rename files within a directory.
3. **Execute (x)**: For files, allows the user to execute the file as a program. For directories, it allows the user to access its contents (i.e., `cd` into it).

## Displaying File Permissions

You can use the `ls` command with the `-l` option to display file permissions in long format:

```
$ ls -l
-rw-r--r-- 1 user group  4096 Jul 20 10:00 myfile.txt
drwxr-xr-x 2 user group  4096 Jul 20 10:00 mydir
```

In the output, the first column represents the file permissions. The first character indicates the file type (`-` for regular files, `d` for directories). The next nine characters are divided into three sets of three characters, representing permissions for owner, group, and others, respectively.

## Changing File Permissions

You can modify file permissions using the `chmod` command. There are two ways to specify permissions: symbolic notation and octal notation.

### Symbolic Notation:

In symbolic notation, you use letters to add or remove permissions. The letters are `+` (add), `-` (remove), and `=` (set explicitly).

For example, to give the owner read and write permissions on a file:

```
$ chmod u+rw myfile.txt
```

To remove execute permission for others on a directory:

```
$ chmod o-x mydir
```

### Octal Notation:

Octal notation represents file permissions using a three-digit number. Each digit corresponds to one of the permission sets (owner, group, others). The digits are calculated by adding the values for read (4), write (2), and execute (1) permissions.

For example, to set read, write, and execute permissions for the owner and read-only permissions for the group and others:

```
$ chmod 755 myfile.txt
```

## Recursively Changing Permissions

The `chmod` command can be used with the `-R` option to recursively change permissions for directories and their contents.

```
$ chmod -R 755 mydir
```

## Special Permissions: Setuid, Setgid, and Sticky Bit

There are three special permissions that can be set on files and directories:

1. **Setuid (SUID)**: When set on an executable file, it allows the user who runs the program to temporarily inherit the owner's privileges.

2. **Setgid (SGID)**: When set on an executable file or directory, it allows the user who runs the program or creates files within the directory to inherit the group's privileges.

3. **Sticky Bit**: When set on a directory, it ensures that only the owner of a file within that directory can delete or rename it, even if others have write permissions on the directory.

Special permissions are represented by additional characters in the file permissions display.
