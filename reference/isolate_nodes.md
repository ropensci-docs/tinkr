# Isolate nodes in a document

Isolate nodes in a document

## Usage

``` r
isolate_nodes(nodelist, type = "context")
```

## Arguments

- nodelist:

  an object of class `xml_nodeset` OR `xml_node` OR a list of either.

- type:

  a string of either "context" (default), "censor", or "list"

## Value

a list of two elements:

- doc: a copy of the document with the nodes isolated depending on the
  context

- key: a string used to tag nodes that are isolated via the `tnk-key`
  attribute

## Details

`isolate_nodes()`is the workhorse for the `show` family of functions.
These functions will create a copy of the document with the nodes
present in `nodelist` isolated. It has the following switches for
"type":

- "context" include the nodes within the block context of the document.
  For example, if the nodelist contains links in headings, paragraphs,
  and lists, those links will appear within these blocks. When
  `mark = TRUE`, ellipses `[...]` will be added to indicate hidden
  content.

- "censor" by default will replace all non-whitespace characters with a
  censor character. This is controlled by `tinkr.censor.regex` and
  `tinkr.censor.mark`

- "list" creates a new document and copies over the nodes so they appear
  as a list of paragraphs.

## See also

Other nodeset isolation functions:
[`provision_isolation()`](https://docs.ropensci.org/tinkr/reference/provision_isolation.md)

## Examples

``` r
if (FALSE) { # isTRUE(as.logical(Sys.getenv("NOT_CRAN", "false")))
path <- system.file("extdata", "show-example.md", package = "tinkr")
y <- tinkr::yarn$new(path, sourcepos = TRUE)
y$protect_math()$protect_curly()
items <- xml2::xml_find_all(y$body, ".//md:item", tinkr::md_ns())
tnk <- asNamespace("tinkr")
tnk$isolate_nodes(items, type = "context")
tnk$isolate_nodes(items, type = "censor")
tnk$isolate_nodes(items, type = "list")
}
```
