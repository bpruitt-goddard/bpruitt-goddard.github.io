---
layout: post
title: Your Diagrams Should Be In Mermaid
category: Documentation
tags: [how-to]
---

# Problems With Traditional Diagrams

As documentation gets created to describe a system/feature/process, diagrams can be used to better illustrate or describe some of the information. However, diagram creation is typically done in a somewhat proprietary way by using a dedicated website/application. This poses three problems:

First, the difficulty in editing the diagram's images. It is usually easy to export a diagram as a simple image, but the reverse import is not normally possible. This becomes even more difficult when there is no information pointing to where/how the diagram was created, which can mean creating the diagram again for even minor edits. If there is a link pointing to the documentation, this can then lead to the next issue:

Second, linked/embedded diagrams can cause access/authorization errors. This means when users attempt to view or edit the diagram in the external site/system (particularly new users that haven't gotten all of their access), they can be greeted with access errors preventing them from completing their task. The external site is an easy-to-overlook secondary access to the primary access of the overall documentation.

Third, without strong enforcement, there could be diagrams created in a different tool than you are familiar with, or that the rest of the documentation uses. There are many diagramming sites/apps available and having to learn a new one just to perform simple edits can make things difficult.

# Mermaid Overview

Fortunately, there is an alternative approach to traditional diagrams that brings them into the documentation. This tool is the [Mermaid diagramming and charting tool](https://mermaid.js.org/). Instead of using a separate (UI) tool to generate a diagram, mermaid provides a markdown-like syntax to define the diagram to be rendered.

Mermaid provides many different diagram types. For example, here is a flowchart diagram and how it looks when rendered:

```mermaid
flowchart LR
  StepOne --> StepTwo
  StepTwo <-.-> StepThree
```

Produces this diagram:

![mermaid simple diagram](/assets/images/mermaid-simple-diagram.png)

# Mermaid Benefits

Mermaid diagrams provide a few key benefits over traditional diagrams.

First, the diagram stays next to the rest of the documentation. This makes it easier to find and edit both of these at once. This also means no access or editing issues that come from diagrams living outside of the documentation.

Second, the diagrams can render natively in Azure Dev Ops (ADO), GitHub, [and many other places](https://mermaid.js.org/ecosystem/integrations.html). This means there is no need for a separate screenshot or diagram embed.

Third, the diagrams are written in a declarative language, meaning you don't have to define the diagram flow. You simply indicate _what_ needs to be diagrammed and Mermaid will control _how_ it gets represented. This can make it easier to create and refactor diagrams.
