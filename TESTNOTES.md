# Marshmellow Test Notes

The following is meant to guide testing and development of Marshmellow.

## Starting out

`mm test.mm` outputs `test.html`

mm process all standard markdown, including marshmellow syntax. 

# Marshmellow Syntax

## Expansions in Place

Syntax: `[some word]/another word/even more words/`
Example: I really need my [best friend]/dog/one and only companion/.

This will create a link on the first word, and when you click it, the word remains a link but the text turns into the next word in the list. You can have as many as you want; they're delimited by `/`. 

## Choices and Memory

Syntax: `[ label | %(option), %(option2), %(option_n)](post-choice replacement text, if any)`
Example: `[ dinner_snack | Would you like %(chips), %(cheese), or %(buttered yams)?](I always knew you'd like %%)`

This allows you to create a choice and optional text to replace the choice delivery sentence with another sentence. 

Once the choice is made, you can query it later like this:

`Let's sit down. [ dinner_snack ? (I see you've already got some food. %%, is it?) : (I guess you haven't picked something yet?)] Great.`

%% yields the value. Can we check if it's the first word in a sentence so that we capitalize it? 

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

These functionality uses triggers to fade in, fade out different div elements. CSS styles should come shipped with Marshmallow templates.

### Implementation

1. Parse the page into blocks of marshmallow-flavored markdown. 
2. For each block, search for page links. 
3. For each link found, dynamically generate ID attributes and:
    1. Create a page object from the (first || previous block w/ button +1) to the block containing the button. 
    2. Wrap that in a page-start div.
    3. Search the blocks for the next button. If you find one, add the end of the page there.
4. Replace the markdown links with onclick links that call a pageLink function.

### Test Case

Given a marshmellow-flavored markdown file:

```
# Welcome to the test

This is a file meant to test whether or not Marshmallow-flavored Markdown can be turned into working, interactive web pages. 

Has at duis dicat tractatos, an vivendo eligendi invidunt duo. Bonorum commune no duo. Quo ex putent eligendi repudiare. Ut quo vidisse propriae efficiantur, eum eu illum zril. [Paulo]{>} perfecto est ut, his torquatos adipiscing ei.

Nec antiopam convenire ei, has fugit decore apeirian at, id diam accusam vix. Ei consul nonumes fabellas eum, cu primis perfecto vix, solet praesent cu eos. Dolor deterruisset ex cum, ius in tollit voluptaria, vis labore democritum id. In vim explicari corrumpit, mei prompta prodesset te. Mel ut nihil omittantur, viris nemore invenire ex mei.

Vel scaevola antiopam necessitatibus ad, sed id vulputate efficiendi. Quidam inermis definitiones ius an. Ad vim wisi deterruisset, aliquam veritus salutandi id pro. Harum saepe qui ei, ei vix solum mediocrem. Duo debitis accumsan ei, cum dicat noster et. Veniam dictas tractatos mea at, minim [alterum nam ut]{>}.

Modus antiopam consequuntur te vim, ea cum saperet molestiae. In simul soluta verterem nec, sit prima affert veniam te. Et lorem facilisis qui, in facer partem usu. Postea option vituperata est an.
```

Running `mm thefile.md` will produce:

```
<div id="mm-page-0">
<h1>Welcome to the test</p>

<p>This is a file meant to test whether or not Marshmallow-flavored Markdown can be turned into working, interactive web pages.</p>

<p>Has at duis dicat tractatos, an vivendo eligendi invidunt duo. Bonorum commune no duo. Quo ex putent eligendi repudiare. Ut quo vidisse propriae efficiantur, eum eu illum zril. <span class="mm-page-link" id="mm-page-link-0" onclick="mm.pageLink(1)">Paulo</span> perfecto est ut, his torquatos adipiscing ei.</p>
</div>
<div id="mm-page-1">
Nec antiopam convenire ei, has fugit decore apeirian at, id diam accusam vix. Ei consul nonumes fabellas eum, cu primis perfecto vix, solet praesent cu eos. Dolor deterruisset ex cum, ius in tollit voluptaria, vis labore democritum id. In vim explicari corrumpit, mei prompta prodesset te. Mel ut nihil omittantur, viris nemore invenire ex mei.

Vel scaevola antiopam necessitatibus ad, sed id vulputate efficiendi. Quidam inermis definitiones ius an. Ad vim wisi deterruisset, aliquam veritus salutandi id pro. Harum saepe qui ei, ei vix solum mediocrem. Duo debitis accumsan ei, cum dicat noster et. Veniam dictas tractatos mea at, minim <span class="mm-page-link" id="mm-page-link-1" onclick="mm.pageLink(2)">alterum nam ut</span>.
</div>
<div id="mm-page-2">
Modus antiopam consequuntur te vim, ea cum saperet molestiae. In simul soluta verterem nec, sit prima affert veniam te. Et lorem facilisis qui, in facer partem usu. Postea option vituperata est an.
</div>
```

