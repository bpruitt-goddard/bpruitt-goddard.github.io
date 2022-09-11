---
layout: post
title: Creating A VS Code Syntax Highlighting Extension (Part 1)
category: VS-Code
tags: [vs-code, how-to]
---

- Part 1 - Introduction
- [Part 2]({% post_url 2019-11-13-creating-vs-code-syntax-highlighter-part-2 %}) - Converting To Yaml

# Introduction

This series of posts details the journey creating a Visual Studio Code extension adding syntax highlighting to the [Mermaid programming language](https://mermaidjs.github.io) found [here](https://marketplace.visualstudio.com/items?itemName=bpruitt-goddard.mermaid-markdown-syntax-highlighting).

The Mermaid language is a markdown-like language for creating charts. These charts can be created and rendered in VS Code using an existing extension [Mermaid Preview](https://marketplace.visualstudio.com/items?itemName=vstirbu.vscode-mermaid-preview). However, these diagram scripts aren't syntax highlighted. This isn't normally a big deal, but dealing with Mermaid scripts infrequently means the language isn't fully internalized and simple syntactic mistakes can be made. Having syntax highlighting helps avoid this.

# Starting Point

Like most things in VS Code, there is extensive documentation for creating a new syntax highlighting extension, with [this being a good starting place](https://code.visualstudio.com/api/language-extensions/syntax-highlight-guide). The highlighting is defined via a TextMate grammar file. These are essentially a list of regex expressions that specify a "scope" for the matched text.

Because it uses the same grammar as Sublime Text, Atom, and other editors, there is an abundance of existing grammars to use as examples. In this case, there was even an existing grammar for Mermaid [here](https://github.com/ytisf/language-mermaid/) that uses a slightly modified TextMate grammar. This was enough of a starting point to begin work.

# Testing/Validating

Unfortunately, there does not appear to be a way of easily unit testing TextMate grammar files. Leaving the only option visually inspecting the results. To make this easier, two things were done:

First, a set of small mermaid diagrams was made that included all of the different syntax features. This would be used as a reference when viewing the output of any changes made. The diagram was added to the repository's [README](https://github.com/bpruitt-goddard/vscode-mermaid-syntax-highlight/blob/master/README.md) to also allow other contributors to use the same template. One was made for each of the three types of diagrams. As a bonus, a screenshot could be included next to each diagram to preview the changes and the progress as each syntax component was added. As a further benefit, if using a git diff tool that compares binary files, the screenshots can be compared to ensure no regression issues were introduced.

Secondly, it is important to make the testing/validation as easy and fast as possible. In this case, being able to view the results of any change without re-installing or loading the extension was best. To do this, the entire repository was stored inside the VS Code [extension folder](https://code.visualstudio.com/docs/editor/extension-gallery#_where-are-extensions-installed). This meant the extension always had the latest code. During development, keep a Visual Studio window open with the sample code from the previous point and run the **Developer: Reload Window** command. This will refresh the extension and reflect the latest changes.

# Adding Features/Support

After getting the first few (test) pieces working, the next step was to determine how to know everything that needed to be colorized. When creating the set of mermaid diagrams demonstrating the different syntax features, a checklist was created that included all of the features. Adding features thus meant going down the checklist (for each diagram type) and implementing them one by one. This made it easy to track the progress and see the sample diagrams get progressively highlighted.

# Next Steps

After getting most of the functionality completed, it started to become hard to read the giant file due to the json boilerplate and the escaping needed for every regex expression. [Part 2]({% post_url 2019-11-13-creating-vs-code-syntax-highlighter-part-2 %}) of this series will explain the conversion to YML.
