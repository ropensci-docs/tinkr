# Changelog

## tinkr 0.3.1

CRAN release: 2025-10-04

### BUG FIX

- list sub-items will no longer be incorrectly indented if the parent
  item is an ordered item (reported and fixed: 1,
  [\#140](https://github.com/ropensci/tinkr/issues/140))
- we now use the air formatter for code in the project
  ([\#141](https://github.com/ropensci/tinkr/issues/141))

## tinkr 0.3.0

CRAN release: 2025-05-03

### NEW FEATURES

- `yarn$show()` method now gains the `lines` parameter, which allows you
  to subset the output by the lines of text. A warning is produced if a
  stylesheet is supplied in place of `lines`.
- `yarn$append_md()` and `yarn$prepend_md()` methods allow you to add
  new markdown to specific places in the document using XPath
  expressions.
- [`to_md_vec()`](https://docs.ropensci.org/tinkr/reference/to_md.md)
  takes an xml node or nodelist and returns a character vector of the
  markdown produced.
- [`show_list()`](https://docs.ropensci.org/tinkr/reference/show.md),
  [`show_block()`](https://docs.ropensci.org/tinkr/reference/show.md),
  and
  [`show_censor()`](https://docs.ropensci.org/tinkr/reference/show.md)
  will show the markdown content of a node, nodelist, or list of nodes
  without needing to print the entire document.
- `yarn$md_vec()` is a new method that will generate a character vector
  of markdown elements from a query. This is a convenience method that
  uses
  [`xml2::xml_find_all()`](http://xml2.r-lib.org/reference/xml_find_all.md)
  and
  [`to_md_vec()`](https://docs.ropensci.org/tinkr/reference/to_md.md) in
  the background.
- [`get_protected()`](https://docs.ropensci.org/tinkr/reference/get_protected.md)
  function (and yarn method) will return nodes which have been protected
  in some way by {tinkr} via one of the `protect_` family of functions.
  Adopting this pattern is preferred over using `md:text[@asis='true']`
  as the attribute names may change in the future
  ([@zkamvar](https://github.com/zkamvar),
  [\#111](https://github.com/ropensci/tinkr/issues/111); reviewed: 1)
- Block math will now include the delimiters and the softbreaks for
  protection (issue/review:
  [\#113](https://github.com/ropensci/tinkr/issues/113), 1; implemented:
  [\#111](https://github.com/ropensci/tinkr/issues/111),
  [@zkamvar](https://github.com/zkamvar))
- Frontmatter in TOML and JSON is now preserved too. The `yaml` field is
  replaced (softly deprecated) with a field called `frontmatter`. A new
  field called `frontmatter_format` (“YAML”, “TOML”, or “JSON”) contains
  the format of the frontmatter. (issue:
  [\#126](https://github.com/ropensci/tinkr/issues/126), 1)
- New
  [`protect_fences()`](https://docs.ropensci.org/tinkr/reference/protect_fences.md)
  function will add a `fence='true'` attribute to fences of Pandoc
  fenced divs to allow parsing of the XML for sending to external APIs.
- New `$protect_fences()` method implements
  [`protect_fences()`](https://docs.ropensci.org/tinkr/reference/protect_fences.md)
  on yarn objects

### NEW IMPORTS

- We now import {rlang} for error handling. Because we already import
  {purrr}, this does not impact the dependency footprint (suggested: 1,
  [\#111](https://github.com/ropensci/tinkr/issues/111); implemented:
  [@zkamvar](https://github.com/zkamvar),
  [\#111](https://github.com/ropensci/tinkr/issues/111)).

### BUG FIX

- Bare links in Markdown (e.g. `<https://example.com/one>`) are no
  longer transformed to markdown-style links (e.g.
  `[https://example.com/one](https://example.com/one)`). (issue:
  [\#59](https://github.com/ropensci/tinkr/issues/59); fix:
  [\#115](https://github.com/ropensci/tinkr/issues/115))
- Inline math with single characters will no longer cause an error
  (issue: [\#101](https://github.com/ropensci/tinkr/issues/101), fix:
  [\#103](https://github.com/ropensci/tinkr/issues/103), 1)
- Special control characters are now filtered out before processing XML
  (issue: [\#96](https://github.com/ropensci/tinkr/issues/96), fix:
  [\#111](https://github.com/ropensci/tinkr/issues/111),
  [@zkamvar](https://github.com/zkamvar))
- Documents with no trailing newline will no longer throw a warning
  (issue: [\#65](https://github.com/ropensci/tinkr/issues/65); fix:
  [\#114](https://github.com/ropensci/tinkr/issues/114),
  [@zkamvar](https://github.com/zkamvar))
- Documents with dollar signs but no math will no longer fail with the
  `$protect_math()` method (issue:
  [\#121](https://github.com/ropensci/tinkr/issues/121),
  [\#124](https://github.com/ropensci/tinkr/issues/124) 1; fix:
  [\#122](https://github.com/ropensci/tinkr/issues/122),
  [\#125](https://github.com/ropensci/tinkr/issues/125)
  [@zkamvar](https://github.com/zkamvar))

### MISC

- Testing code outside of `test_that()` has been removed (issue:
  [\#83](https://github.com/ropensci/tinkr/issues/83), fix:
  [\#102](https://github.com/ropensci/tinkr/issues/102),
  1.  

## tinkr 0.2.0

CRAN release: 2023-03-28

### BUG FIX

- Empty YAML will not prepend with newline in {glue} (\> 1.6.2).
  ([\#93](https://github.com/ropensci/tinkr/issues/93),
  [@jennybc](https://github.com/jennybc))
- Relative links whose text and anchor key are identical
  (e.g. footnotes) are no longer doubled in the output document (issue:
  [\#92](https://github.com/ropensci/tinkr/issues/92), fix:
  [\#94](https://github.com/ropensci/tinkr/issues/94))

### NEW FEATURES

- New
  [`protect_curly()`](https://docs.ropensci.org/tinkr/reference/protect_curly.md)
  function will add a `curly='true'` attribute to text wrapped in curly
  braces (‘{’, ‘}’) to allow parsing of the XML for sending to external
  APIs. This function will also parse alt text and place it in an
  attribute.
- New `$protect_curly()` method implements
  [`protect_curly()`](https://docs.ropensci.org/tinkr/reference/protect_curly.md)
  on yarn objects
- Relative link anchor keys will no longer have characters escaped
  ([\#85](https://github.com/ropensci/tinkr/issues/85)).
- unescaped square braces from the source code can now make the
  roundtrip without extra markup. This is implemented in a new default
  parameter for
  [`to_xml()`](https://docs.ropensci.org/tinkr/reference/to_xml.md)
  called `unescaped = TRUE`, which indicates that you would like
  unescaped square braces to remain unescaped
  ([\#78](https://github.com/ropensci/tinkr/issues/78)).

### DEPENDENCY UPDATE

- removed dependency on stringr in favor of base R functions wrapped in
  internal functions with a stringr-like interface
  ([\#88](https://github.com/ropensci/tinkr/issues/88)).

## tinkr 0.1.0

CRAN release: 2022-08-19

First version on CRAN

### BREAKING CHANGE

- The stylesheets now are in the `stylesheets/` path, not `extdata/`. If
  you have not used
  [`tinkr::stylesheet()`](https://docs.ropensci.org/tinkr/reference/stylesheet.md)
  to access the default stylesheet, you will need to modify your code.

## tinkr 0.0.0.9002

### BUG FIX

- 2022-03-23: added `tasklist` template for compatibility with
  commonmark [version
  1.8](https://github.com/r-lib/commonmark/blob/2b5cce9b85575a7c48a2c22e3e5f9114a41ef2d6/NEWS#L1)
  ([\#66](https://github.com/ropensci/tinkr/issues/66))

## tinkr 0.0.0.9001

- xml and yaml objects are now stored in an R6 class called `yarn`.
- testthat edition 3 is now being used with snapshot testing.
- Tables are now pretty after a full loop
  [`to_xml()`](https://docs.ropensci.org/tinkr/reference/to_xml.md) +
  [`to_md()`](https://docs.ropensci.org/tinkr/reference/to_md.md)
  ([@pdaengeli](https://github.com/pdaengeli),
  [\#9](https://github.com/ropensci/tinkr/issues/9))
- 2021-05-04: yarn objects remember the `sourcepos` and `encoding`
  options when using the `$reset()` method.
- 2021-05-06:
  [`protect_math()`](https://docs.ropensci.org/tinkr/reference/protect_math.md)
  function and method protects LaTeX math (dollar notation) from
  escaping by commonmark ([@zkamvar](https://github.com/zkamvar),
  [\#39](https://github.com/ropensci/tinkr/issues/39)).
- 2021-05-06: GitHub-flavored markdown ticks/checkboxes are now
  protected by default ([@zkamvar](https://github.com/zkamvar),
  [\#39](https://github.com/ropensci/tinkr/issues/39)).
- 2021-05-11:
  [`md_ns()`](https://docs.ropensci.org/tinkr/reference/md_ns.md) is a
  new convenience function to provide the `md` namespace prefix for
  commonmark xml documents ([@zkamvar](https://github.com/zkamvar),
  [\#39](https://github.com/ropensci/tinkr/issues/39)).
- 2021-05-11:
  [`stylesheet()`](https://docs.ropensci.org/tinkr/reference/stylesheet.md)
  returns the path to the internal {tinkr} stylesheet so that it can
  easily be discovered by other packages
- 2021-05-11: yarn methods `show()`,
  [`head()`](https://rdrr.io/r/utils/head.html), and
  [`tail()`](https://rdrr.io/r/utils/head.html) all gain
  `stylesheet_path` arguments so the modified stylesheets can be used.
- 2021-05-24: reference style links (i.e. `[text][link-ref]` with
  `[link-ref]: <link>` on another place in the document will be
  preserved and the anchor will sink to the bottom of the document.
- 2021-09-14: numeric options fig.width and fig.height will no longer be
  quoted; `transform_params()` is simplified and no longer requires
  glue.
- 2021-10-15: math with embedded code and punctuation following are now
  allowed ([@zkamvar](https://github.com/zkamvar)
  [\#56](https://github.com/ropensci/tinkr/issues/56))
- 2021-10-18: links and asis nodes that are at the beginning of
  paragraphs are no longer escaped
  ([@zkamvar](https://github.com/zkamvar),
  [\#58](https://github.com/ropensci/tinkr/issues/58))
- 2022-02-07: [@zkamvar](https://github.com/zkamvar) is now the
  maintainer

## tinkr 0.0.0.9000

- Added a `NEWS.md` file to track changes to the package.
