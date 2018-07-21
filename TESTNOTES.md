# Marshmellow Test Notes

The following is meant to guide testing and development of Marshmellow.

## Starting out

`mm test.mm` outputs `test.html`

mm process all standard markdown, including marshmellow syntax. 

# Marshmellow Syntax

## Pages

Marshmellow is different from Mardown in that it chops up a *.mm file into "pages". A file `example.mm` can consist of many pages.

Consider the following:

```
I am a file and I don't do anything.

My favorite color is cheese.

There are fifteen peanuts in a guitar.
```

Three paragraphs. As it stands, compiled with `mm` this would produce a simple html file with three paragraph tags. 

However, let's make one change:

```
I am a file and I don't do [anything](>).

My favorite color is cheese.

There are fifteen peanuts in a guitar.
```

The word `anything` was converted into a **page link**. This causes everything after it--starting at the next whitespace line and including everything until either the end of the file or the next block of information that contains another page link--to be wrapped up in a page.

You might think of this as an automatic div wrapping system, since the file now looks something like this after it's compiled:

```
<page 1>
I am a file and I don't do [anything](>).
</page 1>
<page 2>
My favorite color is cheese.

There are fifteen peanuts in a guitar.
</page 2>
```

Only one page at a time is rendered on the screen. 