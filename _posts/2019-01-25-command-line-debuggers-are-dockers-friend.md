---
layout: post
title: Command Line Debbuggers Are Docker's Friend
category: Docker
tags: [docker, side-projects]
---

The goal with my side project is to keep everything reusable and not dependent upon the host machine. Using the traditional setup, only the code is local, with the compilation and execution done within docker containers. Not long after starting the project, there came a point where debugging was needed. 

## Traditional Debuggers

Up till this point, my experience with debuggers were all of the visual/IDE variety (C# and Visual Studio, Java/Kotlin and Android Studio, Objective-C and Xcode). Though powerful, these debuggers require installing the language tools on the source/host machine to process the code.
With these tools remote debugging is an option, but it requires that the tooling is installed on the source/host machine. That would defeat the "nothing on the host" requirement.

## Command Line Debuggers

Command-line debuggers don't require any IDE/tooling. Instead, they are part of the language, or a package/library. As the name implies, they enable debugging directly from the command line. The code can be paused at a breakpoint, current variables/state can be investigated, and execution can resume. The distinction is small, but it means that the code can be editor-agnostic and still allow debugging.

Crucially, it keeps the debugging code with the deployed code/machine. 

## Enabling Usage For A Docker Container

Docker images are interacted with by design, meaning adding the ability to query them with a command line tool is very simple. To do so, simply add 2 settings to the container configuration:

```yaml
container:
    tty: true
    stdin_open: true
```

* **tty** Creates a terminal for the container
* **stdin_open** Allows you to send commands to the container via standard input

Once this is set up, the specifics for the particular debugger can be set up. In my case, ruby's [byebug](https://github.com/deivid-rodriguez/byebug) was already installed, so I was ready to use it.

## Debugging

To debug, connect a terminal to the docker container by doing the following:

1. Run the container.
1. Determine the running container's id by running `docker ps`. This will show an id like `063170216bb5` for the container to be debugged.
1. Open another command prompt and attach to the above container using `docker attach <container id>`.
1. Interact with the app to get to the point being debugged and the above command prompt will break execution.

With the above debugging strategy, everything related to the project can safely remain in the docker container.
