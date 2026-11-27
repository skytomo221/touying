---
sidebar_position: 2
---

# MiTeX

スライドを作成する過程では、すでにLaTeXの数式があり、それをTypstの数式に書き直さず、そのままスライドに貼り付けたいことがよくあります。このような場合は、[MiTeX](https://github.com/mitex-rs/mitex) を使用できます。

例：

```example
#import "@preview/mitex:0.2.6": *

Write inline equations like #mi("x") or #mi[y].

Also block equations (this case is from #text(blue.lighten(20%), link("https://katex.org/")[katex.org])):

#mitex(`
  \newcommand{\f}[2]{#1f(#2)}
  \f\relax{x} = \int_{-\infty}^\infty
    \f\hat\xi\,e^{2 \pi i \xi x}
    \,d\xi
`)
```

Touyingは `touying-mitex` 関数も提供しています。例えば、次のように使用できます。

```example
#import "@preview/touying:0.8.0": *
#import "@preview/mitex:0.2.6": *
#import themes.simple: *
#show: simple-theme

#touying-mitex(mitex, `
  f(x) &= \pause x^2 + 2x + 1  \\
      &= \pause (x + 1)^2  \\
`)
```