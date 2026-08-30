# Transform file to XML

Transform file to XML

## Usage

``` r
to_xml(
  path,
  encoding = "UTF-8",
  sourcepos = FALSE,
  anchor_links = TRUE,
  unescaped = TRUE
)
```

## Arguments

- path:

  Path to the file.

- encoding:

  Encoding to be used by readLines.

- sourcepos:

  passed to
  [`commonmark::markdown_xml()`](https://docs.ropensci.org/commonmark/reference/commonmark.html).
  If `TRUE`, the source position of the file will be included as a
  "sourcepos" attribute. Defaults to `FALSE`.

- anchor_links:

  if `TRUE` (default), reference-style links with anchors (in the style
  of `[key]: https://example.com/link "title"`) will be preserved as
  best as possible. If this is `FALSE`, the anchors disappear and the
  links will appear as normal links. See
  [`resolve_anchor_links()`](https://docs.ropensci.org/tinkr/reference/resolve_anchor_links.md)
  for details.

- unescaped:

  if `TRUE` (default) AND `sourcepos = TRUE`, square braces that were
  unescaped in the original document will be preserved as best as
  possible. If this is `FALSE`, these braces will be escaped in the
  output document. See
  [`protect_unescaped()`](https://docs.ropensci.org/tinkr/reference/protect_unescaped.md)
  for details.

## Value

A list containing the front-matter (YAML, TOML or JSON) of the file
(frontmatter) and its body (body) as XML.

## Details

This function will take a (R)markdown file, split the frontmatter from
the body, and read in the body through
[`commonmark::markdown_xml()`](https://docs.ropensci.org/commonmark/reference/commonmark.html).
Any RMarkdown code fences will be parsed to expose the chunk options in
XML and tickboxes (aka checkboxes) in GitHub-flavored markdown will be
preserved (both modifications from the commonmark standard).

## Note

Math elements are not protected by default. You can use
[`protect_math()`](https://docs.ropensci.org/tinkr/reference/protect_math.md)
to address this if needed.

## Examples

``` r
path <- system.file("extdata", "example1.md", package = "tinkr")
post_list <- to_xml(path)
names(post_list)
#> [1] "frontmatter"        "body"               "frontmatter_format"
path2 <- system.file("extdata", "example2.Rmd", package = "tinkr")
post_list2 <- to_xml(path2)
post_list2
#> $frontmatter
#> [1] "---"                         "title: \"Untitled\""        
#> [3] "author: \"M. Salmon\""       "date: \"September 6, 2018\""
#> [5] "output: html_document"       "---"                        
#> 
#> $body
#> {xml_document}
#> <document xmlns="http://commonmark.org/xml/1.0">
#>  [1] <code_block xml:space="preserve" language="r" name="setup" include="FALS ...
#>  [2] <heading level="2">\n  <text xml:space="preserve">R Markdown</text>\n</h ...
#>  [3] <paragraph>\n  <text xml:space="preserve">This is an </text>\n  <striket ...
#>  [4] <paragraph>\n  <text xml:space="preserve">When you click the </text>\n   ...
#>  [5] <code_block xml:space="preserve" language="r" name="" eval="TRUE" echo=" ...
#>  [6] <heading level="2">\n  <text xml:space="preserve">Including Plots</text> ...
#>  [7] <paragraph>\n  <text xml:space="preserve">You can also embed plots, for  ...
#>  [8] <code_block xml:space="preserve" language="python" name="" fig.cap="&quo ...
#>  [9] <code_block xml:space="preserve" language="python" name="">plot(pressure ...
#> [10] <paragraph>\n  <text xml:space="preserve">Non-RMarkdown blocks are also  ...
#> [11] <code_block info="bash" xml:space="preserve" name="">echo "this is an un ...
#> [12] <code_block xml:space="preserve" name="">This is an ambiguous code block ...
#> [13] <paragraph>\n  <text xml:space="preserve">Note that the </text>\n  <code ...
#> [14] <table>\n  <table_header>\n    <table_cell align="left">\n      <text xm ...
#> [15] <paragraph>\n  <text xml:space="preserve">blabla</text>\n</paragraph>
#> 
#> $frontmatter_format
#> [1] "YAML"
#> 
```
