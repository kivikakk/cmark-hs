cmark-hs
========

This package provides Haskell bindings for [libcmark], the reference
parser for [CommonMark].  It includes sources for [libcmark], and
does not require prior installation of the C library.

cmark provides the following advantages over existing Markdown
libraries for Haskell:

  - **Speed:**  Conversion speed is on par with the [sundown] library:
    about 30 times faster than [pandoc], 24 times
    faster than the Haskell [markdown] package, 8 times faster than
    [cheapskate].

  - **Memory footprint:**  Memory footprint is on par with [sundown].
    On one sample, the library uses a fourth the memory that [markdown]
    uses, and less than a tenth the memory that [pandoc] uses.

  - **Robustness:**  cmark can handle whatever is thrown at it,
    without the exponential blowups in parsing time one can sometimes
    get with other libraries.  For example, the input produced by
    this command will tie [markdown] and [pandoc] in knots:

        python -c "print ((500 * '[') + 'hi' + (500 * ']') + '(url)')"

    cmark handles it easily, with no slowdown.

  - **Accuracy:**  cmark passes the CommonMark spec's suite of over
    500 conformance tests.

  - **Standardization:**  Since there is a spec and a comprehensive suite
    of tests, we can have a high degree of confidence that any two
    CommonMark implementations will behave the same.  Thus, for
    example, one could use this library for server-side rendering
    and [commonmark.js] for client-side previewing.

  - **Ease of installation:** cmark has minimal dependencies.

cmark does not provide Haskell versions of the whole [libcmark]
API, which is built around mutable `cmark_node` objects.  Instead, it
provides two functions:

  - `markdownToHtml` uses [libcmark]'s parser and renderer for a
    maximally efficient conversion of CommonMark to HTML (as a Text).
    ("Smart punctuation" and a few other options can be enabled.)

    ``` haskell
    Prelude CMark Data.Text> markdownToHtml [optSmart] (pack "dog's *breakfast*")
    "<p>dog\8217s <em>breakfast</em></p>\n"
    ```

  - `parseDocument` uses [libcmark]'s parser to produce a `Node` tree
    that can be processed further using Haskell.  One can transform
    the tree using generics, convert it to another kind of
    structure (such as a Pandoc object that can be rendered using
    pandoc's renderers) or render it using a custom rendering
    function.

    ``` haskell
    Prelude CMark Data.Text> parseDocument  [optSmart] (pack "dog's *breakfast*")
    Node (Just (PosInfo {startLine = 1, startColumn = 1, endLine = 1, endColumn = 17})) DOCUMENT [Node (Just (PosInfo {startLine = 1, startColumn = 1, endLine = 1, endColumn = 17})) PARAGRAPH [Node Nothing (TEXT "dog") [],Node Nothing (TEXT "\8217") [],Node Nothing (TEXT "s ") [],Node Nothing EMPH [Node Nothing (TEXT "breakfast") []]]]
    ```

[CommonMark]: http://commonmark.org
[libcmark]: http://github.com/jgm/cmark
[benchmarks]: https://github.com/jgm/cmark/blob/master/benchmarks.md
[cheapskate]: https://hackage.haskell.org/package/cheapskate
[pandoc]: https://hackage.haskell.org/package/pandoc
[sundown]: https://hackage.haskell.org/package/sundown
[markdown]: https://hackage.haskell.org/package/markdown
[commonmark.js]: http://github.com/jgm/commonmark.js
