---
sidebar_position: 1
---

# 記述スタイル

## シンプルスタイル

単純な用途であれば、通常のTypst文書を記述するのと同じように、見出しの下に直接内容を記述できます。ここでの見出しはページを分ける役割を果たし、`#pause` などのコマンドも通常どおり使用してアニメーション効果を実現できます。

```example
#import "@preview/touying:0.8.0": *
#import themes.simple: *

#show: simple-theme.with(aspect-ratio: "16-9")

= Title

== First Slide

Hello, Touying!

#pause

Hello, Typst!
```

また、空の見出し `== <touying:hidden>` を使用して新しいページを作成できます。これは前の見出しが引き続き適用される状態を解除するのにも役立ちます。

現在の見出しを維持し、単に新しいページを追加したい場合は、`#pagebreak()` を使用するか、直接 `---` を使用してページを分割できます。Touyingでは後者は `#pagebreak()` として解析されます。

```example
#import "@preview/touying:0.8.0": *
#import themes.simple: *

#show: simple-theme.with(aspect-ratio: "16-9")

= Title

== First Slide

Hello, Touying!

---

Hello, Typst!
```

## ブロックスタイル

シンプルスタイルだけでは、必要な機能をすべて実現できないことがよくあります。より強力な機能と明確な構造のために、`#slide[...]` という形式のブロックスタイルを使用することもできます。

例えば、上の例は次のように変換できます。

```example
#import "@preview/touying:0.8.0": *
#import themes.simple: *

#show: simple-theme.with(aspect-ratio: "16-9")

= Title

== First Slide

#slide[
  Hello, Touying!

  #pause

  Hello, Typst!
]
```

また、`#empty-slide[]` はヘッダーとフッターのない空のスライドを作成できます。

このように記述することには多くの利点があります。

1. デフォルトの `#slide[...]` だけでは足りず、特別な `slide` 関数、例えば `#focus-slide[...]` なども必要になることがよくあります。
2. テーマによっては `#slide[...]` 関数がデフォルトより多くのパラメーターを備えていることがあります。例えば、metropolisテーマの `#slide[...]` 関数には配置を設定できる `align` パラメーターがあります。
3. `slide` 関数だけがコールバック形式のコンテンツブロックを利用し、`#only` と `#uncover` 関数を使って複雑なアニメーション効果を実現できます。
4. 構造をより明確にできます。`#slide[...]` ブロックを識別することで、スライドの具体的なページ分割を容易に判別できます。

## 設定より規約

simpleテーマを使用したとき、第1レベルの見出しを使うと自動的にセクションスライドが作成されることに気付いたかもしれません。これはsimpleテーマが `config-common(slide-fn: slide, new-section-slide-fn: new-section-slide)` 関数を登録しており、Touyingがデフォルトでこの関数を呼び出すためです。

このようなセクションスライドが自動的に作成されないようにしたい場合は、このメソッドを削除できます。

```example
#import "@preview/touying:0.8.0": *
#import themes.simple: *

#show: simple-theme.with(
  aspect-ratio: "16-9",
  config-common(new-section-slide-fn: none),
)

= Title

== First Slide

Hello, Touying!

#pause

Hello, Typst!
```

ご覧のとおり、これによって2ページだけが生成され、デフォルトのセクションスライドはなくなります。

同様に、新しいセクションスライドを登録することもできます。

```example
#import "@preview/touying:0.8.0": *
#import themes.simple: *

#show: simple-theme.with(
  aspect-ratio: "16-9",
  config-common(new-section-slide-fn: section => {
    touying-slide-wrapper(self => {
      touying-slide(
        self: self,
        {
          set align(center + horizon)
          set text(size: 2em, fill: self.colors.primary, style: "italic", weight: "bold")
          utils.display-current-heading(level: 1)
        },
      )
    })
  }),
)

= Title

== First Slide

Hello, Touying!

#pause

Hello, Typst!
```