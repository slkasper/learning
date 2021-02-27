Markdown Cheatsheet
===================
Below is a bunch of markdown syntax. Note that multpile paragraphs and spaces aren't discussed explicitly, two spaces followed by a new line will render as a new paragraph. Alternatively, two new line returns will render a new paragraph. This neuance is particularly helpful when we have formatted text followed by regular text, which doesnt render as intended (the first time). **Note that using VS Code we can see a Markdown Preview using** `ctrl + Shift + V`.

# Header1
## Header2
### Header3
#### Header4
##### Header5
###### Header 6

Here is some plain text, **two asterics is for bold text**. *One asteric is for italic text*. ~~Two tildas is for strike through~~. We can also have `inline code as such`. [links need brackets then parenthesis](http://google.com) i.e. `[name](url)`

- Lists
- are
- easy
    - So
    - are
    - sublists

```
we can also put
various lines of code
rendered as in the shell
here
```

```py
# markdown will also render code as in the editor
# for instance some python code 
x = 1
def foo(x=0):
    "this is a doc string"
    bar = x + 1
    x = 4
    return bar
y = foo(x)
```

Images are just like urls.  They require an exclaimation point before the brackets then parenthesis `![name](image_url)`
![hipster_cat](https://ludovic.chabant.com/devblog/2011/08/25/using-mercurial-to-publish-a-piecrust-website/hipstercat.jpg)