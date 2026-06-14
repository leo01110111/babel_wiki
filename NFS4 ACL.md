# NFS4 ACL

This guide explains how to use NFSv4 Access Control Lists (ACLs) to manage permissions on an NFSv4-mounted filesystem. It focuses on using the **nfs4-acl-tools** package, which provides utilities like `nfs4_getfacl` and `nfs4_setfacl`.

## Overview of NFSv4 ACLs
NFSv4 ACLs extend traditional UNIX permissions by allowing specific access rules for individual users or groups. Each ACL entry specifies a principal (user or group), permissions (e.g., read, write, execute), and inheritance flags.

Key tools:
- `nfs4_getfacl` - Displays the ACLs of a file or directory.
- `nfs4_setfacl` - Modifies or sets ACLs.

## ACL Entry Structure
An NFSv4 ACL entry consists of a type, flags, principal, and permissions. Below are the key components:

### TYPE
- **A** - Allow principal to perform actions requiring permissions.
- **D** - Deny principal from performing actions.

### FLAGS
- **d** - Newly-created subdirectories will inherit the ACE.
- **f** - Newly-created files will inherit the ACE.
- **g** - Indicates that the principal represents a group instead of a user.
- Additional flags (e.g., `i` for inherit only, `n` for no propagate inherit) are covered in [#Inheritance](#inheritance).

### PRINCIPALS
- **OWNER@** - The file or directory owner (user).
- **GROUP@** - The file’s group.
- **EVERYONE@** - All users (world).
- Custom principals can be specific user IDs (e.g., `2688803`) or group IDs (e.g., `100` with the `g` flag).

### PERMISSIONS
Here are common NFSv4 ACL permission flags:
- **r** - Read data / list directory
- **w** - Write data / create file
- **x** - Execute / traverse directory
- **t** - Read attributes
- **T** - Write attributes
- **n** - Read named attributes
- **N** - Write named attributes
- **c** - Read ACL
- **C** - Write ACL
- **y** - Synchronize

### Permission Aliases
Shorthand aliases for common permission sets:
- **R** - Generic read (expands to `rntcy`)
- **W** - Generic write (expands to `watTNcCy`)
- **X** - Generic execute (expands to `xtcy`)

## Viewing ACLs
To check the current ACLs on a file or directory, use `nfs4_getfacl`.

### Example

```
nfs4_getfacl /data/user_data/bob/example.json
```

Output might look like:

```
# file: /data/user_data/bob/example.json
A::OWNER@:rwatTnNcCy
A::GROUP@:rxtncy
A::EVERYONE@:rxtncy
A:g:100:rwxatTnNcCy
```

Explanation:
- `A` = Allow (contrast with `D` for Deny).
- `OWNER@`, `GROUP@`, `EVERYONE@` are special identifiers.
- `100` is an example groupID (`g` flag) with specific permissions.

## Setting ACLs
Use `nfs4_setfacl` to modify ACLs. You can add, remove, or edit entries.

### Adding an ACL Entry
Grant the user with uid 1001 read and execute permissions:

```
nfs4_setfacl -a A::1001:rxtncy /data/user_data/bob/example.json
```

### Modifying an Existing Entry
Edit an existing entry for uid 1001 to add write permissions:

```
nfs4_setfacl -m A::1001:rxtncy A::1001:rwxtncy /data/user_data/bob/example.json
```

### Removing an Entry
Remove uid "1001"’s ACL entry:

```
nfs4_setfacl -x A::1001:rwxtncy /data/user_data/bob/example.json
```

### Setting ACLs from a File
Create a file (e.g., `myacl.txt`) with ACL entries:

```
A::OWNER@:rwatTnNcCy
A::GROUP@:rxtncy
A::EVERYONE@:rxtncy
A:g:100:rwxtncy
```

Apply it:

```
nfs4_setfacl -S myacl.txt /data/user_data/bob/example.json
```

## Inheritance
NFSv4 ACLs support inheritance for directories. Use flags like:
- `f` - File inherit
- `d` - Directory inherit
- `i` - Inherit only (applies to children, not the directory itself)
- `n` - No propagate inherit (limits inheritance depth)

Example: Allow groupID "100" to inherit permissions on new files and subdirectories:

```
nfs4_setfacl -a A:fd:g:100:rwxtncy /data/user_data/bob/project_dir
```

## Example: Setting Up a Group Share Directory
Here’s how to create a shared directory with NFSv4 ACLs for a group:

```
[dgriffi2@babel-5-27 dgriffi2]$ mkdir -m 0770 pub
[dgriffi2@babel-5-27 dgriffi2]$ chown :hpcuser /data/user_data/dgriffi2 /data/user_data/dgriffi2/pub
[dgriffi2@babel-5-27 dgriffi2]$ chmod g+x /data/user_data/dgriffi2
[dgriffi2@babel-5-27 dgriffi2]$ chmod g+s pub
[dgriffi2@babel-5-27 dgriffi2]$ nfs4_setfacl -a A:dfg:500:RWX pub/
[dgriffi2@babel-5-27 dgriffi2]$ touch pub/pub-file.txt
[dgriffi2@babel-5-27 dgriffi2]$ ls -la pub/
total 3
drwxrws---+ 2 dgriffi2 hpcuser 3 Mar  7 07:41 .
drwx--x---  3 dgriffi2 hpcuser 3 Mar  7 07:40 ..
-rw-rw----  1 dgriffi2 hpcuser 0 Mar  7 07:41 pub-file.txt
```

Explanation:
- `mkdir -m 0770 pub` creates a directory with group read/write permissions.
- `chown :hpcuser` sets the group ownership to "hpcuser".
- `chmod g+s` ensures new files inherit the group.
- `nfs4_setfacl -a A:dfg:500:RWX` grants the group, hpcuser generic read/write/execute with inheritance for files (`f`) and directories (`d`).

## See Also
- [Filesystem](Filesystem.md#filesystem-layout) - General NFS documentation
- [NFSv4 ACL Tools Documentation](https://linux.die.net/man/1/nfs4_setfacl)
