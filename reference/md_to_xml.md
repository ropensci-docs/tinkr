# Convert markdown to XML

Convert markdown to XML

## Usage

``` r
md_to_xml(md)
```

## Arguments

- md:

  a character vector of markdown text

## Value

an XML nodeset of the markdown text

## Examples

``` r
if (requireNamespace("withr")) {

withr::with_namespace("tinkr", {
md_to_xml(c(
  "## This is a new section of markdown",
  "",
  "Each new element",
  "Is converted to a new line of markdown text",
  "",
  "```{r code-example, echo = FALSE}",
  "cat('code blocks work well here, too')",
  "```",
  "",
  "Neat, right?"
))
})

}
#> {xml_nodeset (4)}
#> [1] <heading level="2">\n  <text xml:space="preserve">This is a new section o ...
#> [2] <paragraph>\n  <text xml:space="preserve">Each new element</text>\n  <sof ...
#> [3] <code_block xml:space="preserve" language="r" name="code-example" echo="F ...
#> [4] <paragraph>\n  <text xml:space="preserve">Neat, right?</text>\n</paragraph>
```
