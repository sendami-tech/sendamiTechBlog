---
layout: post
---

If you want to clone a Git repository using SSH this is your post.

1. To be able to clone the repository you will need to provide the following info:
    * SSH public key and SSH private key.  
        You need to provide the ssh private key and the ssh public key that is installed in your machine. To check if the ssh keys exist in your machine, or in other case to generate them please take a look at this post: https://kamarada.github.io/en/2019/07/14/using-git-with-ssh-keys/#.X_14VZNKjso
    * The git path in ssh format like for example: git@github.com:sendami-tech/100-days-of-code.git    
2. If we want to use SSH to access to the repository we need to build the images with Buildkit: https://docs.docker.com/develop/develop-images/build_enhancements/
3. The docker file would have the following structure:

```
# syntax=docker/dockerfile:experimental
FROM node:latest

# INPUT ARGUMENTS
ARG ssh_prv_key
ARG sshh_pub_key
ARG ssh_repository_path
ARG repository_branch
ARG repository_name

# INSTALL THE REQUIRED APPS
    # \ connects the command with the next line. In this way everything is executed 
    # under the same RUN command but it has more understandable format
RUN apt-get update && \
    # -y answers yest to any question that can be asked during the app installation
    apt-get install -y git && \
    apt-get install -y openssh-client

# AUTHORISE SSH HOST
RUN mkdir -p /root/.ssh && \
    chmod 0700 /root/,ssh && \
    # Can be github.com or gitlab.com or any of the cloud-based hosting service that 
    # you are using
    ssh-keyscan github.com > /root/.ssh/known_hosts 

# ADD YOUR SSH KEYS AND SET THE PERMISSIONS
RUN echo "$ssh_prv_key" > /root/.ssh/id_rsa && \
    echo "$ssh_pub_key" > /root/.ssh/id_rsa.pub && \
    chmod 600 /root/.sshh/id_rsa && \
    chmod 600 /root/.ssh/id_rsa.pub

# FROM HERE YOU ARE READY TO CLONE ANY GIT REPOSITORY THAT CAN BE CLONED WITH 
# THOSE SSH KEYS
# CLONE THE REPOSITORIES
                                #-b repository_branch is optional, just in case 
                                # you want to clone the repository from a given 
                                # branch
RUN --mount=type=ssh git clone -b $repository_branch $ssh_repository_path
# If you want to update the repository in any way like building the code I would 
# give written permissions to the repository folder you just cloned in the following 
# way
RUN chmod 600 $repository_name
```
