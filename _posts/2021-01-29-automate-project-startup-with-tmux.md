---
layout: post
title: Automate Project Startup With tmux Templates
category: Misc
tags: [automation, how-to]
---

# Background

My current project is on an application that spans a few projects. It requires:

* React frontend
* A Handful of .net core API backends isolating various services/connections
* SQL database
* Redis cache
* Elasticsearch cluster

The datastores (SQL, Redis, Elasticsearch) were all set up using docker compose files.

# Before tmux

The original solution was running the above on an as-needed basis. This meant pointing at dev for all of the projects that were not currently being worked on to minimize the amount of services that needed to be manually run. This was good enough for a while, but became a hassle as more work cross-project work was needed, which meant a good bit of switching the configurations between pointing at dev and pointing locally. 

Additionally, as features and data changed, the environments became "mixed". Some of the needed data was worked on pointing to the dev services, meaning it needed to be re-created locally for testing a new feature.  This left the local environment in a very broken state and meant it would be easier going forward to keep the local environment up-to-date with data and features.

Eventually, an attempt was made to run everything locally all of the time. But this became cumbersome. Running everything at minimum required running:

1. A terminal window for spinning up the docker containers
2. A terminal window to run the frontend
3. A VS Code launch profile to run all of the backends

This became tedious, especially when needing to re-run all of the backend projects when only making a change to a single project due to them being grouped into one task. Additionally, this was a very manual process.

# With tmux

[tmux](https://github.com/tmux/tmux/wiki) is a terminal multiplexer that allows for splitting the terminal window into multiple windows and splitting each of those windows into potentially multiple panes. Additionally and more crucially, tmux allows for scripting an entire tmux session via a shell script. This meant being able to perform all of the above steps once at the start of the work day and having a very easy to navigate setup.

Overall tmux view of the various windows that are created:
![tmux window list](/assets/images/tmux-window-list.png)

Splitting a single window into multiple panes to group related services:
![tmux split panes](/assets/images/tmux-split-panes.png)

Below is the initial script that was created. As the screenshots show, it creates a handful of (named) windows for each piece of functionality. It also splits some of the windows into (explicitly sized) panes for related commands. This makes it easy to navigate and find a particular window when debugging or looking for output.
Additionally, the script allows for consolidating other command line features:

* A git window
* [Taskell](https://taskell.app/) for todo tracking
* A window for backend dotnet testing

Isolating each project into its own pane/window allows for a more customizable control of the setup and makes extensibility much easier. Going forward, this will be the go-to approach to set up even moderately-complicated setups due to all of these benefits.

# Script

```sh
#!/bin/bash
SESSION=`basename firstam`

tmux -2 new-session -d -s $SESSION
# git window
tmux rename-window -t $SESSION:0 git

# # docker window
tmux new-window -t $SESSION -a -n docker
# # api database
tmux select-pane -t 0
tmux send-keys "cd Api && docker-compose up" C-m
# # iris
tmux split-window -v
tmux select-pane -t 1
tmux send-keys "cd Iris && docker-compose up" C-m

# wait 5 seconds for docker to spin up
sleep 5

# api pane (0)
tmux split-window -h
tmux select-pane -t 0
tmux resize-pane -R 30
tmux send-keys "dotnet watch -p Api/src/Api run" C-m
# iris pane (1)
tmux select-pane -t 1
tmux send-keys "dotnet watch -p Iris/src/Iris run" C-m
# frontend pane (2)
tmux select-pane -t 0
tmux split-window  -v
tmux select-pane -t 1
tmux send-keys "cd frontend && yarn start:dev" C-m
# # proxy pane
tmux select-pane -t 2
tmux split-window -v
tmux send-keys "dotnet watch -p Proxy/src/Proxy run" C-m

# taskell window
tmux new-window -t $SESSION -a -n taskell
tmux send-keys "taskell ~/taskell.md" C-m

# testing window
tmux new-window -t $SESSION -a -n test
# tmux send-keys "cd Iris/tests/Common.Tests/" C-m

# focus on original pane
tmux select-window -t $SESSION:0
tmux -2 attach -t $SESSION
```