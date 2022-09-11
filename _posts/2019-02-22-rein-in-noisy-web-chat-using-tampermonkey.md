---
layout: post
title: Rein In Noisy Web Chat Using Tampermonkey
category: Productivity
tags: [productivity, tampermonkey]
---

Every job has a handful of tools that are used on a daily basis. The more these tools are used, the more important it is that they behave as optimally and efficiently as possible. When they don't live up to expectations out of the box, then a tool like Tampermonkey can be used to fill in the feature/functionality gaps.

As a quick summary, Tampermonkey allows a javascript script to run on specified websites. The script can do anything from re-design the page, to add new features/functionality to the page.

# Problem

In this case, the problem was with a conference site UberConference. In this app, there is a chat window. Unfortunately, the chat window is spammed with useless notifications every time someone mutes/unmutes. And even more frustratingly, the message lists both who was (un)muted and who did the (un)muting, leading to even longer messages.

![Before Image](/assets/images/uberconference-before-tampermonkey.png)

# Goal

The goal is to hide the (un)mute notifications to retake the chat window for its original purpose of chatting. These notifications can safely be hidden because they are fairly useless with the current mute state listed on each caller's icon. Additionally, if there is ever a need for these notifications, Tampermonkey provides the ability to easily enable/disable a script from the browser extensions options.

# Solution

The solution is simple - when new messages are created, hide them if they are mute notifications. This is accomplished through a library called [arrive](https://github.com/uzairfarooq/arrive/) that can monitor elements as they enter the DOM. As these elements enter, check the text for "muted" and hide the element.

Of course, the solution can hide legitimate messages with the word "muted" in them. If this is a concern, the check can be expanded to check the text more closely, or examine the style as it appears different between notifications and regular chat messages.

# Script

{% gist 08864d2d34ba44f94b63f4dd254f5bd6 %}
