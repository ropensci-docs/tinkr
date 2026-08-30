# Create a document and list of nodes to isolate

This uses
[`xml2::xml_root()`](http://xml2.r-lib.org/reference/xml_children.md)
and [`xml2::xml_path()`](http://xml2.r-lib.org/reference/xml_path.md) to
make a copy of the root document and then tag the corresponding nodes in
the nodelist so that we can filter on nodes that are not connected to
those present in the nodelist. This function is required for
[`isolate_nodes()`](https://docs.ropensci.org/tinkr/reference/isolate_nodes.md)
to work.

## Usage

``` r
provision_isolation(nodelist)
```

## Arguments

- nodelist:

  an object of class `xml_nodeset` OR `xml_node` OR a list of either.

## Value

a list of three elements:

- doc: a copy of the document with the nodes isolated depending on the
  context

- key: a string used to tag nodes that are isolated via the `tnk-key`
  attribute.

- unrelated: an `xml_nodeset` containing nodes that have no ancestor,
  descendant, or self relationship to the nodes in `nodelist`.

## See also

Other nodeset isolation functions:
[`isolate_nodes()`](https://docs.ropensci.org/tinkr/reference/isolate_nodes.md)

## Examples

``` r
if (FALSE) { # isTRUE(as.logical(Sys.getenv("NOT_CRAN", "false")))
path <- system.file("extdata", "show-example.md", package = "tinkr")
y <- tinkr::yarn$new(path, sourcepos = TRUE)
y$protect_math()$protect_curly()
items <- xml2::xml_find_all(y$body, ".//md:item", tinkr::md_ns())
tnk <- asNamespace("tinkr")
tnk$provision_isolation(items)
}
```
