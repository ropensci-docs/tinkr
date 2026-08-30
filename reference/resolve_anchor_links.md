# Resolve Reference-Style Links

[Reference style links and
images](https://www.markdownguide.org/basic-syntax/#reference-style-links)
are a form of markdown syntax that reduces duplication and makes
markdown more readable. They come in two parts:

1.  The inline part that uses two pairs of square brackets where the
    second pair of square brackets contains the reference for the anchor
    part of the link. Example:

        [inline text describing link][link-reference]

2.  The anchor part, which can be anywhere in the document, contains a
    pair of square brackets followed by a colon and space with the link
    and optionally the link title. Example:

        [link-reference]: https://docs.ropensci.org/tinkr/ 'documentation for tinkr'

Commonmark treats reference-style links as regular links, which can be a
pain when converting large documents. This function resolves these links
by reading in the source document, finding the reference-style links,
and adding them back at the end of the document with the 'anchor'
attribute and appending the reference to the link with the 'ref'
attribute.

## Usage

``` r
resolve_anchor_links(body, txt, ns = md_ns())
```

## Arguments

- body:

  an XML body

- txt:

  the text of a source file

- ns:

  an the namespace that resolves the Markdown namespace (defaults to
  [`md_ns()`](https://docs.ropensci.org/tinkr/reference/md_ns.md))

## Details

### Nomenclature

The reference-style link contains two parts, but they don't have common
names (the [markdown guide](https://www.markdownguide.org/basic-syntax/)
calls these "first part and second part"), so in this documentation, we
call the link pattern of `[link text][link-ref]` as the "inline
reference-style link" and the pattern of `[link-ref]: <URL>` as the
"anchor references-style link".

### Reference-style links in commonmark's XML representation

A link or image in XML is represented by a node with the following
attributes

- destination: the URL for the link

- title: an optional title for the link

For example, this markdown link
`[link text](https://example.com "example link")` is represented in XML
as text inside of a link node:

    lnk <- "[link text](https://example.com 'example link')"
    xml <- xml2::read_xml(commonmark::markdown_xml(lnk))
    cat(as.character(xml2::xml_find_first(xml, ".//d1:link")))
    #> <link destination="https://example.com" title="example link">
    #>   <text xml:space="preserve">link text</text>
    #> </link>

However, reference-style links are rendered equivalently:

    lnk <- "
    [link text][link-ref]

    [link-ref]: https://example.com 'example link'
    "
    xml <- xml2::read_xml(commonmark::markdown_xml(lnk))
    cat(as.character(xml2::xml_find_first(xml, ".//d1:link")))
    #> <link destination="https://example.com" title="example link">
    #>   <text xml:space="preserve">link text</text>
    #> </link>

### XML attributes of reference-style links

To preserve the anchor reference-style links, we search the source
document for the destination attribute proceded by `]: `, transform that
information into a new link node with the `anchor` attribute, and add it
to the end of the document. That node looks like this:

    <link destination="https://example.com" title="example link" anchor="true">
      <text>link-ref</text>
    </link>

From there, we add the anchor text to the node that is present in our
document as the `ref` attribute:

    <link destination="https://example.com" title="example link" rel="link-ref">
      <text xml:space="preserve">link text</text>
    </link>

## Note

this function is internally used in the function
[`to_xml()`](https://docs.ropensci.org/tinkr/reference/to_xml.md).

## Examples

``` r
f <- system.file("extdata", "link-test.md", package = "tinkr")
md <- yarn$new(f, sourcepos = TRUE, anchor_links = FALSE)
md$show()
#> ---
#> title: this tests links
#> ---
#> 
#> ## These are some links that are valid in basic markdown
#> 
#> This is some text [that contains links](https://example.com/1) which
#> [can be `inline`](https://example.com/2) or [can be spread across multiple lines
#> because the link text is JUST TOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOO
#> LONG, y'all](https://example.com/3).
#> 
#> Mainly, we want to see if [link text
#> by reference](https://example.com/4) and if links [can be referenced multiple times](https://example.com/1)
#> 
#> This should also [include non-reference links](https://example.com/5)
#> 
#> If you [write]{.confession} \[some link text\]\[link2\], that will appear as [some link text](https://example.com/2 "link with title!")
#> but you can also [test](https://example.com/racehorse/) [sub](https://example.com/racehorse/1/1 "One One Won One") [links](https://example.com/racehorse/2/2/ "Two Two Won One Two")
#> 
#> [pizza \& icecream](https://example.com/pizza&icecream) = fun
#> 
#> ```markdown
#> you can write links like [a link](https://example.com/racehorse) or using
#> [reference style][racehorce]
#> 
#> [racehorse]: https://example.com/racehorse/   
#> ```
#> 
#> ## This is some extended markdown content {#extended .callout}
#> 
#> This should also include references that use [standalone](https://example.com/standalone) links and
#> footnotes should not be properly parsed and will be considered 'asis' nodes,
#> at least that's what I *believe*[^footy]. Maybe this might not pan out [^but who
#> knows? footnotes are **WEIRD**, man].
#> 
#> <!-- links go here! -->
#> 
#> [^footy]: this is a footnote that
#> should be preserved
#> 
if (requireNamespace("withr")) {
lnks <- withr::with_namespace("tinkr",
  resolve_anchor_links(md$body, readLines(md$path)))
md$body <- lnks
md$show()
}
#> ---
#> title: this tests links
#> ---
#> 
#> ## These are some links that are valid in basic markdown
#> 
#> This is some text [that contains links][this fun link1] which
#> [can be `inline`](https://example.com/2) or [can be spread across multiple lines
#> because the link text is JUST TOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOO
#> LONG, y'all][link3].
#> 
#> Mainly, we want to see if [link text
#> by reference][link4] and if links [can be referenced multiple times][this fun link1]
#> 
#> This should also [include non-reference links](https://example.com/5)
#> 
#> If you [write]{.confession} \[some link text\]\[link2\], that will appear as [some link text][link2]
#> but you can also [test][racehorse] [sub][sub-link1] [links][sub_link2]
#> 
#> [pizza \& icecream][pizzaicecream] = fun
#> 
#> ```markdown
#> you can write links like [a link](https://example.com/racehorse) or using
#> [reference style][racehorce]
#> 
#> [racehorse]: https://example.com/racehorse/   
#> ```
#> 
#> ## This is some extended markdown content {#extended .callout}
#> 
#> This should also include references that use [standalone] links and
#> footnotes should not be properly parsed and will be considered 'asis' nodes,
#> at least that's what I *believe*[^footy]. Maybe this might not pan out [^but who
#> knows? footnotes are **WEIRD**, man].
#> 
#> <!-- links go here! -->
#> 
#> [^footy]: this is a footnote that
#> should be preserved
#> 
#> [this fun link1]: https://example.com/1
#> [link3]: https://example.com/3
#> [link4]: https://example.com/4
#> [link2]: https://example.com/2 "link with title!"
#> [racehorse]: https://example.com/racehorse/
#> [sub-link1]: https://example.com/racehorse/1/1 "One One Won One"
#> [sub_link2]: https://example.com/racehorse/2/2/ "Two Two Won One Two"
#> [pizzaicecream]: https://example.com/pizza&icecream
#> [standalone]: https://example.com/standalone
#> 
#> 
```
