# Find between a pattern

Helper function to find all nodes between a standard pattern. This is
useful if you want to find unnested pandoc tags.

## Usage

``` r
find_between(
  body,
  ns,
  pattern = "md:paragraph[md:text[starts-with(text(), ':::')]]",
  include = FALSE
)
```

## Arguments

- body:

  and XML document

- ns:

  the namespace of the document

- pattern:

  an XPath expression that defines characteristics of nodes between
  which you want to extract everything.

- include:

  if `TRUE`, the tags matching `pattern` will be included in the output,
  defaults to `FALSE`, which only gives you the nodes in between
  `pattern`.

## Value

a nodeset

## Examples

``` r
md <- "
 h1
 ====

 ::: section

 h2
 ----

 section *text* with [a link](https://ropensci.org/)

 :::
"
x <- xml2::read_xml(commonmark::markdown_xml(md))
ns <- xml2::xml_ns_rename(xml2::xml_ns(x), d1 = "md")
res <- find_between(x, ns)
res
#> {xml_nodeset (2)}
#> [1] <heading level="2">\n  <text xml:space="preserve">h2</text>\n</heading>
#> [2] <paragraph>\n  <text xml:space="preserve">section </text>\n  <emph>\n     ...
xml2::xml_text(res)
#> [1] "h2"                       "section text with a link"
xml2::xml_find_all(res, ".//descendant-or-self::md:*", ns = ns)
#> {xml_nodeset (9)}
#> [1] <heading level="2">\n  <text xml:space="preserve">h2</text>\n</heading>
#> [2] <text xml:space="preserve">h2</text>
#> [3] <paragraph>\n  <text xml:space="preserve">section </text>\n  <emph>\n     ...
#> [4] <text xml:space="preserve">section </text>
#> [5] <emph>\n  <text xml:space="preserve">text</text>\n</emph>
#> [6] <text xml:space="preserve">text</text>
#> [7] <text xml:space="preserve"> with </text>
#> [8] <link destination="https://ropensci.org/" title="">\n  <text xml:space="p ...
#> [9] <text xml:space="preserve">a link</text>
```
