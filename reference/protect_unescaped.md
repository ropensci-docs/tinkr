# Protect unescaped square brackets from being escaped

Commonmark allows both `[unescaped]` and `\[escaped\]` square brackets,
but in the XML representation, it makes no note of which square brackets
were originally escaped and thus will escape both in the output. This
function protects brackets that were unescaped in the source document
from being escaped.

## Usage

``` r
protect_unescaped(body, txt, ns = md_ns())
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

This is an **internal function** that is run by default via
[`to_xml()`](https://docs.ropensci.org/tinkr/reference/to_xml.md) and
`yarn$new()`. It uses the original document, parsed as text, to find and
protect unescaped square brackets from being escaped in the output.

### Example: child documents and footnotes

For example, let's say you have two R Markdown documents, one references
the other as a child, which has a [reference-style
link](https://spec.commonmark.org/0.30/#reference-link):

index.Rmd:

    ## Title

    Without protection reference style links (e.g. \[text\]\[link\]) like this
    [outside link][reflink] would be accidentally escaped.
    This is a footnote [^1].

    [^1]: footnotes are not recognised by commonmark

    ```{r, child="child.Rmd"}
    ```

child.Rmd:

    ...
    [reflink]: https://example.com

Without protection, the roundtripped index.Rmd document would look like
this:

    ## Title

    Without protection reference style links (e.g. \[text\]\[link\]) like this
    \[outside link\]\[reflink\] would be accidentally escaped.
    This is a footnote \[^1\]

    \[^1\]: footnotes are not recognised by commonmark

    ```{r, child="child.Rmd"}
    ```

This function provides the protection that allows these unescaped
brackets to remain unescaped during roundtrip.

## Note

Because the This `body` to be an XML document with `sourcepos`
attributes on the nodes, which is achieved by using `sourcepos = TRUE`
with [`to_xml()`](https://docs.ropensci.org/tinkr/reference/to_xml.md)
or [yarn](https://docs.ropensci.org/tinkr/reference/yarn.md).

## Examples

``` r
f <- system.file("extdata", "link-test.md", package = "tinkr")
md <- yarn$new(f, sourcepos = TRUE, unescaped = FALSE)
md$show()
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
#> If you \[write\]{.confession} \[some link text\]\[link2\], that will appear as [some link text][link2]
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
#> at least that's what I *believe*\[^footy\]. Maybe this might not pan out \[^but who
#> knows? footnotes are **WEIRD**, man\].
#> 
#> <!-- links go here! -->
#> 
#> \[^footy\]: this is a footnote that
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
if (requireNamespace("withr")) {
lines <- readLines(f)[-length(md$frontmatter)]
lnks <- withr::with_namespace("tinkr",
  protect_unescaped(body = md$body, txt = lines))
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
#> If you [write]{.confession} [some link text][link2], that will appear as [some link text][link2]
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
