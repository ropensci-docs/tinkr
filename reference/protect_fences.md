# Protect fences of Pandoc fences divs for further processing

Protect fences of Pandoc fences divs for further processing

## Usage

``` r
protect_fences(body, ns = md_ns())
```

## Arguments

- body:

  an XML object

- ns:

  an XML namespace object (defaults:
  [`md_ns()`](https://docs.ropensci.org/tinkr/reference/md_ns.md)).

## Value

a copy of the modified XML object

## Details

Commonmark will render text such as `::: footer` as normal text which
might be problematic if trying to extract real text from the XML.

If sending the XML to, say, a translation API that allows some tags to
be ignored, you could first transform the text tags with the attribute
`fences` to `fences` tags, and then transform them back to text tags
before using
[`to_md()`](https://docs.ropensci.org/tinkr/reference/to_md.md).

## Note: yarn method

This function is also a method in the
[yarn](https://docs.ropensci.org/tinkr/reference/yarn.md) object.

## Examples

``` r
m <- tinkr::to_xml(system.file("extdata", "fenced-divs.md", package = "tinkr"))
xml2::xml_child(m$body)
#> {xml_node}
#> <paragraph>
#> [1] <text xml:space="preserve">::::: {#special .sidebar}</text>
#> [2] <softbreak/>
#> [3] <text xml:space="preserve">Here is a paragraph.</text>
m$body <- protect_fences(m$body)
xml2::xml_child(m$body)
#> {xml_node}
#> <paragraph>
#> [1] <text xml:space="preserve" fence="true">::::: {#special .sidebar}</text>
#> [2] <softbreak/>
#> [3] <text xml:space="preserve">Here is a paragraph.</text>
```
