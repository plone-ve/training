---
myst:
  html_meta:
    "description": "Authors' guide to writing Plone Trainings. It covers configuring quality checks and syntax for writing markup that is of particular interest to authors."
    "property=og:description": "Authors' guide to writing Plone Trainings. It covers configuring quality checks and syntax for writing markup that is of particular interest to authors."
    "property=og:title": "Authors Guide"
    "keywords": "Plone, Trainings, SEO, meta, presentation, exercises, solutions, Vale, linkcheck, lexer"
---

(authors-guide-label)=

# Authors Guide

This guide is for authors of Training documentation.
It covers configuring quality checks and syntax for writing markup that is of particular interest to authors.
For general markup syntax, see {doc}`writing-docs-guide`.


(authors-html-meta-data-label)=

## HTML and Open Graph Metadata

All documents must have a `myst` topmatter key with an `html_meta` directive at the top of every page.
When rendered to HTML, it inserts `<meta>` tags for improved search engine results and nicer social media posts.
Authors should include at least `description`, `property=og:description`, `property=og:title`, and `keywords` meta tags.

The following is an example of `html_meta`.
Note that the content of the two tags `description` and `property=og:description` should be identical.

```md
---
myst:
  html_meta:
    "description": "Authors' guide to writing Plone Trainings. It covers configuring quality checks and syntax for writing markup that is of particular interest to authors."
    "property=og:description": "Authors' guide to writing Plone Trainings. It covers configuring quality checks and syntax for writing markup that is of particular interest to authors."
    "property=og:title": "Authors Guide"
    "keywords": "Plone, Trainings, SEO, meta, presentation, exercises, solutions, Vale, linkcheck, lexer"
---
```

This renders in the HTML `<head>` section as follows.

```html
<meta content="Authors' guide to writing Plone Trainings. It covers configuring quality checks and syntax for writing markup that is of particular interest to authors." name="description" />
<meta content="Authors' guide to writing Plone Trainings. It covers configuring quality checks and syntax for writing markup that is of particular interest to authors." property="og:description" />
<meta content="Authors Guide" property="og:title" />
<meta content="Plone, Trainings, SEO, meta, presentation, exercises, solutions, Vale, linkcheck, lexer" name="keywords" />
```

