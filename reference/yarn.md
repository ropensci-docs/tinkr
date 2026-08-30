# R6 class containing XML representation of Markdown

Wrapper around an XML representation of a Markdown document. It contains
four publicly accessible slots: path, frontmatter, body, and ns.

## Details

This class is a fancy wrapper around the results of
[`to_xml()`](https://docs.ropensci.org/tinkr/reference/to_xml.md) and
has methods that make it easier to add, analyze, remove, or write
elements of your markdown document.

## Note

this requires the `sourcepos` attribute to be recorded when the object
is initialised. See
[`protect_unescaped()`](https://docs.ropensci.org/tinkr/reference/protect_unescaped.md)
for details.

## See also

[`to_md_vec()`](https://docs.ropensci.org/tinkr/reference/to_md.md) for
a way to generate the same vector from a nodelist without a yarn object

## Public fields

- `path`:

  \[`character`\] path to file on disk

- `frontmatter`:

  \[`character`\] text block at head of file

- `frontmatter_format`:

  \[`character`\] 'YAML', 'TOML' or 'JSON'

- `body`:

  \[`xml_document`\] an xml document of the (R)Markdown file.

- `ns`:

  \[`xml_document`\] an xml namespace object defining "md" to
  commonmark.

## Active bindings

- `yaml`:

  \[`character`\] **\[deprecated\]** `frontmatter`

## Methods

### Public methods

- [`yarn$new()`](#method-yarn-initialize)

- [`yarn$reset()`](#method-yarn-reset)

- [`yarn$write()`](#method-yarn-write)

- [`yarn$show()`](#method-yarn-show)

- [`yarn$head()`](#method-yarn-head)

- [`yarn$tail()`](#method-yarn-tail)

- [`yarn$md_vec()`](#method-yarn-md_vec)

- [`yarn$add_md()`](#method-yarn-add_md)

- [`yarn$append_md()`](#method-yarn-append_md)

- [`yarn$prepend_md()`](#method-yarn-prepend_md)

- [`yarn$protect_math()`](#method-yarn-protect_math)

- [`yarn$protect_curly()`](#method-yarn-protect_curly)

- [`yarn$protect_fences()`](#method-yarn-protect_fences)

- [`yarn$protect_unescaped()`](#method-yarn-protect_unescaped)

- [`yarn$get_protected()`](#method-yarn-get_protected)

- [`yarn$clone()`](#method-yarn-clone)

------------------------------------------------------------------------

### `yarn$new()`

Create a new yarn document

#### Usage

    yarn$new(path = NULL, encoding = "UTF-8", sourcepos = FALSE, ...)

#### Arguments

- `path`:

  \[`character`\] path to a markdown episode file on disk

- `encoding`:

  \[`character`\] encoding passed to
  [`readLines()`](https://rdrr.io/r/base/readLines.html)

- `sourcepos`:

  passed to
  [`commonmark::markdown_xml()`](https://docs.ropensci.org/commonmark/reference/commonmark.html).
  If `TRUE`, the source position of the file will be included as a
  "sourcepos" attribute. Defaults to `FALSE`.

- `...`:

  arguments passed on to
  [`to_xml()`](https://docs.ropensci.org/tinkr/reference/to_xml.md).

#### Returns

A new yarn object containing an XML representation of a (R)Markdown
file.

#### Examples

    path <- system.file("extdata", "example1.md", package = "tinkr")
    ex1 <- tinkr::yarn$new(path)
    ex1
    path2 <- system.file("extdata", "example2.Rmd", package = "tinkr")
    ex2 <- tinkr::yarn$new(path2)
    ex2

------------------------------------------------------------------------

### `yarn$reset()`

reset a yarn document from the original file

#### Usage

    yarn$reset()

#### Examples

    path <- system.file("extdata", "example1.md", package = "tinkr")
    ex1 <- tinkr::yarn$new(path)
    # OH NO
    ex1$body
    ex1$body <- xml2::xml_missing()
    ex1$reset()
    ex1$body

------------------------------------------------------------------------

### `yarn$write()`

Write a yarn document to Markdown/R Markdown

#### Usage

    yarn$write(path = NULL, stylesheet_path = stylesheet())

#### Arguments

- `path`:

  path to the file you want to write

- `stylesheet_path`:

  path to the xsl stylesheet to convert XML to markdown.

#### Examples

    path <- system.file("extdata", "example1.md", package = "tinkr")
    ex1 <- tinkr::yarn$new(path)
    ex1
    tmp <- tempfile()
    try(readLines(tmp)) # nothing in the file
    ex1$write(tmp)
    head(readLines(tmp)) # now a markdown file
    unlink(tmp)

------------------------------------------------------------------------

### `yarn$show()`

show the markdown contents on the screen

#### Usage

    yarn$show(lines = TRUE, stylesheet_path = stylesheet())

#### Arguments

- `lines`:

  a subset of elements to show. Defaults to `TRUE`, which shows all
  lines of the output. This can be either logical or numeric.

- `stylesheet_path`:

  path to the xsl stylesheet to convert XML to markdown.

#### Returns

a character vector with one line for each line in the output

#### Examples

    path <- system.file("extdata", "example2.Rmd", package = "tinkr")
    ex2 <- tinkr::yarn$new(path)
    ex2$head(5)
    ex2$tail(5)
    ex2$show()

------------------------------------------------------------------------

### `yarn$head()`

show the head of the markdown contents on the screen

#### Usage

    yarn$head(n = 6L, stylesheet_path = stylesheet())

#### Arguments

- `n`:

  the number of elements to show from the top. Negative numbers

- `stylesheet_path`:

  path to the xsl stylesheet to convert XML to markdown.

#### Returns

a character vector with `n` elements

------------------------------------------------------------------------

### `yarn$tail()`

show the tail of the markdown contents on the screen

#### Usage

    yarn$tail(n = 6L, stylesheet_path = stylesheet())

#### Arguments

- `n`:

  the number of elements to show from the bottom. Negative numbers

- `stylesheet_path`:

  path to the xsl stylesheet to convert XML to markdown.

#### Returns

a character vector with `n` elements

------------------------------------------------------------------------

### `yarn$md_vec()`

query and extract markdown elements

#### Usage

    yarn$md_vec(xpath = NULL, stylesheet_path = stylesheet())

#### Arguments

- `xpath`:

  a valid XPath expression

- `stylesheet_path`:

  path to the xsl stylesheet to convert XML to markdown.

#### Returns

a vector of markdown elements generated from the query

#### Examples

    path <- system.file("extdata", "example1.md", package = "tinkr")
    ex <- tinkr::yarn$new(path)
    # all headings
    ex$md_vec(".//md:heading")
    # all headings greater than level 3
    ex$md_vec(".//md:heading[@level>3]")
    # all links
    ex$md_vec(".//md:link")
    # all links that are part of lists
    ex$md_vec(".//md:list//md:link")
    # all code
    ex$md_vec(".//md:code | .//md:code_block")

------------------------------------------------------------------------

### `yarn$add_md()`

add an arbitrary Markdown element to the document

#### Usage

    yarn$add_md(md, where = 0L)

#### Arguments

- `md`:

  a string of markdown formatted text.

- `where`:

  the location in the document to add your markdown text. This is passed
  on to
  [`xml2::xml_add_child()`](http://xml2.r-lib.org/reference/xml_replace.md).
  Defaults to 0, which indicates the very top of the document.

#### Examples

    path <- system.file("extdata", "example2.Rmd", package = "tinkr")
    ex <- tinkr::yarn$new(path)
    # two headings, no lists
    xml2::xml_find_all(ex$body, "md:heading", ex$ns)
    xml2::xml_find_all(ex$body, "md:list", ex$ns)
    ex$add_md(
      "# Hello\n\nThis is *new* formatted text from `{tinkr}`!",
      where = 1L
    )$add_md(
      " - This\n - is\n - a new list",
      where = 2L
    )
    # three headings
    xml2::xml_find_all(ex$body, "md:heading", ex$ns)
    xml2::xml_find_all(ex$body, "md:list", ex$ns)
    tmp <- tempfile()
    ex$write(tmp)
    readLines(tmp, n = 20)

------------------------------------------------------------------------

### `yarn$append_md()`

append abritrary markdown to a node or set of nodes

#### Usage

    yarn$append_md(md, nodes = NULL, space = TRUE)

#### Arguments

- `md`:

  a string of markdown formatted text.

- `nodes`:

  an XPath expression that evaulates to object of class `xml_node` or
  `xml_nodeset` that are all either inline or block nodes (never both).
  The XPath expression is passed to
  [`xml2::xml_find_all()`](http://xml2.r-lib.org/reference/xml_find_all.md).
  If you want to append a specific node, you can pass that node to this
  parameter.

- `space`:

  if `TRUE`, inline nodes will have a space inserted before they are
  appended.

#### Details

this is similar to the
[`add_md()`](https://docs.ropensci.org/tinkr/reference/add_md.md) method
except that it can do the following:

1.  append content after a *specific* node or set of nodes

2.  append content to multiple places in the document

#### Examples

    path <- system.file("extdata", "example2.Rmd", package = "tinkr")
    ex <- tinkr::yarn$new(path)
    # append a note after the first heading

    txt <- c("> Hello from *tinkr*!", ">", ">  :heart: R")
    ex$append_md(txt, ".//md:heading[1]")$head(20)

------------------------------------------------------------------------

### `yarn$prepend_md()`

prepend arbitrary markdown to a node or set of nodes

#### Usage

    yarn$prepend_md(md, nodes = NULL, space = TRUE)

#### Arguments

- `md`:

  a string of markdown formatted text.

- `nodes`:

  an XPath expression that evaulates to object of class `xml_node` or
  `xml_nodeset` that are all either inline or block nodes (never both).
  The XPath expression is passed to
  [`xml2::xml_find_all()`](http://xml2.r-lib.org/reference/xml_find_all.md).
  If you want to append a specific node, you can pass that node to this
  parameter.

- `space`:

  if `TRUE`, inline nodes will have a space inserted before they are
  prepended.

#### Details

this is similar to the
[`add_md()`](https://docs.ropensci.org/tinkr/reference/add_md.md) method
except that it can do the following:

1.  prepend content after a *specific* node or set of nodes

2.  prepend content to multiple places in the document

#### Examples

    path <- system.file("extdata", "example2.Rmd", package = "tinkr")
    ex <- tinkr::yarn$new(path)

    # prepend a table description to the birds table
    ex$prepend_md("Table: BIRDS, NERDS", ".//md:table[1]")$tail(20)

------------------------------------------------------------------------

### `yarn$protect_math()`

Protect math blocks from being escaped

#### Usage

    yarn$protect_math()

#### Examples

    path <- system.file("extdata", "math-example.md", package = "tinkr")
    ex <- tinkr::yarn$new(path)
    ex$tail() # math blocks are escaped :(
    ex$protect_math()$tail() # math blocks are no longer escaped :)

------------------------------------------------------------------------

### `yarn$protect_curly()`

Protect curly phrases `{likethat}` from being escaped

#### Usage

    yarn$protect_curly()

#### Examples

    path <- system.file("extdata", "basic-curly.md", package = "tinkr")
    ex <- tinkr::yarn$new(path)
    ex$protect_curly()$head()

------------------------------------------------------------------------

### `yarn$protect_fences()`

Protect fences of Pandoc fenced divs `:::`

#### Usage

    yarn$protect_fences()

#### Examples

    path <- system.file("extdata", "fenced-divs.md", package = "tinkr")
    ex <- tinkr::yarn$new(path)
    ex$protect_fences()$head()

------------------------------------------------------------------------

### `yarn$protect_unescaped()`

Protect unescaped square braces from being escaped.

This is applied by default when you use `yarn$new(sourcepos = TRUE)`.

#### Usage

    yarn$protect_unescaped()

#### Examples

    path <- system.file("extdata", "basic-curly.md", package = "tinkr")
    ex <- tinkr::yarn$new(path, sourcepos = TRUE, unescaped = FALSE)
    ex$tail()
    ex$protect_unescaped()$tail()

------------------------------------------------------------------------

### `yarn$get_protected()`

Return nodes whose contents are protected from being escaped

#### Usage

    yarn$get_protected(type = NULL)

#### Arguments

- `type`:

  a character vector listing the protections to be included. Defaults to
  `NULL`, which includes all protected nodes:

  - math: via the
    [`protect_math()`](https://docs.ropensci.org/tinkr/reference/protect_math.md)
    function

  - curly: via the
    [`protect_curly()`](https://docs.ropensci.org/tinkr/reference/protect_curly.md)
    function

  - fence: via the
    [`protect_fences()`](https://docs.ropensci.org/tinkr/reference/protect_fences.md)
    function

  - unescaped: via the
    [`protect_unescaped()`](https://docs.ropensci.org/tinkr/reference/protect_unescaped.md)
    function

#### Examples

    path <- system.file("extdata", "basic-curly.md", package = "tinkr")
    ex <- tinkr::yarn$new(path, sourcepos = TRUE)
    # protect curly braces
    ex$protect_curly()
    # add fenced divs and protect then
    ex$add_md(c("::: alert\n",
      "blabla",
      ":::")
    )
    ex$protect_fences()
    # add math and protect it
    ex$add_md(c("## math\n",
      "$c^2 = a^2 + b^2$\n",
      "$$",
      "\\sum_{i}^k = x_i + 1",
      "$$\n")
    )
    ex$protect_math()
    # get protected now shows all the protected nodes
    ex$get_protected()
    ex$get_protected(c("math", "curly")) # only show the math and curly

------------------------------------------------------------------------

### `yarn$clone()`

The objects of this class are cloneable with this method.

#### Usage

    yarn$clone(deep = FALSE)

#### Arguments

- `deep`:

  Whether to make a deep clone.

## Examples

``` r

## ------------------------------------------------
## Method `yarn$new()`
## ------------------------------------------------

path <- system.file("extdata", "example1.md", package = "tinkr")
ex1 <- tinkr::yarn$new(path)
ex1
#> <yarn>
#>   Public:
#>     add_md: function (md, where = 0L) 
#>     append_md: function (md, nodes = NULL, space = TRUE) 
#>     body: xml_document, xml_node
#>     clone: function (deep = FALSE) 
#>     frontmatter: --- title: "What have these birds been studied for? Quer ...
#>     frontmatter_format: YAML
#>     get_protected: function (type = NULL) 
#>     head: function (n = 6L, stylesheet_path = stylesheet()) 
#>     initialize: function (path = NULL, encoding = "UTF-8", sourcepos = FALSE, 
#>     md_vec: function (xpath = NULL, stylesheet_path = stylesheet()) 
#>     ns: http://commonmark.org/xml/1.0
#>     path: /github/home/R/x86_64-pc-linux-gnu-library/4.6/tinkr/ext ...
#>     prepend_md: function (md, nodes = NULL, space = TRUE) 
#>     protect_curly: function () 
#>     protect_fences: function () 
#>     protect_math: function () 
#>     protect_unescaped: function () 
#>     reset: function () 
#>     show: function (lines = TRUE, stylesheet_path = stylesheet()) 
#>     tail: function (n = 6L, stylesheet_path = stylesheet()) 
#>     write: function (path = NULL, stylesheet_path = stylesheet()) 
#>     yaml: active binding
#>   Private:
#>     encoding: UTF-8
#>     md_lines: function (path = NULL, stylesheet = NULL) 
#>     sourcepos: FALSE
path2 <- system.file("extdata", "example2.Rmd", package = "tinkr")
ex2 <- tinkr::yarn$new(path2)
ex2
#> <yarn>
#>   Public:
#>     add_md: function (md, where = 0L) 
#>     append_md: function (md, nodes = NULL, space = TRUE) 
#>     body: xml_document, xml_node
#>     clone: function (deep = FALSE) 
#>     frontmatter: --- title: "Untitled" author: "M. Salmon" date: "Septemb ...
#>     frontmatter_format: YAML
#>     get_protected: function (type = NULL) 
#>     head: function (n = 6L, stylesheet_path = stylesheet()) 
#>     initialize: function (path = NULL, encoding = "UTF-8", sourcepos = FALSE, 
#>     md_vec: function (xpath = NULL, stylesheet_path = stylesheet()) 
#>     ns: http://commonmark.org/xml/1.0
#>     path: /github/home/R/x86_64-pc-linux-gnu-library/4.6/tinkr/ext ...
#>     prepend_md: function (md, nodes = NULL, space = TRUE) 
#>     protect_curly: function () 
#>     protect_fences: function () 
#>     protect_math: function () 
#>     protect_unescaped: function () 
#>     reset: function () 
#>     show: function (lines = TRUE, stylesheet_path = stylesheet()) 
#>     tail: function (n = 6L, stylesheet_path = stylesheet()) 
#>     write: function (path = NULL, stylesheet_path = stylesheet()) 
#>     yaml: active binding
#>   Private:
#>     encoding: UTF-8
#>     md_lines: function (path = NULL, stylesheet = NULL) 
#>     sourcepos: FALSE

## ------------------------------------------------
## Method `yarn$reset()`
## ------------------------------------------------


path <- system.file("extdata", "example1.md", package = "tinkr")
ex1 <- tinkr::yarn$new(path)
# OH NO
ex1$body
#> {xml_document}
#> <document xmlns="http://commonmark.org/xml/1.0">
#>  [1] <paragraph>\n  <text xml:space="preserve">In the </text>\n  <link destin ...
#>  [2] <heading level="3">\n  <text xml:space="preserve">Getting a list of 50 s ...
#>  [3] <paragraph>\n  <text xml:space="preserve">For more details about the fol ...
#>  [4] <code_block info="r" xml:space="preserve" name=""># polygon for filterin ...
#>  [5] <paragraph>\n  <text xml:space="preserve">For the sake of simplicity, we ...
#>  [6] <code_block info="r" xml:space="preserve" name="">species &lt;- ebd %&gt ...
#>  [7] <paragraph>\n  <text xml:space="preserve">The species are Carrion Crow,  ...
#>  [8] <heading level="3">\n  <text xml:space="preserve">Querying the scientifi ...
#>  [9] <paragraph>\n  <text xml:space="preserve">Just like rOpenSci has a taxon ...
#> [10] <paragraph>\n  <text xml:space="preserve">We shall use </text>\n  <code  ...
#> [11] <paragraph>\n  <text xml:space="preserve">We first define a function ret ...
#> [12] <paragraph>\n  <text xml:space="preserve">We use </text>\n  <code xml:sp ...
#> [13] <code_block info="r" xml:space="preserve" name="">.get_papers &lt;- func ...
#> [14] <code_block xml:space="preserve" name="">##  [1] "Great spotted cuckoo n ...
#> [15] <paragraph>\n  <text xml:space="preserve">If we were working on a scient ...
#> [16] <paragraph>\n  <text xml:space="preserve">We then apply this function to ...
#> [17] <code_block info="r" xml:space="preserve" name="">get_papers &lt;- ratel ...
#> [18] <code_block xml:space="preserve" name="">## [1] 522\n</code_block>
#> [19] <code_block info="r" xml:space="preserve" name="">all_papers &lt;- uniqu ...
#> [20] <code_block xml:space="preserve" name="">## [1] 378\n</code_block>
#> ...
ex1$body <- xml2::xml_missing()
ex1$reset()
ex1$body
#> {xml_document}
#> <document xmlns="http://commonmark.org/xml/1.0">
#>  [1] <paragraph>\n  <text xml:space="preserve">In the </text>\n  <link destin ...
#>  [2] <heading level="3">\n  <text xml:space="preserve">Getting a list of 50 s ...
#>  [3] <paragraph>\n  <text xml:space="preserve">For more details about the fol ...
#>  [4] <code_block info="r" xml:space="preserve" name=""># polygon for filterin ...
#>  [5] <paragraph>\n  <text xml:space="preserve">For the sake of simplicity, we ...
#>  [6] <code_block info="r" xml:space="preserve" name="">species &lt;- ebd %&gt ...
#>  [7] <paragraph>\n  <text xml:space="preserve">The species are Carrion Crow,  ...
#>  [8] <heading level="3">\n  <text xml:space="preserve">Querying the scientifi ...
#>  [9] <paragraph>\n  <text xml:space="preserve">Just like rOpenSci has a taxon ...
#> [10] <paragraph>\n  <text xml:space="preserve">We shall use </text>\n  <code  ...
#> [11] <paragraph>\n  <text xml:space="preserve">We first define a function ret ...
#> [12] <paragraph>\n  <text xml:space="preserve">We use </text>\n  <code xml:sp ...
#> [13] <code_block info="r" xml:space="preserve" name="">.get_papers &lt;- func ...
#> [14] <code_block xml:space="preserve" name="">##  [1] "Great spotted cuckoo n ...
#> [15] <paragraph>\n  <text xml:space="preserve">If we were working on a scient ...
#> [16] <paragraph>\n  <text xml:space="preserve">We then apply this function to ...
#> [17] <code_block info="r" xml:space="preserve" name="">get_papers &lt;- ratel ...
#> [18] <code_block xml:space="preserve" name="">## [1] 522\n</code_block>
#> [19] <code_block info="r" xml:space="preserve" name="">all_papers &lt;- uniqu ...
#> [20] <code_block xml:space="preserve" name="">## [1] 378\n</code_block>
#> ...

## ------------------------------------------------
## Method `yarn$write()`
## ------------------------------------------------

path <- system.file("extdata", "example1.md", package = "tinkr")
ex1 <- tinkr::yarn$new(path)
ex1
#> <yarn>
#>   Public:
#>     add_md: function (md, where = 0L) 
#>     append_md: function (md, nodes = NULL, space = TRUE) 
#>     body: xml_document, xml_node
#>     clone: function (deep = FALSE) 
#>     frontmatter: --- title: "What have these birds been studied for? Quer ...
#>     frontmatter_format: YAML
#>     get_protected: function (type = NULL) 
#>     head: function (n = 6L, stylesheet_path = stylesheet()) 
#>     initialize: function (path = NULL, encoding = "UTF-8", sourcepos = FALSE, 
#>     md_vec: function (xpath = NULL, stylesheet_path = stylesheet()) 
#>     ns: http://commonmark.org/xml/1.0
#>     path: /github/home/R/x86_64-pc-linux-gnu-library/4.6/tinkr/ext ...
#>     prepend_md: function (md, nodes = NULL, space = TRUE) 
#>     protect_curly: function () 
#>     protect_fences: function () 
#>     protect_math: function () 
#>     protect_unescaped: function () 
#>     reset: function () 
#>     show: function (lines = TRUE, stylesheet_path = stylesheet()) 
#>     tail: function (n = 6L, stylesheet_path = stylesheet()) 
#>     write: function (path = NULL, stylesheet_path = stylesheet()) 
#>     yaml: active binding
#>   Private:
#>     encoding: UTF-8
#>     md_lines: function (path = NULL, stylesheet = NULL) 
#>     sourcepos: FALSE
tmp <- tempfile()
try(readLines(tmp)) # nothing in the file
#> Warning: cannot open file '/tmp/Rtmp19kCX0/file5973cb44bf2': No such file or directory
#> Error in file(con, "r") : cannot open the connection
ex1$write(tmp)
head(readLines(tmp)) # now a markdown file
#> [1] "---"                                                                               
#> [2] "title: \"What have these birds been studied for? Querying science outputs with R\""
#> [3] "slug: birds-science"                                                               
#> [4] "authors:"                                                                          
#> [5] "  - name: Maëlle Salmon"                                                           
#> [6] "    url: https://masalmon.eu/"                                                     
unlink(tmp)

## ------------------------------------------------
## Method `yarn$show()`
## ------------------------------------------------

path <- system.file("extdata", "example2.Rmd", package = "tinkr")
ex2 <- tinkr::yarn$new(path)
ex2$head(5)
#> ---
#> title: "Untitled"
#> author: "M. Salmon"
#> date: "September 6, 2018"
#> output: html_document
ex2$tail(5)
#> | Cyanistes caeruleus        | Eurasian Blue Tit   | 233 | 
#> | Chroicocephalus ridibundus | Black-headed Gull   | 223 | 
#> 
#> blabla
#> 
ex2$show()
#> ---
#> title: "Untitled"
#> author: "M. Salmon"
#> date: "September 6, 2018"
#> output: html_document
#> ---
#> 
#> ```{r setup, include=FALSE, eval=TRUE}
#> knitr::opts_chunk$set(echo = TRUE)
#> ```
#> 
#> ## R Markdown
#> 
#> This is an ~~R Markdown document~~. Markdown is a simple formatting syntax for authoring HTML, PDF, and MS Word documents. For more details on using R Markdown see <http://rmarkdown.rstudio.com>.
#> 
#> When you click the **Knit** button a document will be generated that includes both content as well as the output of any embedded R code chunks within the document. You can embed an R code chunk like this:
#> 
#> ```{r, eval=TRUE, echo=TRUE}
#> summary(cars)
#> ```
#> 
#> ## Including Plots
#> 
#> You can also embed plots, for example:
#> 
#> ```{python, fig.cap="pretty plot", echo=-c(1, 2), eval=TRUE}
#> plot(pressure)
#> ```
#> 
#> ```{python}
#> plot(pressure)
#> ```
#> 
#> Non-RMarkdown blocks are also considered
#> 
#> ```bash
#> echo "this is an unevaluted bash block"
#> ```
#> 
#> ```
#> This is an ambiguous code block
#> ```
#> 
#> Note that the `echo = FALSE` parameter was added to the code chunk to prevent printing of the R code that generated the plot.
#> 
#> | scientific\_name            | common\_name         | n   | 
#> | :------------------------- | :------------------ | --: |
#> | Corvus corone              | Carrion Crow        | 288 | 
#> | Turdus merula              | Eurasian Blackbird  | 285 | 
#> | Anas platyrhynchos         | Mallard             | 273 | 
#> | Fulica atra                | Eurasian Coot       | 268 | 
#> | Parus major                | Great Tit           | 266 | 
#> | Podiceps cristatus         | Great Crested Grebe | 254 | 
#> | Ardea cinerea              | Gray Heron          | 236 | 
#> | Cygnus olor                | Mute Swan           | 234 | 
#> | Cyanistes caeruleus        | Eurasian Blue Tit   | 233 | 
#> | Chroicocephalus ridibundus | Black-headed Gull   | 223 | 
#> 
#> blabla
#> 

## ------------------------------------------------
## Method `yarn$md_vec()`
## ------------------------------------------------

path <- system.file("extdata", "example1.md", package = "tinkr")
ex <- tinkr::yarn$new(path)
# all headings
ex$md_vec(".//md:heading")
#> [1] "### Getting a list of 50 species from occurrence data"
#> [2] "### Querying the scientific literature"               
#> [3] "### Querying scientific open data"                    
#> [4] "### Conclusion"                                       
#> [5] "#### Scientific literature access"                    
#> [6] "#### Scientific data access… and publication with R"  
#> [7] "#### No more birding? No, your turn!"                 
# all headings greater than level 3
ex$md_vec(".//md:heading[@level>3]")
#> [1] "#### Scientific literature access"                  
#> [2] "#### Scientific data access… and publication with R"
#> [3] "#### No more birding? No, your turn!"               
# all links
ex$md_vec(".//md:link")
#>  [1] "[second post of the series where we obtained data from\neBird](https://ropensci.org/blog/2018/08/21/birds-radolfzell/)"                                                                           
#>  [2] "[the fourth post of the\nseries](https://ropensci.org/blog/2018/09/04/birds-taxo-traits/)"                                                                                                        
#>  [3] "[previous post\nof the series](https://ropensci.org/blog/2018/08/21/birds-radolfzell/)"                                                                                                           
#>  [4] "[(`glue::glue_collapse(species, sep = \", \", last = \" and \")`)](https://twitter.com/LucyStats/status/1031938964796657665?s=19)"                                                                
#>  [5] "[`taxize`](https://github.com/ropensci/taxize)"                                                                                                                                                   
#>  [6] "[`spocc`](https://github.com/ropensci/spocc)"                                                                                                                                                     
#>  [7] "[`fulltext`](https://github.com/ropensci/fulltext)"                                                                                                                                               
#>  [8] "[\"Investigating the impact of media on demand for wildlife: A case\nstudy of Harry Potter and the UK trade in\nowls\"](http://journals.plos.org/plosone/article?id=10.1371/journal.pone.0182368)"
#>  [9] "[`cites`](https://github.com/ecohealthalliance/cites/)"                                                                                                                                           
#> [10] "[`rcites`](https://ibartomeus.github.io/rcites/)"                                                                                                                                                 
#> [11] "[`wordcloud`\npackage](https://cran.r-project.org/web/packages/wordcloud/index.html)"                                                                                                             
#> [12] "[`wordcloud2`\npackage](https://github.com/Lchiffon/wordcloud2)"                                                                                                                                  
#> [13] "[from\nPhylopic](http://phylopic.org/image/6209c9be-060e-4d7f-bc74-a75f3ccf4629/)"                                                                                                                
#> [14] "[DataONE](https://www.dataone.org/)"                                                                                                                                                              
#> [15] "[searching\nDataONE\nvignette](https://github.com/DataONEorg/rdataone/blob/master/vignettes/searching-dataone.Rmd)"                                                                               
#> [16] "[download data\nvignette](https://github.com/DataONEorg/rdataone/blob/master/vignettes/download-data.Rmd)"                                                                                        
#> [17] "[`europepmc`](https://github.com/ropensci/europepmc)"                                                                                                                                             
#> [18] "[`jstor`](https://github.com/ropensci/jstor)"                                                                                                                                                     
#> [19] "[`suppdata`](https://github.com/ropensci/suppdata)"                                                                                                                                               
#> [20] "[much\nmore](https://ropensci.org/packages/)"                                                                                                                                                     
#> [21] "[`dataone`\npackage](https://github.com/DataONEorg/rdataone)"                                                                                                                                     
#> [22] "[`rfigshare`](https://github.com/ropensci/rfigshare)"                                                                                                                                             
#> [23] "[Figshare](https://figshare.com/)"                                                                                                                                                                
#> [24] "[`EML` package](https://github.com/ropensci/EML)"                                                                                                                                                 
#> [25] "[unconf\n`dataspice` project](https://github.com/ropenscilabs/dataspice)"                                                                                                                         
#> [26] "[here](https://ropensci.org/packages/)"                                                                                                                                                           
#> [27] "[How to identify spots for birding using open geographical\ndata](https://ropensci.org/blog/2018/08/14/where-to-bird/)"                                                                           
#> [28] "[How to obtain bird occurrence data in\nR](https://ropensci.org/blog/2018/08/21/birds-radolfzell/)"                                                                                               
#> [29] "[How to extract text from old natural history\ndrawings](https://ropensci.org/blog/2018/08/28/birds-ocr/)"                                                                                        
#> [30] "[How to complement an occurrence dataset with taxonomy and trait\ninformation](https://ropensci.org/blog/2018/09/04/birds-taxo-traits/)"                                                          
#> [31] "[our friendly discussion\nforum](https://discuss.ropensci.org/c/usecases)"                                                                                                                        
# all links that are part of lists
ex$md_vec(".//md:list//md:link")
#> [1] "[download data\nvignette](https://github.com/DataONEorg/rdataone/blob/master/vignettes/download-data.Rmd)"                              
#> [2] "[How to identify spots for birding using open geographical\ndata](https://ropensci.org/blog/2018/08/14/where-to-bird/)"                 
#> [3] "[How to obtain bird occurrence data in\nR](https://ropensci.org/blog/2018/08/21/birds-radolfzell/)"                                     
#> [4] "[How to extract text from old natural history\ndrawings](https://ropensci.org/blog/2018/08/28/birds-ocr/)"                              
#> [5] "[How to complement an occurrence dataset with taxonomy and trait\ninformation](https://ropensci.org/blog/2018/09/04/birds-taxo-traits/)"
# all code
ex$md_vec(".//md:code | .//md:code_block")
#>  [1] "```r\n# polygon for filtering\nlandkreis_konstanz <- osmdata::getbb(\"Landkreis Konstanz\",\n                             format_out = \"sf_polygon\")\ncrs <- sf::st_crs(landkreis_konstanz)\n\n# get and filter data\nf_out_ebd <- \"ebird/ebd_lk_konstanz.txt\"\n\nlibrary(\"magrittr\")\n\nebd <- auk::read_ebd(f_out_ebd) %>%\n  sf::st_as_sf(coords = c(\"longitude\", \"latitude\"),\n                crs = crs)\n\nin_indices <- sf::st_within(ebd, landkreis_konstanz)\n\nebd <- dplyr::filter(ebd, lengths(in_indices) > 0)\n\nebd <- as.data.frame(ebd)\n\nebd <- dplyr::filter(ebd, approved, lubridate::year(observation_date) > 2010)\n```"                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  
#>  [2] "```r\nspecies <- ebd %>%\n  dplyr::count(common_name, sort = TRUE) %>%\n  head(n = 50) %>%\n  dplyr::pull(common_name)\n```"                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               
#>  [3] "`glue::glue_collapse(species, sep = \", \", last = \" and \")`"                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            
#>  [4] "`taxize`"                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  
#>  [5] "`spocc`"                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   
#>  [6] "`fulltext`"                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                
#>  [7] "`fulltext`"                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                
#>  [8] "`tidytext`"                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                
#>  [9] "`dplyr::bind_rows`"                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        
#> [10] "`fulltext`"                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                
#> [11] "```r\n.get_papers <- function(species){\n  species %>%\n    tolower() %>%\n    fulltext::ft_search() %>%\n    fulltext::ft_get() %>%\n    fulltext::ft_collect() %>%\n    fulltext::ft_chunks(c(\"title\", \"abstract\")) %>%\n    fulltext::ft_tabularize() %>%\n    dplyr::bind_rows()\n}\n\n.get_papers(species[1]) %>%\n  dplyr::pull(title)\n```"                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     
#> [12] "```\n##  [1] \"Great spotted cuckoo nestlings have no antipredatory effect on magpie or carrion crow host nests in southern Spain\"\n##  [2] \"Donor-Control of Scavenging Food Webs at the Land-Ocean Interface\"\n##  [3] \"Formal comment to Soler et al.: Great spotted cuckoo nestlings have no antipredatory effect on magpie or carrion crow host nests in southern Spain\"\n##  [4] \"Socially Driven Consistent Behavioural Differences during Development in Common Ravens and Carrion Crows\"\n##  [5] \"Behavioral Responses to Inequity in Reward Distribution and Working Effort in Crows and Ravens\"\n##  [6] \"Early Duplication of a Single MHC IIB Locus Prior to the Passerine Radiations\"\n##  [7] \"Investigating the impact of media on demand for wildlife: A case study of Harry Potter and the UK trade in owls\"\n##  [8] \"New Caledonian Crows Rapidly Solve a Collaborative Problem without Cooperative Cognition\"\n##  [9] \"Nest Predation Deviates from Nest Predator Abundance in an Ecologically Trapped Bird\"\n## [10] \"Dietary Compositions and Their Seasonal Shifts in Japanese Resident Birds, Estimated from the Analysis of Volunteer Monitoring Data\"\n```"
#> [13] "`cites`"                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   
#> [14] "`rcites`"                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  
#> [15] "```r\nget_papers <- ratelimitr::limit_rate(.get_papers,\n                                     rate = ratelimitr::rate(1, 2))\n\nall_papers <- purrr::map_df(species, get_papers)\n\nnrow(all_papers)\n```"                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 
#> [16] "```\n## [1] 522\n```"                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      
#> [17] "```r\nall_papers <- unique(all_papers)\n\nnrow(all_papers)\n```"                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           
#> [18] "```\n## [1] 378\n```"                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      
#> [19] "```r\nlibrary(\"tidytext\")\nlibrary(\"rcorpora\")\n\nstopwords <- corpora(\"words/stopwords/en\")$stopWords\n\nall_papers %>%\n  dplyr::group_by(title, abstract) %>%\n  dplyr::summarise(text = paste(title, abstract)) %>%\n  dplyr::ungroup() %>%\n  unnest_tokens(word, text) %>%\n  dplyr::filter(!word %in% stopwords) %>%\n  dplyr::count(word, sort = TRUE) -> words\n```"                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        
#> [20] "```r\nhead(words, n = 10)\n```"                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            
#> [21] "```\n##           word   n\n## 1      species 754\n## 2        birds 514\n## 3        virus 270\n## 4        avian 268\n## 5         bird 262\n## 6        study 243\n## 7     breeding 231\n## 8         wild 227\n## 9  populations 217\n## 10  population 213\n```"                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     
#> [22] "`wordcloud`"                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               
#> [23] "```r\nlibrary(\"wordcloud\")\n\nwith(words, wordcloud(word, n, max.words = 100))\n```"                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     
#> [24] "`wordcloud2`"                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              
#> [25] "```r\nbird <- words %>%\n  head(n = 100) %>%\n  wordcloud2::wordcloud2(figPath = \"bird.png\",\n                       color = \"black\", size = 1.5)\n# https://www.r-graph-gallery.com/196-the-wordcloud2-library/\nhtmlwidgets::saveWidget(bird,\n                        \"tmp.html\",\n                        selfcontained = F)\n```"                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               
#> [26] "`webshot`"                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 
#> [27] "`delay`"                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   
#> [28] "```r\nmagick::image_read(\"screenshot.png\")\n```"                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         
#> [29] "```r\n.get_meta <- function(species){\n\n  cn <- dataone::CNode(\"PROD\")\n  search <- list(q = glue::glue(\"abstract:{species}\"),\n                        fl = \"id,title,abstract\",\n                        sort = \"dateUploaded+desc\")\n\n  result <- dataone::query(cn, solrQuery = search,\n                           as=\"data.frame\")\n\n  if(nrow(result) == 0){\n    NULL\n  }else{\n    # otherwise one line by version\n  result <- unique(result)\n\n  tibble::tibble(species = species,\n                 title = result$title,\n                 abstract = result$abstract)\n  }\n}\n```"                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           
#> [30] "```r\nget_meta <- ratelimitr::limit_rate(.get_meta,\n                                     rate = ratelimitr::rate(1, 2))\n\nall_meta <- purrr::map_df(species, get_meta)\n\nnrow(all_meta)\n```"                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           
#> [31] "```\n## [1] 266\n```"                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      
#> [32] "```r\nlength(unique(all_meta$species))\n```"                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               
#> [33] "```\n## [1] 35\n```"                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       
#> [34] "```r\nall_meta <- unique(all_meta[,c(\"title\", \"abstract\")])\n\nnrow(all_meta)\n```"                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    
#> [35] "```\n## [1] 104\n```"                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      
#> [36] "```r\nall_meta %>%\n  dplyr::group_by(title, abstract) %>%\n  dplyr::summarise(text = paste(title, abstract)) %>%\n  dplyr::ungroup() %>%\n  unnest_tokens(word, text) %>%\n  dplyr::filter(!word %in% stopwords) %>%\n  dplyr::count(word, sort = TRUE) -> data_words\n\nhead(data_words, n = 10)\n```"                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   
#> [37] "```\n## # A tibble: 10 x 2\n##    word           n\n##    <chr>      <int>\n##  1 data         153\n##  2 species      120\n##  3 birds         94\n##  4 breeding      87\n##  5 feeding       75\n##  6 population    65\n##  7 bird          60\n##  8 genetic       58\n##  9 study         56\n## 10 effects       54\n```"                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           
#> [38] "```r\nwith(data_words, wordcloud(word, n, max.words = 100))\n```"                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          
#> [39] "`fulltext`"                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                
#> [40] "`dataone`"                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 
#> [41] "```r\nspecies %>%\n    tolower() %>%\n    fulltext::ft_search() %>%\n    fulltext::ft_get() %>%\n    fulltext::ft_collect() %>%\n    fulltext::ft_chunks(c(\"title\", \"abstract\")) %>%\n    fulltext::ft_tabularize() %>%\n    dplyr::bind_rows()\n```"                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  
#> [42] "`fulltext`"                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                
#> [43] "`europepmc`"                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               
#> [44] "`jstor`"                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   
#> [45] "`suppdata`"                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                
#> [46] "`dataone`"                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 
#> [47] "`rfigshare`"                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               
#> [48] "`EML`"                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     
#> [49] "`dataspice`"                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               
#> [50] "`opencage`"                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                
#> [51] "`bbox`"                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    
#> [52] "`osmdata`"                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 
#> [53] "`osmplotr`"                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                
#> [54] "`rebird`"                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  
#> [55] "`auk`"                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     
#> [56] "`magick`"                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  
#> [57] "`tesseract`"                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               
#> [58] "`cld2`"                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    
#> [59] "`cld3`"                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    
#> [60] "`taxize::gnr_resolve`"                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     
#> [61] "`taxize`"                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  
#> [62] "`traits`"                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  

## ------------------------------------------------
## Method `yarn$add_md()`
## ------------------------------------------------

path <- system.file("extdata", "example2.Rmd", package = "tinkr")
ex <- tinkr::yarn$new(path)
# two headings, no lists
xml2::xml_find_all(ex$body, "md:heading", ex$ns)
#> {xml_nodeset (2)}
#> [1] <heading level="2">\n  <text xml:space="preserve">R Markdown</text>\n</he ...
#> [2] <heading level="2">\n  <text xml:space="preserve">Including Plots</text>\ ...
xml2::xml_find_all(ex$body, "md:list", ex$ns)
#> {xml_nodeset (0)}
ex$add_md(
  "# Hello\n\nThis is *new* formatted text from `{tinkr}`!",
  where = 1L
)$add_md(
  " - This\n - is\n - a new list",
  where = 2L
)
# three headings
xml2::xml_find_all(ex$body, "md:heading", ex$ns)
#> {xml_nodeset (3)}
#> [1] <heading level="1">\n  <text xml:space="preserve">Hello</text>\n</heading>
#> [2] <heading level="2">\n  <text xml:space="preserve">R Markdown</text>\n</he ...
#> [3] <heading level="2">\n  <text xml:space="preserve">Including Plots</text>\ ...
xml2::xml_find_all(ex$body, "md:list", ex$ns)
#> {xml_nodeset (1)}
#> [1] <list type="bullet" tight="true">\n  <item>\n    <paragraph>\n      <text ...
tmp <- tempfile()
ex$write(tmp)
readLines(tmp, n = 20)
#>  [1] "---"                                         
#>  [2] "title: \"Untitled\""                         
#>  [3] "author: \"M. Salmon\""                       
#>  [4] "date: \"September 6, 2018\""                 
#>  [5] "output: html_document"                       
#>  [6] "---"                                         
#>  [7] ""                                            
#>  [8] "```{r setup, include=FALSE, eval=TRUE}"      
#>  [9] "knitr::opts_chunk$set(echo = TRUE)"          
#> [10] "```"                                         
#> [11] ""                                            
#> [12] "# Hello"                                     
#> [13] ""                                            
#> [14] "- This"                                      
#> [15] "- is"                                        
#> [16] "- a new list"                                
#> [17] ""                                            
#> [18] "This is *new* formatted text from `{tinkr}`!"
#> [19] ""                                            
#> [20] "## R Markdown"                               

## ------------------------------------------------
## Method `yarn$append_md()`
## ------------------------------------------------

path <- system.file("extdata", "example2.Rmd", package = "tinkr")
ex <- tinkr::yarn$new(path)
# append a note after the first heading

txt <- c("> Hello from *tinkr*!", ">", ">  :heart: R")
ex$append_md(txt, ".//md:heading[1]")$head(20)
#> ---
#> title: "Untitled"
#> author: "M. Salmon"
#> date: "September 6, 2018"
#> output: html_document
#> ---
#> 
#> ```{r setup, include=FALSE, eval=TRUE}
#> knitr::opts_chunk$set(echo = TRUE)
#> ```
#> 
#> ## R Markdown
#> 
#> > Hello from *tinkr*!
#> > 
#> > :heart: R
#> 
#> This is an ~~R Markdown document~~. Markdown is a simple formatting syntax for authoring HTML, PDF, and MS Word documents. For more details on using R Markdown see <http://rmarkdown.rstudio.com>.
#> 
#> When you click the **Knit** button a document will be generated that includes both content as well as the output of any embedded R code chunks within the document. You can embed an R code chunk like this:

## ------------------------------------------------
## Method `yarn$prepend_md()`
## ------------------------------------------------

path <- system.file("extdata", "example2.Rmd", package = "tinkr")
ex <- tinkr::yarn$new(path)

# prepend a table description to the birds table
ex$prepend_md("Table: BIRDS, NERDS", ".//md:table[1]")$tail(20)
#> 
#> Note that the `echo = FALSE` parameter was added to the code chunk to prevent printing of the R code that generated the plot.
#> 
#> Table: BIRDS, NERDS
#> 
#> | scientific\_name            | common\_name         | n   | 
#> | :------------------------- | :------------------ | --: |
#> | Corvus corone              | Carrion Crow        | 288 | 
#> | Turdus merula              | Eurasian Blackbird  | 285 | 
#> | Anas platyrhynchos         | Mallard             | 273 | 
#> | Fulica atra                | Eurasian Coot       | 268 | 
#> | Parus major                | Great Tit           | 266 | 
#> | Podiceps cristatus         | Great Crested Grebe | 254 | 
#> | Ardea cinerea              | Gray Heron          | 236 | 
#> | Cygnus olor                | Mute Swan           | 234 | 
#> | Cyanistes caeruleus        | Eurasian Blue Tit   | 233 | 
#> | Chroicocephalus ridibundus | Black-headed Gull   | 223 | 
#> 
#> blabla
#> 

## ------------------------------------------------
## Method `yarn$protect_math()`
## ------------------------------------------------

path <- system.file("extdata", "math-example.md", package = "tinkr")
ex <- tinkr::yarn$new(path)
ex$tail() # math blocks are escaped :(
#> 
#> $$
#> Q\_{N(norm)}=\\frac{C\_N +C\_{N-1}}2\\times
#> \\frac{\\sum *{i=N-n}^{N}Q\_i} {\\sum*{j=N-n}^{N}{(\\frac{C\_j+C\_{j-1}}2)}}
#> $$
#> 
ex$protect_math()$tail() # math blocks are no longer escaped :)
#> 
#> $$
#> Q_{N(norm)}=\frac{C_N +C_{N-1}}2\times
#> \frac{\sum _{i=N-n}^{N}Q_i} {\sum_{j=N-n}^{N}{(\frac{C_j+C_{j-1}}2)}}
#> $$
#> 

## ------------------------------------------------
## Method `yarn$protect_curly()`
## ------------------------------------------------

path <- system.file("extdata", "basic-curly.md", package = "tinkr")
ex <- tinkr::yarn$new(path)
ex$protect_curly()$head()
#> ---
#> title: basic curly
#> ---
#> 
#> # preface {#pre-face .unnumbered}
#> 

## ------------------------------------------------
## Method `yarn$protect_fences()`
## ------------------------------------------------

path <- system.file("extdata", "fenced-divs.md", package = "tinkr")
ex <- tinkr::yarn$new(path)
ex$protect_fences()$head()
#> ---
#> title: Pandoc's fenced divs
#> ---
#> 
#> ::::: {#special .sidebar}
#> Here is a paragraph.

## ------------------------------------------------
## Method `yarn$protect_unescaped()`
## ------------------------------------------------

path <- system.file("extdata", "basic-curly.md", package = "tinkr")
ex <- tinkr::yarn$new(path, sourcepos = TRUE, unescaped = FALSE)
ex$tail()
#> ![a pretty puppy](https://placedog.net/200/300){#dog alt="a picture
#> of Mickey's dog"}
#> 
#> \[a span with attributes\]{.span-with-attributes
#> style='color: red;'}
#> 
ex$protect_unescaped()$tail()
#> ![a pretty puppy](https://placedog.net/200/300){#dog alt="a picture
#> of Mickey's dog"}
#> 
#> [a span with attributes]{.span-with-attributes
#> style='color: red;'}
#> 

## ------------------------------------------------
## Method `yarn$get_protected()`
## ------------------------------------------------

path <- system.file("extdata", "basic-curly.md", package = "tinkr")
ex <- tinkr::yarn$new(path, sourcepos = TRUE)
# protect curly braces
ex$protect_curly()
# add fenced divs and protect then
ex$add_md(c("::: alert\n",
  "blabla",
  ":::")
)
ex$protect_fences()
# add math and protect it
ex$add_md(c("## math\n",
  "$c^2 = a^2 + b^2$\n",
  "$$",
  "\\sum_{i}^k = x_i + 1",
  "$$\n")
)
ex$protect_math()
# get protected now shows all the protected nodes
ex$get_protected()
#> {xml_nodeset (16)}
#>  [1] <text asis="true" math="true">$c^2 = a^2 + b^2$</text>
#>  [2] <text xml:space="preserve" asis="true" math="true">$$</text>
#>  [3] <softbreak asis="true" math="true"/>
#>  [4] <text xml:space="preserve" asis="true" math="true">\\sum_{i}^k = x_i + 1 ...
#>  [5] <softbreak asis="true" math="true"/>
#>  [6] <text xml:space="preserve" asis="true" math="true">$$</text>
#>  [7] <text xml:space="preserve" fence="true">::: alert</text>
#>  [8] <text xml:space="preserve" fence="true">:::</text>
#>  [9] <text curly="true">{#pre-face .unnumbered}</text>
#> [10] <text curly="true">{xml2}</text>
#> [11] <text curly="true">{tinkr}</text>
#> [12] <text curly="true" alt="a picture of a kitten">{#kitteh alt='a picture o ...
#> [13] <text curly="true" alt="a picture of Mickey's dog">{#dog alt="a picture\ ...
#> [14] <text asis="true">[</text>
#> [15] <text asis="true">]</text>
#> [16] <text curly="true">{.span-with-attributes\nstyle='color: red;'}</text>
ex$get_protected(c("math", "curly")) # only show the math and curly
#> {xml_nodeset (12)}
#>  [1] <text asis="true" math="true">$c^2 = a^2 + b^2$</text>
#>  [2] <text xml:space="preserve" asis="true" math="true">$$</text>
#>  [3] <softbreak asis="true" math="true"/>
#>  [4] <text xml:space="preserve" asis="true" math="true">\\sum_{i}^k = x_i + 1 ...
#>  [5] <softbreak asis="true" math="true"/>
#>  [6] <text xml:space="preserve" asis="true" math="true">$$</text>
#>  [7] <text curly="true">{#pre-face .unnumbered}</text>
#>  [8] <text curly="true">{xml2}</text>
#>  [9] <text curly="true">{tinkr}</text>
#> [10] <text curly="true" alt="a picture of a kitten">{#kitteh alt='a picture o ...
#> [11] <text curly="true" alt="a picture of Mickey's dog">{#dog alt="a picture\ ...
#> [12] <text curly="true">{.span-with-attributes\nstyle='color: red;'}</text>
```
