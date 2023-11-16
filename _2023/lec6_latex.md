---
layout: lecture
title: "LaTeX"
date: 2024-01-16
ready: false
phase: 2
---

# A quick introduction to LaTeX

## What is LaTeX?

## When to and not to use LaTeX

# LaTeX with Overleaf

Overleaf is one of many LaTeX editors available to use. To quote their website, Overleaf is "The easy to use, online, collaborative LaTeX editor".

While not FOSS software, Overleaf is free to access for all University of Birmingham students. To get started, head to [https://www.overleaf.com/login](https://www.overleaf.com/login) and log in through your institution. We will discuss FOSS alternatives to Overleaf at the end of the lesson.

![overleaf_login](/2023/files/latex/overleaf_login.png "Login page of overleaf.com.")

Let's create a new blank and explore what Overleaf has to offer.

![overleaf_editor](/2023/files/latex/overleaf_editor.png "Overleaf's editor view")

# The basics

# Document classes and packages

# Typesetting more exciting stuff

## Mathematical expressions

LaTeX only really starts to shine when used to typeset complex content that cannot be easily formatted in a WYSIWYG editor. A good example of this is mathematical expressions and equations. While this is not supported by LaTeX entirely out-of-the-box, mathematical statements can be typeset using the `amsmath` package with symbols from the `amssymb` package.

Below is a simple equation typeset inline with other text. Observe how the expression is enclosed in `$` signs.

```latex
This is an equation of the form $x + y = z$
```

![output_amsmath_inline](/2023/files/latex/output_amsmath_inline.png "A simple equation inline with other text.")

We can also typeset special characters using specific commands. For example,

```latex
$\alpha \rightarrow \sin(\Delta x)$
```

yields
![output_amsmath_special](/2023/files/latex/output_amsmath_special.png "An equation with special characters.")

Below are a few commonly used maths symbols:

| Category             | Symbols   | Commands                                         |
|----------------------|-----------|--------------------------------------------------|
| Relational operators | ≤ ≥ ≠ ⊂   |`\leq`, `\geq`, `\neq`, `\subset`                 |
| Vector operators     | ⋅ × ∇      | `\cdot`, `\times`, `\nabla`                      |
| Greek letters        | α β δ Δ    | `\alpha`, `\beta`, `\delta`, `\Delta`            |
| Miscellaneous        | ∫ ∑ ∴ ∵ ∎ | `\int`, `\sum`, `\therefore`, `\because`, `\QED` |

A comprehensive list of symbols can be found [here](https://tug.ctan.org/info/symbols/comprehensive/symbols-a4.pdf).

Fractions can be typeset using the `\frac` command which takes two arguments -- the numerator and denominator expression.

```latex
$\frac{5x}{10} = \frac{x}{2}$
```

![output_amsmath_frac](/2023/files/latex/output_amsmath_frac.png "An equation with fractions.")

### Equations

So far, we have only learned how to inline our maths expressions. Some use cases may require a full-width expression, say a theorem we might wish to introduce in our document. `amsmath` provides an `equation` environment that allows for this, which we use below to typeset the Pythagorean theorem.

```latex
\begin{equation}
    c^2 = b^2 + a^2
\end{equation}
```

![output_amsmath_equation](/2023/files/latex/output_amsmath_equation.png "A full-width equation.")

Observe how LaTeX automatically numbers and centre-aligns the equation.
To typeset and align multiline equations, we use the `align` environment:

```latex
\begin{align}
    x^2 &= 49\\
    \therefore x &= \pm 7
\end{align}
```

![output_amsmath_align](/2023/files/latex/output_amsmath_align.png "A multi-line equation.")

The output can be thought of as a table where columns are demarcated by `&` and rows by `\\`. In other words, `&` denotes the end of a column and `\\` denotes the end of a line. Using this information, `amsmath` typesets the equation such that all `&` are aligned one below the other. Observe how the two lines of differing length are aligned to the `=`symbol.

A handy tip to note is that automatic line numbering can be switched off by suffixing `*` to `amsmath` environments, e.g. `\begin{align*}` and `\end{align*}`. Alternatively, numbering can be turned off for specific lines by starting them with `\nonumber`. For example,

```latex
\begin{align}
    \nonumber x^2 &= 49\\
    \therefore x &= \pm 7
\end{align}
```

![output_amsmath_nonumber](/2023/files/latex/output_amsmath_nonumber.png "A multi-line equation where some lines are unnumbered.")

## Code

As programmers, we often need to embed code snippets or entire programs in documents. There are LaTeX packages that, along with formatting code nicely, also provide syntax highlighting for a variety of languages. We will briefly cover two such packages: `minted` and `listings`.

### Using `minted`

Code is embed inside a `minted` environment that allows for several customisations. For example, the following is some Java code that has been typeset with syntax highlighting and line numbers:

```latex
\begin{minted}[linenos]{java}
class Latex {
    public static final String message = "Typesetting code with LaTeX!";
}
\end{minted}
```

![output_minted](/2023/files/latex/output_minted.png "Some Java code typeset using minted.")

Similar to `linenos`, we can pass other options to the `minted` environment. Below is some python code highlighted using vim's colour scheme and with a black background.

```latex
\begin{minted}[style=vim,bgcolor=black]{python}
def add(x, y):
    return x + y
\end{minted}
```

![output_minted_vim_scheme](/2023/files/latex/output_minted_vim_scheme.png "Some python code typeset using minted.")

`minted` also allows for for embedding a single line of code:

```latex
\mint{js}|let someVariable = 5;|
```

![output_mint](/2023/files/latex/output_mint.png "A single line of JavaScript typeset using \mint.")

A comprehensive list of what `minted` is capable of can be found [here](https://tug.ctan.org/macros/latex/contrib/minted/minted.pdf).

### Using `listings`

## Images

## Tables and lists

## Graphs

# Integrating your work with Git

# Citing and referencing content

# Using LaTeX offline

# Additional reading
