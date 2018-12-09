---
layout: post
title: Make Side Projects Easier To Work On With Docker  
category: Docker
tags: [docker, side-projects]
---

Recently, I restarted work on an old side project I had put on hold. In the intervening time, my machine was updated and I subsequently deleted the virtual machine image I was developing on. With the dread of having to create and set up a new virtual machine and the knowledge of containerization somewhat maturing, I decided to give Docker a try.

## Initial Setup

My previous setup had many problems. I was wasting a lot of hard drive space on a virtual machine that was only used for this side project. I had to deal with installing the language tools, database permissions, etc on the machine. Furthermore, I had to install all of my development tools to do the actual development. In short, I had to deal with all of the general headaches of managing an entire machine. The reason for not developing the application on the host machine was to isolate all of these dependencies and tools from the host machine. But it slowly became obvious that this advantage was outweighed by the problems. Comparing the above list with Docker's features/benefits, it appears this would be an ideal solution.

Following the many Docker guides on [Docker Hub](https://hub.docker.com), it was easy to find an existing container for the app's (in this case Ruby on Rails) environment. Furthermore, each container provides a README describing their available settings and configurations to make tweaking them a snap. Within a short time, a container can be created, and a running version of the app can be reached from the host machine. Aside from waiting for the container to initially download, there is no other ceremony needed with installers, permission prompts, etc.

## General Benefits

The hard drive space used by a general purpose virtual machine with enough space to grow with updates, etc can easily be in the tens of gigabytes. But with single-purpose Docker containers, the environment can be trimmed down to a fraction of that by having most of the unnecessary bits removed. If space is still a concern, some images even be built off of the [Alpine Linux](https://hub.docker.com/_/alpine/) image and can be less than 10 **megabytes**!

One project-specific headache that Docker also solved was handling different versions of Ruby. When installing Ruby, it installs globally on the machine. The default install makes it difficult to use multiple versions of Ruby or deal with version mismatches. This is solved in a non-containerized environment using [RVM](https://rvm.io), but it is yet another tool that needs to be set up on the machine. With Docker, the container only has the single version of Ruby since no other applications are running on the machine. So this problem becomes a non-issue.

A more general note to the last benefit, the isolation of all dependencies inside an app-specific container ensures that anything can be done on the host machine without worrying about it breaking the side project.

## Future Machine Changes
Once the Docker configuration is set up, re-using it or migrating to another machine is as simple as re-building the container. This completely isolates the environment for this application from the rest of the host machine. With this flexibility, the host machine can be updated, or new software can be installed without worrying about any of these changes affecting the app's environment. Additionally the Docker image is a much smaller download than transferring a large virtual machine image between machines.

When a new version of the framework/language/tooling becomes available for the app, it was difficult to update on the virtual machine because the old version may linger and the new version may break other software using the prior version. If everything is in Docker, then the version is defined in the container/configuration code. So updating the app to use the new version is as simple as pointing to the new container image or updating the configured version. And if this update fails or otherwise causes problem, the action can be easily reverted. No worries about re-installing an old version of ruby.

## Conclusion
I was originally skeptical about using Docker for a side project as I thought it would be overkill or involve lots of setup/maintance. Instead, it provides what it advertises in a simple and easy-to-use manner. If any of the above issues are a problem for a personal (or any) project, I suggest giving Docker a try.