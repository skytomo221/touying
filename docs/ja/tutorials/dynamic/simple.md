---
sidebar_position: 1
---

# シンプルなアニメーション

Touyingには簡単なアニメーションを実現する `#pause` と `#meanwhile` という2つのマーカーがあります。

## pause

`#pause` を置くと、それ以降の内容が次のサブスライドで表示されます。`#pause` を複数置けば、複数のサブスライドを作成できます。次の例を見てください。

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

この例では3つのサブスライドが作られ、内容が段階的に表示されます。

`#pause` は行の途中にも単独の行にも配置できます。

## meanwhile

`#pause` による段階表示と並行して、別の内容も切り替えたい場合には `#meanwhile` を使用します。

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

この例ではサブスライドが2つだけ作られます。最初は「First」と「Third」が、次は「Second」と「Fourth」が同時に表示されます。