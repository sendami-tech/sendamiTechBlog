---
layout: post
---
You wonder why do you need to set up a development environment with Docker having your laptop to develop code. Sometimes you need to build in a different OS, for example, and you don't want to buy a new laptop per every different environment. In my case, I am starting to study AWS. For me, it was just easier to set up a Linux environment with Docker, to install all the tools without a possible break my current development environment. As well, it can happen that in your team some of your colleagues use Windows, others use Mac, then it's useful that everyone works under the same development environment to be able to build and deploy the app in the same way.

Let's go straight to the steps to follow, shall we?

# Requirements

It would help if you had Docker installed on your laptop. You can follow these instructions they are straightforward to follow: https://docs.docker.com/get-started/. And as well, you can learn what's Docker in there, in case you don't know.

**Note:** don't worry about Kubernetes or Swarn, I want to go straight to the point to get a simple Linux environment to be able to develop code, nothing else, I want to keep it simple.

# Steps

* Define a Dockerfile the environment you want to have. With that, I mean things like OS, tools like git or node or npm. Just think about what you can need to develop your app. In here, you can find a Dockerfile reference: https://docs.docker.com/engine/reference/builder/. Again this is not the purpose of this post; it's to have a simple development environment as quickly as possible.

```
FROM ubuntu:16.04

RUN apt-get update

# Install all the tools that you need like git, node, nvm, npm...
```
* Once you have your Dockerfile, you need to build the image that you are going to use to work. To do that you need to execute the following command:

```
docker image build -t <imageName>:<imageVersion> .
```

This command needs to be run once unless you get errors. Note: Don't forget the dot at the end of the command. If you don't add it, it returns a strange error log, and you won't know why it's failing.

* Now that you have your image built, we want to start working with that container we just created. To do that we execute the following command:

```
docker run -v /path:/containerPath ---name <containerName> -it <imageName>:<imageVersion> bash
```

What are we doing here? We are mapping a folder of our laptop "path" with a path inside the container "containerPath". So, for example, you are developing your app in your laptop. Still, you need to build it in an appropriate environment; you can map the folder where the app is stored and access to it from the container, making the app, deploying the app, whatever you need.

'bash' is used to start the container with the command line, being able to execute directly in there all the commands that I need.

You can find more info regarding docker run in here: https://docs.docker.com/engine/reference/commandline/run/


* From this point, you can start working, but you can think, what if the bash is closed? Or if you want to stop working in the app through Docker but keep on accomplishing what I was doing one week after. I found the following commands useful to do that:

```
# To start the container
docker start <conatinerName>

#To stop the container
docker stop <containerName>

#To run the container opening window with bash
docker exec -it <containerName> bash
```
**Note:** if you want to exit from the command line inside the container use Ctrl + D.