This tutorial is written in [Markdown](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet). This is a *much* easier format to write in than HTML! This system uses [Showdown](https://github.com/showdownjs/showdown) to convert the markdown to HTML before merging it with the template.

## Code Blocks 

All the GitHub extensions should be available. For example, you can include language-specific code blocks that will be highlighted using prism.

```javascript
//comments
var x = 'Hello World!';
console.log(x);
```

And you can include `in-line code` as well.

## Tables

Tables should also work. A Showdown extension adds `class="table"` to the `<table>` element so that Bootstrap will format it nicely.

First Name | Last Name
--- | ---
Frodo | Baggins
Bilbo | Baggins
Gandalf | The Grey

## Images

You can also include images:

![cute bunny](img/bunny.jpg) 
