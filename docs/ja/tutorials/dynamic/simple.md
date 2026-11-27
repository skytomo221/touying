---
sidebar_position: 1
---

# シンプルなアニメーション

Touyingは簡単なアニメーション効果のために、`#pause` と `#meanwhile` という2つのマーカーを提供しています。

## pause

`#pause` の目的は単純です。それ以降の内容を次のサブスライドに分けることです。`#pause` を複数使用して、複数のサブスライドを作成できます。以下は簡単な例です。

```example
>>> #import "@preview/touying:0.8.0": *
>>> #import themes.simple: *
>>> #show: simple-theme
#slide[
  First #pause Second

  #pause

  Third
]
```

この例では3つのサブスライドが作成され、内容が段階的に表示されます。

ご覧のとおり、`#pause` は行の途中でも、独立した行でも使用できます。

## meanwhile

場合によっては、`#pause` による表示と同時に、ほかの内容を表示する必要があるかもしれません。その場合は `#meanwhile` を使用できます。

```example
>>> #import "@preview/touying:0.8.0": *
>>> #import themes.simple: *
>>> #show: simple-theme
#slide[
  First
  
  #pause
  
  Second

  #meanwhile

  Third

  #pause

  Fourth
]
```

この例ではサブスライドが2つだけ作成され、「First」と「Third」が同時に表示され、「Second」と「Fourth」が同時に表示されます。