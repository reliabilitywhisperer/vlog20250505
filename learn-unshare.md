# Unshare Plaground wiki

## Overview
`unshare` is a Linux command-line utility that creates new namespaces and executes programs within them. It's a wrapper for the `unshare` system call and is essential for container technologies.

## Basic Usage
```bash
unshare [options] [program [arguments]]
```

## Namespace Types

### UTS Namespace
Creates a namespace for hostname and domain name:
```bash
unshare --uts bash
```

### PID Namespace
Creates a namespace for process IDs:
```bash
unshare --pid --mount-proc --fork bash
```
The `--mount-proc` option mounts a new `/proc` directory, and `--fork` is required for PID namespaces.

### Mount Namespace
Creates a namespace for filesystem mounts:
```bash
unshare --mount bash
```

## Creating a Minimal Container

Download base image to be used as new file system : `alpine-rootfs`


   ```
   mkdir ~/container
   cd ~/container
   wget https://dl-cdn.alpinelinux.org/alpine/v3.16/releases/x86_64/alpine-minirootfs-3.16.0-x86_64.tar.gz
   mkdir alpine-rootfs
   tar -xzf alpine-minirootfs-3.16.0-x86_64.tar.gz -C alpine-rootfs
   ```

### Creating a Root Filesystem
1. Bind mount the directory:
   ```bash
   cd alpine-rootfs
   mount --bind ./alpine-rootfs ./alpine-rootfs
   ```

2. Create namespaces and switch to the new root:
   ```bash
   unshare --pid --mount --fork bash
   pivot_root . ./old_root
   ```

   Now, if you do `ls` you will se alpine files mounted on `/`

### Working in the New Environment
- Without standard utilities, use shell builtins:
  - List files: `echo /*`
  - Read files: use `read` builtin
  - Execute commands with the minimal shell

## Common Options

| Option | Description |
|--------|-------------|
| `--mount`, `-m` | Create a new mount namespace |
| `--uts`, `-u` | Create a new UTS namespace |
| `--pid`, `-p` | Create a new PID namespace |
| `--net`, `-n` | Create a new network namespace |
| `--fork`, `-f` | Fork before executing the program |
| `--mount-proc` | Mount `/proc` filesystem (for PID namespaces) |

## See Also
- `nsenter` - Enter existing namespaces
- `lsns` - List namespaces
- `ip netns` - Manage network namespaces
