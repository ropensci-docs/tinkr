# Insert markdown before or after a set of nodes

Insert markdown before or after a set of nodes

## Usage

``` r
insert_md(body, md, nodes, where = "after", space = TRUE)
```

## Arguments

- body:

  an XML object generated via tinkr

- md:

  markdown text to insert

- nodes:

  a character vector of an XPath expression OR an `xml_node` or
  `xml_nodeset` object.

- where:

  the position in the markdown document to insert the new markdown

- space:

  when `TRUE` (default) inline nodes have a single space appended or
  prepended to avoid the added markdown abutting text.

## Value

a copy of the XML object with the translated markdown inserted

## Note

The markdown content must be of the same type as the XML nodes, either
inline or block content.
