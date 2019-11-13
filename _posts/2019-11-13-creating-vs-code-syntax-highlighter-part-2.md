---
layout: post
title: Creating A VS Code Syntax Highlighting Extension (Part 2)
category: VS-Code
tags: [vs-code, how-to]
---

* [Part 1](2019-04-26-creating-vs-code-syntax-highlighter-part-1.md) - Introduction
* Part 2 - Converting To Yaml

# Problem
After creating the extension in Part 1 and adding support for most of the syntax, the code was quickly becoming bloated and hard to follow in json. The issues were exactly those outlined in Microsoft's [conversion section](https://code.visualstudio.com/api/language-extensions/syntax-highlight-guide#using-yaml-to-write-a-grammar).

For example, here is a snippet from one of the regexes
```
(\\b[-\\w]+\\b\\s*)(-?-[-\\>]\\|?|=?=[=\\>]|(?:\\.-|-\\.)-?\\>?)
```

And for a comparison to the final result, here is the equivalent yaml code. Note the improvements:
1. No need to escape the backslash 
1. The ability to break the two components by line and with a comment

```yml
begin: !regex |-
    (\b[-\w]+\b\s*) # Entity From
    (-?-[-\>]\|?|=?=[=\>]|(?:\.-|-\.)-?\>?) # Graph Link
```

# Build Setup
Microsoft provides steps for [converting to a yaml format](https://code.visualstudio.com/api/language-extensions/syntax-highlight-guide#using-yaml-to-write-a-grammar). This proved to be a good starting off point, with `js-yml` providing a simple tool to use to convert the yaml to json. But it quickly became apparent that two of the main benefits of yaml were not translating to the correct json for the regex expressions:

1. Multi-line strings will have whitespace separating them in the json unless they are double-quoted strings (requiring the whole regex to be escaped). This meant that the regex wouldn't match because it looked for that whitespace.
1. Comments on multi-line statements aren't valid.

To counter these, a custom yaml regex type was needed to be created. It striped out end of line comments and new lines to get the regex behaving properly.

```javascript
var RegexYamlType = new yaml.Type('!regex', {
  kind: 'scalar',
  construct: function (data) {
    return data
      //Remove end of line comments (space then #)
      .replace(/(^|\s)#.*/g, '')
      //Remove new lines from yaml multistring
      .replace(/\n/g, '');
  }
});
```

# Regression Check
The good news is that with the json already existing, the newly created yaml file could easily be tested for parity by comparing the output. Once the output matched the existing json, it could be concluded that the yaml was properly written.

# Improving Ease Of Use
With the new and improved yaml file ready, it was only a matter of running the conversion against the file before checking in the changes. This could prove error prone as it is a bit lengthy running the converter with the correct parameters and using the custom type. To make this more manageable, this was shortened into a one-liner added to the `package.json` file to allow anyone to use it. Ideally, this script would be added to the merge process and handled automatically, but it is easy enough to run at this point that it isn't a large pain point.

## Future Improvements
With full control over the yaml conversion, there are a few things that could be improved to improve readability:

**Variables** could be introduced to the script to clean up the various places where a regex match is re-used.

The yaml could also be **split for each diagram type** into separate files, making it easier to read and develop. Depending on the amount of additional development work that will be done, one or both of these might be explored and implemented.
