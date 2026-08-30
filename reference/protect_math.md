# Protect math elements from commonmark's character escape

Protect math elements from commonmark's character escape

## Usage

``` r
protect_math(body, ns = md_ns())
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

Commonmark does not know what LaTeX is and will LaTeX equations as
normal text. This means that content surrounded by underscores are
interpreted as `<emph>` elements and all backslashes are escaped by
default. This function protects inline and block math elements that use
`$` and `$$` for delimiters, respectively.

## Note: yarn method

This function is also a method in the
[yarn](https://docs.ropensci.org/tinkr/reference/yarn.md) object.

## Examples

``` r
m <- tinkr::to_xml(system.file("extdata", "math-example.md", package = "tinkr"))
txt <- textConnection(tinkr::to_md(m))
cat(tail(readLines(txt)), sep = "\n") # broken math
#> 
#> $$
#> Q\_{N(norm)}=\\frac{C\_N +C\_{N-1}}2\\times
#> \\frac{\\sum *{i=N-n}^{N}Q\_i} {\\sum*{j=N-n}^{N}{(\\frac{C\_j+C\_{j-1}}2)}}
#> $$
#> 
close(txt)
m$body <- protect_math(m$body)
txt <- textConnection(tinkr::to_md(m))
cat(tail(readLines(txt)), sep = "\n") # fixed math
#> 
#> $$
#> Q_{N(norm)}=\frac{C_N +C_{N-1}}2\times
#> \frac{\sum _{i=N-n}^{N}Q_i} {\sum_{j=N-n}^{N}{(\frac{C_j+C_{j-1}}2)}}
#> $$
#> 
close(txt)
```
