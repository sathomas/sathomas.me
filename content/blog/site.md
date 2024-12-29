---
title: How the Site is Built
description: The applications, utilities, and libraries I use for the site
date: 2025-01-08
tags: site
---

The applications, utilities, and libraries that make this site possible also make me feel guilty. My own contributions to open source software seem pretty lame in comparison. Perhaps something that starts here will lead to a useful project. At the very least, I want to acknowledge all of the foundations of this site and offer my heartfelt thanks to their authors, maintainers, and contributors .

In the beginning I assumed it would be easy to build the kind of site I wanted. There were only two requirements, and they both seemed fairly simple:

1. No JavaScript.
2. Nicely rendered mathematics.

It turns out that those requirements aren’t so trivial after all. The two major math rendering libraries are both designed to run as JavaScript in the browser. In theory, both can also execute in a `node.js` environment during the build process, but that approach introduces significant complexity and compromises. Furthermore, the output formats seem to be limited to unattractive <abbr>SVG</abbr> graphics or a Rube Goldberg combination of <abbr>HTML,</abbr> web components, and <abbr>CSS.</abbr>

Fortunately, modern browsers have pretty good support for [MathML](https://www.w3.org/Math/) and there are tools that generate MathML from LaTeX input. So it’s possible to write mathematics in the format familiar to most mathematical authors:

```latex
\def\d{\mathrm{d}}

\oint_C \vec{B}\circ \d\vec{l} = \mu_0
\left(
  I_{\text{enc}} +
    \varepsilon_0
    \frac{\d}{\d t}
    \int_S {\vec{E} \circ \hat{n}}\; \d a
\right)
```

And have it appear as a nice equation whose styling perfectly matches other content on the site:
$$
\def\d{\mathrm{d}}

\oint_C \vec{B}\circ \d\vec{l} = \mu_0 \left( I_{\text{enc}} + \varepsilon_0 \frac{\d}{\d t} \int_S {\vec{E} \circ \hat{n}}\; \d a \right)
$$
Once I had a solution for mathematical content, putting everything together was pretty straightforward. The full set of tools that produce this site include:

- Static site generator: [Eleventy](https://www.11ty.dev).
- Math rendering: [<abbr>TEMML</abbr>](https://temml.org) via a [plugin](https://github.com/traeblain/markdown-it-temml).
- Additional markdown plugins for [subscripts](https://github.com/markdown-it/markdown-it-sub), [superscripts](https://github.com/markdown-it/markdown-it-sup), and [table captions](https://github.com/martinring/markdown-it-table-captions).
- Theme: Adopted from [<abbr>LATEX</abbr>.css](https://latex.vercel.app).
- Fonts: Latin Modern families from [<abbr>GUST</abbr>](https://www.gust.org.pl/projects/e-foundry/latin-modern).
- Icons: [Material Icons](https://fonts.google.com/icons), brand icons, and custom.

For the writing process itself the [typora](https://typora.io) editor deserves a special acknowledgment. I’ve been using it since the early beta releases, and it was the primary writing tool for my last book, my PhD thesis, and several academic papers (including the latest currently in review, fingers crossed).
