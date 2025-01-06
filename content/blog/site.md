---
title: How the Site is Built
description: The applications, utilities, and libraries I use for the site
date: 2025-01-06
tags: site
---

I’m truly grateful to all the generous developers whose open source software makes this site possible. They certainly deserve acknowledgment as well as other forms of support. And since some readers might be curious to learn the reasons behind my choices, a brief post on the site’s construction seemed appropriate. When I began to contemplate a new blog, there were really only two requirements, and they both seemed fairly simple:

1. No JavaScript in the browser.
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
With a solution for mathematical content, putting everything together was pretty straightforward. There are plenty of static site generating applications, but Eleventy looked like a good fit for me. It provides easy control over the <abbr>HTML</abbr> and <abbr>CSS</abbr> that ultimately make up the site, and it’s implemented in language (JavaScript) and environment (`node.js`) with which I have a lot of familiarity. I’ve tried to keep other dependencies to a minimum, so most of the functionality is built into Eleventy. The site does rely on a few extensions, however, As of this writing, the applications, libraries, and tools that produce this site include:

- Static site generator: [Eleventy](https://www.11ty.dev).
- Math rendering: Ron Kok’s [<abbr>TEMML</abbr>](https://temml.org) via Trae Blain’s [plugin](https://github.com/traeblain/markdown-it-temml).
- Additional markdown plugins for [subscripts](https://github.com/markdown-it/markdown-it-sub), [superscripts](https://github.com/markdown-it/markdown-it-sup), and, from Martin Ring, [table captions](https://github.com/martinring/markdown-it-table-captions).
- Theme: Adopted from Vincent Dörig’s awesome [<abbr>LATEX</abbr>.css](https://latex.vercel.app).
- Fonts: Latin Modern families from [<abbr>GUST</abbr>](https://www.gust.org.pl/projects/e-foundry/latin-modern).
- Icons: [Material Icons](https://fonts.google.com/icons), brand icons, and custom.

For the writing process itself the [typora](https://typora.io) editor from appmakes deserves a special acknowledgment. I’ve been using it since the early beta releases, and it was the primary writing tool for my last book, my PhD thesis, and various academic papers.
