Dynamic Content Generation with `quarto`
======================================

---

Dynamic Reports
===============

Traditionally, report generation is a two-step process:

1. Analyze data/run models in software of choice
2. Export the results and integrate into separate report document

This results in a static document. Any changes to the analysis forces you back to step one!

**Dynamic reports** are generated in a one-step process:

1. Construct an integrated analysis and report document
2. There is no step 2

Dynamic reports can be updated automatically if data or analysis change.

---

A Brief History of Things
=========================

Famed computer scientist Donald Knuth invented TeX and coined *literate programming*.

* typesetting
  * TeX (1978)
* literate programming (1984)
  * natural language mixed with source code
  * noweb (1989)
  * Sweave - interfacing R/S with LaTeX (2002)
  * [knitr](http://yihui.name/knitr/) - extension of Sweave (2012)
  * [quarto](https://quarto.org/index.html) - another extension (2020)
* markup languages
  * the anti-word processor WYSIWYG
  * LaTeX (1984)
  * HTML (1989)
  * markdown (2004)
* data serialization
  * generalized data structure
  * YAML (2001)
* document conversion
  * pandoc (2006)

---

File Extensions
===============

| extension | description |
| --------- | ----------- |
| .tex | LaTeX |
| .nw | noweb |
| .Rnw | R + LaTeX |
| .md | markdown |
| .Rmd | R + markdown |
| .qmd | R/Python/Julia + markdown |

---

What Did *Sweave* Give Us?
========================

- dynamic, reproducible reports
- combine R code chunks within LaTeX
- headaches - LaTeX is hard

Example code chunk

    <<my-label, eval=TRUE, dev='png'>>=
    set.seed(1213)  # for reproducibility
    x = cumsum(rnorm(100))
    mean(x)  # mean of x
    plot(x, type = 'l')  # Brownian motion
    @

The `<< >>=` delimiter indicates the beginning of a chunk, and can contain zero or more user-defined options, including a label.

The `@` delimiter indicates the end of a chunk. Anything beyond this symbol is not processed as code.

---

What Did *knitr* Give Us?
========================

- full Sweave functionality
- replaced LaTeX with Markdown/MultiMarkdown/MathJax
- supports PDF and HTML output
- more code chunk options
- code decoration
- built-in cacheing
- it's an R package

---

What Does *quarto* Give Us?
========================

- full knitr functionality
- code chunks with Python and Julia
- even more output types

Quarto actually uses Knitr to execute R code

---

Code Chunks
===============

knitr/quarto code chunks use this format

  	```{r}
    #| label: my-label
    #| eval: true
    #| dev: 'png'
    set.seed(1213)  # for reproducibility
    x = cumsum(rnorm(100))
    mean(x)  # mean of x
    plot(x, type = 'l')  # Brownian motion
    ```

---

Chunk Options
=============

Chunk options previously had a `key=value` format:

    ```{r mychunk, cache=TRUE, eval=FALSE, dpi=100}

Now they can be provided as one option per line with YAML syntax, `key: value`.

Options can take values from executable R code when proceeded by `!expr`.

    ```{r}
    #| label: mychunk
    #| cache: true
    #| eval: false
    #| dpi: 100
    #| fig-cap: !expr 'paste("Updated", Sys.Date())'

Avoid spaces and periods in chunk labels and directory names.

---

Chunk Options
=============

- **eval**: (*true* or false) whether to evaluate the code chunk
- **echo**: (*true* or false) whether to include R source code in the output file
- **results**: ('markup'; character) takes four possible values
    + 'markup': mark up the results using the output hook
    + 'asis': write raw results from R into the output document
	+ 'hold': all output should appear at end of chunk
    + 'hide' hide results
- **error**: (true or *false*) force evaluation to stop on error
- **include**: (*true* or false) whether to include the chunk output in the final output document
- **comment**: ('##'; character) the prefix to be put before source code output
- **cache**: (true or *false*) whether to cache a code chunk
- **dev**: ('pdf' for LaTeX output and 'png' for HTML/markdown; character) the graphical device to record plots
- **fig.width**, **fig.height**: width/height of plot in inches

Comprehensive list of options at <https://yihui.org/knitr/options/>

---

Global Options
==============

Chunk options that will apply by default to every chunk in a document can be set using the `opts_chunk` object:

    ```{r}
    #| label: setup
    #| include: false
    knitr::opts_chunk$set(fig.width = 5, fig.height = 5)
    ```

This example will constrain the dimensions of all figures to 5x5.

---

Inline Code
===========

We often wish to integrate variables and output from code chunks into sentences of our report.
This can be done using the `r` command. For example:

    The first element of *x* is `r x[1]`.

... produces this sentence in Markdown:

The first element of *x* is -0.2412.

---

How To Stop
===========

There may be times when we want to knit/render a report, but only up to a point.
Rather than commenting out several lines, we can use this code chunk to immediately stop execution.
Only code chunks evaluated before this will be included in the output.

    ```{r}
    #| label: stopper
    #| echo: false
    knitr::knit_exit()
    ```

---

What's the Front Matter?
========================

The very beginning of a rmd/qmd file contains the YAML front-matter.
It stores information (metadata) about how to compile the markdown into a final document.

The options (metadata keys) will often vary depending on the format type.
[Here](https://quarto.org/docs/reference/formats/html.html) is the documentation for HTML.

Here's an example where we can render both HTML and PDF output.

    ---
    title: My Report
    author: Me
    date: last-modified
    format:
      html:
        embed-resources: true
        toc: true
        code-fold: show
        fig-width: 5
        fig-height: 5
      pdf:
        pdf-engine: pdflatex
        keep-tex: true
        toc: true
        code-fold: show
        fig-width: 5
        fig-height: 5
    ---

"pdf-engine" is usually unnecessary as RStudio includes a LaTeX compiler called "tinytex".

---

Other Types of Output
=======================

The internal markdown code used to create HTML/PDF output is mostly interchangeable.
There may be a few extra steps with other forms of output,
such as [presentations](https://quarto.org/docs/presentations/),
[dashboards](https://quarto.org/docs/dashboards/), and
[websites](https://quarto.org/docs/websites/).

For example, a "revealjs" presentation may require creating a "div" with a referenced CSS class.
The following would create a slide where the two list items are revealed incrementally.

    ::: {.incremental}
    - Eat spaghetti
    - Drink wine
    :::

The ":::" is creating a "div", and the name of the CSS class is ".incremental".
CSS classes have defined behaviour and formatting, though these can be modified.

---

Exercise
========

Grab the files `binomial.tex` and `binomial.R` in the exercises folder of the Bios6301 GitHub repo.

1. Convert the LaTeX code into Markdown - you'll delete most of it.
2. Integrate the R code with the Markdown to produce a working `qmd` report. Verify that it works.
3. Change the options on the R code so that it shows shows the plot, but not the code itself.
4. Add a sentence at the bottom of the document that says: "*The mean value of the binomial sample was was x*", but replace *x* with a variable that inserts the actual mean value.
