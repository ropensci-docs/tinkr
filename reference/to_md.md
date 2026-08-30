# Write front-matter (YAML, TOML or JSON) and XML back to disk as (R)Markdown

Write front-matter (YAML, TOML or JSON) and XML back to disk as
(R)Markdown

## Usage

``` r
to_md(
  frontmatter_xml_list,
  path = NULL,
  stylesheet_path = stylesheet(),
  yaml_xml_list = deprecated()
)

to_md_vec(nodelist, stylesheet_path = stylesheet())
```

## Arguments

- frontmatter_xml_list:

  result from a call to
  [`to_xml()`](https://docs.ropensci.org/tinkr/reference/to_xml.md) and
  editing.

- path:

  path of the new file. Defaults to `NULL`, which will not write any
  file, but will still produce the conversion and pass the output as a
  character vector.

- stylesheet_path:

  path to the XSL stylesheet

- yaml_xml_list:

  **\[deprecated\]** Use `frontmatter_xml_list()`.

- nodelist:

  an object of `xml_nodelist` or `xml_node`

## Value

- `to_md()`: `\[character\]` the converted document, invisibly as a
  character vector containing two elements: the frontmatter list and the
  markdown body.

- `to_md_vec()`: `\[character\]` the markdown representation of each
  node.

## Details

The stylesheet you use will decide whether lists are built using "\*" or
"-" for instance. If you're keen to keep your own Markdown style when
using `to_md()` after
[`to_xml()`](https://docs.ropensci.org/tinkr/reference/to_xml.md), you
can tweak the XSL stylesheet a bit and provide the path to your XSL
stylesheet as argument.

## Examples

``` r
path <- system.file("extdata", "example1.md", package = "tinkr")
frontmatter_xml_list <- to_xml(path)
names(frontmatter_xml_list)
#> [1] "frontmatter"        "body"               "frontmatter_format"
# extract the level 3 headers from the body
headers3 <- xml2::xml_find_all(
  frontmatter_xml_list$body,
  xpath = './/md:heading[@level="3"]',
  ns = md_ns()
)
# show the headers
print(h3 <- to_md_vec(headers3))
#> [1] "### Getting a list of 50 species from occurrence data"
#> [2] "### Querying the scientific literature"               
#> [3] "### Querying scientific open data"                    
#> [4] "### Conclusion"                                       
# transform level 3 headers into level 1 headers
# NOTE: these nodes are still associated with the document and this is done
# in place.
xml2::xml_set_attr(headers3, "level", 1)
# preview the new headers
print(h1 <- to_md_vec(headers3))
#> [1] "# Getting a list of 50 species from occurrence data"
#> [2] "# Querying the scientific literature"               
#> [3] "# Querying scientific open data"                    
#> [4] "# Conclusion"                                       
# save back and have a look
newmd <- tempfile("newmd", fileext = ".md")
res <- to_md(frontmatter_xml_list, newmd)
# show that it works
regmatches(res[[2]], gregexpr(h1[1], res[[2]], fixed = TRUE))
#> [[1]]
#> [1] "# Getting a list of 50 species from occurrence data"
#> 
# file.edit("newmd.md")
file.remove(newmd)
#> [1] TRUE
```