Additional {term}`Open Graph` metadata is implemented through the Sphinx extension [`sphinxext-opengraph`](https://github.com/sphinx-doc/sphinxext-opengraph) and the [MyST `html_meta` directive](https://myst-parser.readthedocs.io/en/latest/configuration.html#global-configuration), which resolves to the [Docutils `meta` directive](https://docutils.sourceforge.io/docs/ref/rst/directives.html#metadata).
See the site-wide configuration in {file}`conf.py`.


(authors-presentation-markup-label)=

## Writing Presentation Markup

The `presentation` build creates an abbreviated version of the documentation.
It is designed for projectors which are typically low resolution and have limited screen space.
Authors should use bullet points instead of long narrative text.

The command `make presentation` as described in {ref}`setup-build-make-presentation-label` is implemented through the [Sphinx `only` directive](https://www.sphinx-doc.org/en/master/usage/restructuredtext/directives.html#directive-only).

The `presentation` builder is configured in {file}`Makefile` using the [`sphinx-build` flag `-t`](https://www.sphinx-doc.org/en/master/man/sphinx-build.html#cmdoption-sphinx-build-t).

To show something in the `presentation` build and hide in the `html` build, use the following syntax.

````md
```{only} presentation
> This will appear only in the presentation build.
```
````

This will render as follows.

```{only} presentation
> This will appear only in the presentation build.
```

To hide something in the `presentation` build and show in the `html` build, use the following syntax.

````md
```{only} not presentation
> I am hiding from the presentation build.
```
````

This will render as follows.

```{only} not presentation
> I am hiding from the presentation build.
```


(authors-exercises-and-solutions-label)=

## Writing Exercises and Solutions

We use [Sphinx Design's dropdowns](https://sphinx-design.readthedocs.io/en/latest/dropdowns.html) to hide and show solutions to exercises in the trainings.

We use its options of `:animate: fade-in-slide-down` and `:icon: question` to animate and provide an icon for the item.

Note that the markup uses [MyST nested directives](https://myst-parser.readthedocs.io/en/latest/syntax/roles-and-directives.html#nesting-directives).

`````md
````{dropdown} This is a title
:animate: fade-in-slide-down
:icon: question

```{code-block} python
:linenos:
:emphasize-lines: 1, 3

a = 2
print("my 1st line")
print(f"my {a}nd line")
```

````
`````

This will render as follows.

````{dropdown} This is a title
:animate: fade-in-slide-down
:icon: question

```{code-block} python
:linenos:
:emphasize-lines: 1, 3

a = 2
print("my 1st line")
print(f"my {a}nd line")
```

````

(authors-quality-checks-label)=

## Quality checks

We strive for high quality documentation, setting the following minimum standards.


(authors-markup-syntax-label)=

### Markup syntax must be valid

See both the specific markup syntax above and general markup in {doc}`writing-docs-guide`.

To validate markup, run the following command.

```shell
make html
```

Open `/_build/html/index.html` in a web browser.


(authors-english-label)=

### American English spelling, grammar, and syntax, and style guide

Spellings are enforced through [`Vale`](https://vale.sh/).
Plone uses American English.

Spelling is configured in {file}`Makefile`, {file}`.vale.ini`, and in files in `styles/Vocab/Plone/`.

Authors should add new words and proper names using correct casing to {file}`styles/Vocab/Plone/accept.txt`, sorted alphabetically and case-insensitive.

Vale also provides English grammar and syntax checking, as well as a Style Guide.
We follow the [Microsoft Writing Style Guide](https://learn.microsoft.com/en-us/style-guide/welcome/).

To perform all these checks, run the following command.

```shell
make vale
```

Because it is difficult to automate good American English grammar and syntax, we do not strictly enforce it.
We also understand that contributors might not be fluent in English.
We encourage contributors to make a reasonable effort, and to request a review of their pull request from community members who are fluent in English to fix grammar and syntax.
Please ask!


(authors-linkcheck-label)=

### All links must be valid

```{important}
Before you add a link, consider whether you really need it for the training.
Avoid linking to blog posts because they rapidly succumb to bitrot.
It is preferred to copy the content from the source and add a link to the source as a reference through a `seealso` admonition or footnote, than to merely link to the source.
```

Valid links are enforced automatically through Sphinx's `linkcheck` builder.

[Configuration of the `linkcheck` builder](https://www.sphinx-doc.org/en/master/usage/configuration.html#options-for-the-linkcheck-builder) is in {file}`Makefile` and {file}`docs/conf.py`.

`linkcheck_ignore` supports regular expression syntax.

When authors add a link to their training, it must be a valid public URL without requiring authentication.

If it is not a valid link, or is private or local, then you must exclude it from `linkcheck` by wrapping it in single backticks.

```md
Visit the URL `http://www.example.com` for an example.
```

This will render as follows.

> Visit the URL `http://www.example.com` for an example.

If a link has succumbed to bit rot, then try finding the most recently scraped version on the [Internet Archive Wayback Machine](https://web.archive.org/), and update the link.

To validate links, run the following command.

```shell
make linkcheck
```

Open `/_build/presentation/output.txt` for a list of broken links.

```{danger}
Please do not abuse `linkcheck_ignore`.

There is a special place in hell reserved for contributors who do not bother to update bad links, either dead ones or redirects, causing `linkcheck` to fail.
And there is a doubly punishing place for those who disable `linkcheck` because there are too many bad links.

Please do not be "that person".
```


(authors-syntax-highlighting-label)=

### Syntax highlighting

Pygments provides syntax highlighting in Sphinx.

When including code snippets, you should specify the language.
Authors must use a proper [Pygments lexer](https://pygments.org/docs/lexers/) and not generate warnings.

The snippet must be valid syntax for the language you specify, else it will not be highlighted properly.
Avoid adding comments to code snippets, unless you use valid comment syntax for that language.
For example, JSON does not allow comments.

Do not indicate elided or omitted code with ellipses (`...` or `…`).
These are almost never valid syntax and will cause syntax highlighting to fail for the code block.


#### Choosing a Lexer

Some lexers are less than perfect.
If your code block does not highlight well, then consider specifying a less ambitious lexer, such as `text`.

Use `shell` for commands to be issued in a terminal session.
Do not include shell prompts.
This will make commands easy to copy and paste for readers.

Use `console` for output of a shell session.
If you have a mix of a shell command and its output, then use `console`.

If `xml` does not work well, then try `html`.

`jsx` has a complex syntax that is difficult to parse.
We have high hopes for the project [`jsx-lexer`](https://github.com/fcurella/jsx-lexer).
We include it in our `requirements.txt` file.
Please contribute to its further development.

The lexers `html+ng2`, `scss`, `http`, `less` are also suboptimal and particular.

If no other lexer works well, then fall back to `text`.
At least then the build will succeed without warnings, although syntax highlighting for such snippets will not appear.


#### Validate the Lexer

Always build the page to validate syntax.
Your own training should not be merged into the larger Training docs if there are any Sphinx warnings.
The Sphinx console will display any warnings, such as the following.

```console
/Plone/training/voltohandson/introtoblocks.md:55: WARNING: Could not lex literal_block as "jsx". Highlighting skipped.
```

The above warning indicates that the syntax is not valid.
Common mistakes include:

- Using `...` or `…` to indicate omitted code.
  It is preferable to never use ellipses.
  If you must do that, comment it out using the language's comment syntax.
- Using comments in JSON.
- A previous code block bleeds through to the next due to invalid MyST syntax.

To validate code block syntax, run the following command.

```shell
make html
```

An [online demo of all lexers that Pygments supports](https://pygments.org/demo/) may be helpful to test out your code blocks and snippets for syntax highlighting.
You can also use the [`pygmentize`](https://pygments.org/docs/cmdline/) binary.

When using the online lexer, if any red-bordered rectangles appear, then the lexer for Pygments interprets your snippet as not valid.
You can search the [Pygments issue tracker](https://github.com/pygments/pygments/issues?q=) for possible solutions, or submit a pull request to enhance the lexer.


## Synchronize the Browser While Editing

Use `sphinx-autobuild` to view changes in the browser while editing documentation.

```shell
make livehtml
```

You can open a browser at http://127.0.0.1:8000/ to preview the documentation.
