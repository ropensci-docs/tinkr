# Find and protect all inline math elements

Find and protect all inline math elements

## Usage

``` r
protect_inline_math(body, ns)
```

## Arguments

- body:

  an XML document

- ns:

  an XML namespace

## Value

a modified *copy* of the original XML document

## Examples

``` r
txt <- commonmark::markdown_xml(
  "This sentence contains $I_A$ $\\frac{\\pi}{2}$ inline $\\LaTeX$ math."
)
txt <- xml2::read_xml(txt)
cat(tinkr::to_md(list(body = txt, frontmatter = "")), sep = "\n")
#> 
#> This sentence contains $I\_A$ $\\frac{\\pi}{2}$ inline $\\LaTeX$ math.
#> 
ns  <- tinkr::md_ns()
if (requireNamespace("withr")) {
protxt <- withr::with_namespace("tinkr", protect_inline_math(txt, ns))
cat(tinkr::to_md(list(body = protxt, frontmatter = "")), sep = "\n")
}
#> 
#> This sentence contains $I_A$ $\frac{\pi}{2}$ inline $\LaTeX$ math.
#> 
```
