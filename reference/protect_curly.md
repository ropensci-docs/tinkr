# Protect curly elements for further processing

Protect curly elements for further processing

## Usage

``` r
protect_curly(body, ns = md_ns())
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

Commonmark will render text such as `{.unnumbered}` (Pandoc/Quarto
option) or `{#hello .greeting .message style="color: red;"}` (Markdown
custom block) as normal text which might be problematic if trying to
extract real text from the XML.

If sending the XML to, say, a translation API that allows some tags to
be ignored, you could first transform the text tags with the attribute
`curly` to `curly` tags, and then transform them back to text tags
before using
[`to_md()`](https://docs.ropensci.org/tinkr/reference/to_md.md).

## Note: yarn method

This function is also a method in the
[yarn](https://docs.ropensci.org/tinkr/reference/yarn.md) object.

## Examples

``` r
m <- tinkr::to_xml(system.file("extdata", "basic-curly.md", package = "tinkr"))
xml2::xml_child(m$body)
#> {xml_node}
#> <heading level="1">
#> [1] <text xml:space="preserve">preface {#pre-face .unnumbered}</text>
m$body <- protect_curly(m$body)
xml2::xml_child(m$body)
#> {xml_node}
#> <heading level="1">
#> [1] <text xml:space="preserve">preface </text>
#> [2] <text curly="true">{#pre-face .unnumbered}</text>
#> [3] <text/>
```
