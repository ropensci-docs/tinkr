# Add children to a specific location in the full document.

Add children to a specific location in the full document.

## Usage

``` r
add_nodes_to_body(body, nodes, where = 0L)
```

## Arguments

- body:

  an XML object generated via tinkr

- nodes:

  an object of `xml_node` or list of nodes

- where:

  the position in the markdown document to insert the new markdown

## Value

a copy of the XML object with nodes inserted
