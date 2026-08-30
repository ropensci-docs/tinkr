# Get protected nodes

Get protected nodes

## Usage

``` r
get_protected(body, type = NULL, ns = md_ns())
```

## Arguments

- body:

  an `xml_document` object

- type:

  a character vector listing the protections to be included. Defaults to
  `NULL`, which includes all protected nodes:

  - math: via the
    [`protect_math()`](https://docs.ropensci.org/tinkr/reference/protect_math.md)
    function

  - curly: via the
    [`protect_curly()`](https://docs.ropensci.org/tinkr/reference/protect_curly.md)
    function

  - unescaped: via the
    [`protect_unescaped()`](https://docs.ropensci.org/tinkr/reference/protect_unescaped.md)
    function

- ns:

  the namespace of the document (defaults to
  [`md_ns()`](https://docs.ropensci.org/tinkr/reference/md_ns.md))

## Value

an `xml_nodelist` object.

## Examples

``` r
path <- system.file("extdata", "basic-curly.md", package = "tinkr")
ex <- tinkr::yarn$new(path, sourcepos = TRUE)
# protect curly braces
ex$protect_curly()
# add fenced divs and protect then
ex$add_md(c("::: alert\n",
  "blabla",
  ":::")
)
ex$protect_fences()
# add math and protect it
ex$add_md(c("## math\n",
  "$c^2 = a^2 + b^2$\n",
  "$$",
  "\\sum_{i}^k = x_i + 1",
  "$$\n")
)
ex$protect_math()
# get protected now shows all the protected nodes
get_protected(ex$body)
#> {xml_nodeset (16)}
#>  [1] <text asis="true" math="true">$c^2 = a^2 + b^2$</text>
#>  [2] <text xml:space="preserve" asis="true" math="true">$$</text>
#>  [3] <softbreak asis="true" math="true"/>
#>  [4] <text xml:space="preserve" asis="true" math="true">\\sum_{i}^k = x_i + 1 ...
#>  [5] <softbreak asis="true" math="true"/>
#>  [6] <text xml:space="preserve" asis="true" math="true">$$</text>
#>  [7] <text xml:space="preserve" fence="true">::: alert</text>
#>  [8] <text xml:space="preserve" fence="true">:::</text>
#>  [9] <text curly="true">{#pre-face .unnumbered}</text>
#> [10] <text curly="true">{xml2}</text>
#> [11] <text curly="true">{tinkr}</text>
#> [12] <text curly="true" alt="a picture of a kitten">{#kitteh alt='a picture o ...
#> [13] <text curly="true" alt="a picture of Mickey's dog">{#dog alt="a picture\ ...
#> [14] <text asis="true">[</text>
#> [15] <text asis="true">]</text>
#> [16] <text curly="true">{.span-with-attributes\nstyle='color: red;'}</text>
get_protected(ex$body, c("math", "curly")) # only show the math and curly
#> {xml_nodeset (12)}
#>  [1] <text asis="true" math="true">$c^2 = a^2 + b^2$</text>
#>  [2] <text xml:space="preserve" asis="true" math="true">$$</text>
#>  [3] <softbreak asis="true" math="true"/>
#>  [4] <text xml:space="preserve" asis="true" math="true">\\sum_{i}^k = x_i + 1 ...
#>  [5] <softbreak asis="true" math="true"/>
#>  [6] <text xml:space="preserve" asis="true" math="true">$$</text>
#>  [7] <text curly="true">{#pre-face .unnumbered}</text>
#>  [8] <text curly="true">{xml2}</text>
#>  [9] <text curly="true">{tinkr}</text>
#> [10] <text curly="true" alt="a picture of a kitten">{#kitteh alt='a picture o ...
#> [11] <text curly="true" alt="a picture of Mickey's dog">{#dog alt="a picture\ ...
#> [12] <text curly="true">{.span-with-attributes\nstyle='color: red;'}</text>
```
