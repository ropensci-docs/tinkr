# Display a node or nodelist as markdown

When inspecting the results of an XPath query, displaying the text often

## Usage

``` r
show_list(nodelist, stylesheet_path = stylesheet())

show_block(nodelist, mark = FALSE, stylesheet_path = stylesheet())

show_censor(nodelist, stylesheet_path = stylesheet())
```

## Arguments

- nodelist:

  an object of class `xml_nodeset` OR `xml_node` OR a list of either.

- stylesheet_path:

  path to the XSL stylesheet

- mark:

  \[bool\] When `TRUE` markers (`[...]`) are added to replace nodes that
  come before or after the islated nodes. Defaults to `FALSE`, which
  only shows the isolated nodes in their respective blocks. Note that
  the default state may cause nodes within the same block to appear
  adjacent to each other.

## Value

a character vector, invisibly. The result of these functions are
displayed to the screen

## See also

[`to_md_vec()`](https://docs.ropensci.org/tinkr/reference/to_md.md) to
get a vector of these elements in isolation.

## Examples

``` r
path <- system.file("extdata", "show-example.md", package = "tinkr")
y <- tinkr::yarn$new(path, sourcepos = TRUE)
y$protect_math()$protect_curly()
items <- xml2::xml_find_all(y$body, ".//md:item", tinkr::md_ns())
imgs <- xml2::xml_find_all(y$body, ".//md:image | .//node()[@curly]",
  tinkr::md_ns())
links <- xml2::xml_find_all(y$body, ".//md:link", tinkr::md_ns())
code <- xml2::xml_find_all(y$body, ".//md:code", tinkr::md_ns())
blocks <- xml2::xml_find_all(y$body, ".//md:code_block", tinkr::md_ns())

# show a list of items
show_list(links)
#> 
#> 
#> [relative links](#links)
#> 
#> [anchor links]
#> 
#> [anchor links]: https://example.com/anchor
#> 
#> 
show_list(code)
#> 
#> 
#> `utils::strcapture()`
#> 
show_list(blocks)
#> 
#> 
#> ```r
#> sourcepos <- c("2:1-2:33", "4:1-7:7")
#> pattern <- "([[:digit:]]+):([[:digit:]]+)-([[:digit:]]+):([[:digit:]]+)"
#> proto <- data.frame(
#>   linestart = integer(), colstart = integer(),
#>   lineend = integer(), colend = integer()
#> )
#> utils::strcapture(pattern, sourcepos, proto)
#> ```
#> 
#> 

# show the items in their local structure
show_block(items)
#> 
#> 
#> - kittens
#>   - are
#>     - super
#>     - cute
#>   - have
#>     - teef
#>     - murder mittens
#> - brains
#>   - are
#>     - wrinkly
#> 
show_block(links, mark = TRUE)
#> 
#> 
#> [...] [relative links](#links) [...][...] [anchor links] [...]
#> 
#> [anchor links]: https://example.com/anchor
#> 
#> 

# show the items in the full document censored (everything but whitespace):
show_censor(imgs)
#> 
#> 
#> ## ▇▇▇▇▇
#> 
#> ### ▇▇▇▇▇▇▇▇
#> 
#> ▇▇▇▇ ▇▇▇ ▇▇▇▇ [▇▇▇▇▇▇▇▇ ▇▇▇▇▇](▇▇▇▇▇▇) ▇▇▇ [▇▇▇▇▇▇ ▇▇▇▇▇]▇
#> 
#> ### ▇▇▇▇▇▇
#> 
#> ![kittens are cute](https://loremflickr.com/320/240){alt='a random picture of a kitten'}
#> 
#> ## ▇▇▇▇▇
#> 
#> - ▇▇▇▇▇▇▇
#>   - ▇▇▇
#>     - ▇▇▇▇▇
#>     - ▇▇▇▇
#>   - ▇▇▇▇
#>     - ▇▇▇▇
#>     - ▇▇▇▇▇▇ ▇▇▇▇▇▇▇
#> - ▇▇▇▇▇▇
#>   - ▇▇▇
#>     - ▇▇▇▇▇▇▇
#> 
#> ## ▇▇▇▇
#> 
#> ▇▇▇▇ ▇▇ ▇▇ ▇▇▇▇▇▇▇ ▇▇ ▇▇▇ `▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇` ▇▇▇▇▇▇▇▇
#> 
#> ```r
#> ▇▇▇▇▇▇▇▇▇ ▇▇ ▇▇▇▇▇▇▇▇▇▇▇▇▇ ▇▇▇▇▇▇▇▇▇▇
#> ▇▇▇▇▇▇▇ ▇▇ ▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇
#> ▇▇▇▇▇ ▇▇ ▇▇▇▇▇▇▇▇▇▇▇
#>   ▇▇▇▇▇▇▇▇▇ ▇ ▇▇▇▇▇▇▇▇▇▇ ▇▇▇▇▇▇▇▇ ▇ ▇▇▇▇▇▇▇▇▇▇
#>   ▇▇▇▇▇▇▇ ▇ ▇▇▇▇▇▇▇▇▇▇ ▇▇▇▇▇▇ ▇ ▇▇▇▇▇▇▇▇▇
#> ▇
#> ▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇ ▇▇▇▇▇▇▇▇▇▇ ▇▇▇▇▇▇
#> ```
#> 
#> ## ▇▇▇▇
#> 
#> ▇▇▇▇▇▇ ▇▇▇▇ ▇▇▇ ▇▇ ▇▇▇▇▇▇▇ ▇▇ ▇▇ ▇ ▇▇ ▇ ▇▇ ▇▇▇▇▇ ▇▇▇▇▇ ▇▇▇▇ ▇▇▇▇▇▇ ▇▇▇
#> 
#> ▇▇
#> ▇ ▇ ▇▇ ▇ ▇
#> ▇▇
#> 
#> [▇▇▇▇▇▇ ▇▇▇▇▇]: ▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇▇
#> 
#> 

# You can also adjust the censorship parameters. There are two paramters
# available: the mark, which chooses what character you want to use to
# replace characters (default: `\u2587`); and the regex which specifies
# characters to replace (default: `[^[:space:]]`, which replaces all
# non-whitespace characters.
#
# The following will replace everything that is not a whitespace
# or punctuation character with "o" for a very ghostly document
op <- options()
options(tinkr.censor.regex = "[^[:space:][:punct:]]")
options(tinkr.censor.mark = "o")
show_censor(links)
#> 
#> 
#> ## ooooo
#> 
#> ### oooooooo
#> 
#> oooo ooo oooo [relative links](#links) ooo [anchor links].
#> 
#> ### oooooo
#> 
#> ![ooooooo ooo oooo](ooooo://ooooooooooo.ooo/ooo/ooo){ooo='o oooooo ooooooo oo o oooooo'}
#> 
#> ## ooooo
#> 
#> - ooooooo
#>   - ooo
#>     - ooooo
#>     - oooo
#>   - oooo
#>     - oooo
#>     - oooooo ooooooo
#> - oooooo
#>   - ooo
#>     - ooooooo
#> 
#> ## oooo
#> 
#> oooo oo oo ooooooo oo ooo `ooooo::oooooooooo()` oooooooo
#> 
#> ```r
#> ooooooooo <- o("o:o-o:oo", "o:o-o:o")
#> ooooooo <- "([[:ooooo:]]+):([[:ooooo:]]+)-([[:ooooo:]]+):([[:ooooo:]]+)"
#> ooooo <- oooo.ooooo(
#>   ooooooooo = ooooooo(), oooooooo = ooooooo(),
#>   ooooooo = ooooooo(), oooooo = ooooooo()
#> )
#> ooooo::oooooooooo(ooooooo, ooooooooo, ooooo)
#> ```
#> 
#> ## oooo
#> 
#> oooooo oooo ooo oo ooooooo oo $o = oo + o$ ooooo ooooo oooo oooooo oo:
#> 
#> $$
#> o = oo + o
#> $$
#> 
#> [anchor links]: https://example.com/anchor
#> 
#> 
options(tinkr.censor.regex = NULL)
options(tinkr.censor.mark = NULL)
```
