This post is the notes when I learn Docker.

# What is docker

Unlike virtual machines, Docker containers don’t use hardware virtualization. Programs running inside Docker containers interface directly with the host’s Linux kernel. Docker is not a virtualization technology. Docker doesn’t provide the container technology, but it specifically makes it simpler to use.

The containers that Docker builds are isolated with respect to eight aspects:
- PID namespace —Process identifiers and capabilities
- UTS namespace —Host and domain name
- MNT namespace —File system access and structure
- IPC namespace —Process communication over shared memory
- NET namespace —Network access and structure
- USR namespace —User names and identifiers
- `chroot()` —Controls the location of the file system root
- `cgroups` —Resource protection

# Basic commands

## get help
`docker help` - all help
`docker help` - help for a certain command

## create and start a container
`docker run --detach --name xxx <image_name>:<tag>
`docker run --tty --interactive --name xxx <image_name> /bin/bash`

- --detach or -d: daemon
- --tty --interactive: interactive with a virtual terminal and bind stdin

## list
`docker ps` - list all containers
`docker exec <container_id> ps` - processes within a specific container

## restart & stop
`docker restart <container_id or name>`
`docker stop <container_id or name>`

## view logs
`docker logs <container_id or name>` - Anything that the program writes to the stdout or stderr output streams will be recorded in this log

## just create a container not starting
`docker create <image_name>` - output the container id
`docker create --cidfile <cid_file_path> <image_name>` - save CID into a file

## how to get container id 
```
CID=$(docker ps --latest -quiet)
echo $CID
```

Retrieve the latest running container ID

## tell the dependency
`docker create --link <CID> <image_name>`

The link mechanism injects IP addresses into dependent containers, and containers that aren’t running don’t have IP addresses.

 
# Environment-agnostic
Docker has three specific features to help build environment-agnostic systems:

- Read-only file systems
- Environment variable injection
- Volumes
 

