# Markdown

## Links

* Bare links work:  http://www.google.com

* So do links with a title:  [Google](http://www.google.com)

  ```markdown
  [Google](http://www.google.com)
  ```

  

* As do internal links:  [To the test link!](../archive/anewdoc.md)

  ```markdown
  [To the test link!](../archive/anewdoc.md)
  ```

  

* Footnotes are possible[^1]

* And you can link to [headers](#links)

  ```markdown
  And you can link to [headers](#links)
  
  Note that multi word headers will be lowercased with a - between them
  ```

  

## Tables

| first col            | second col |
| -------------------- | ---------- |
| Okay, this is a nice | Editor for |
| Making tables neatly | inside     |
| of markdown          | nifty      |

```markdown
Just start with:


```





[^1]: Here's the syntax:

```markdown
Footnotes are possible[^1]
...
[^1]: Here's the syntax:

```
