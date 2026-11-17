---
sidebar_position: 1
---

# 記述スタイル

## シンプルスタイル

基本的な使い方では、通常のTypst文書のように見出しの下に内容を直接記述できます。見出しがページの区切りとなり、`#pause` でアニメーションを実現できます。

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

空の見出し `== <touying:hidden>` を使うと新しいページを作れます。前の見出しの適用を解除する場合にも便利です。

現在の見出しを維持したまま改ページするには `#pagebreak()` または `---` を使います。Touyingでは `---` も `#pagebreak()` として解釈されます。

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

シンプルスタイルだけでは足りないときには、より高度な機能と明確な構造を備える `#slide[...]` 形式のブロックスタイルを使用できます。

たとえば、上の例を次のように書き換えられます。

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

`#empty-slide[]` はヘッダーやフッターのない空のスライドを作成します。

ブロックスタイルには次の利点があります。

1. 標準の `#slide[...]` だけでなく、`#focus-slide[...]` のような特別なスライド関数も使用できます。
2. テーマによって追加パラメーターを利用できます。たとえばmetropolisテーマの `#slide[...]` には配置を設定する `align` があります。
3. コールバック形式の内容ブロックと `#only`・`#uncover` を組み合わせた複雑なアニメーションを実現できます。
4. `#slide[...]` ブロックによってスライドの区切りと構造が明確になります。

## 設定より規約

simpleテーマでは第1レベルの見出しを書くと自動的にセクションスライドが作られます。これは `config-common(slide-fn: slide, new-section-slide-fn: new-section-slide)` が登録されているためです。

自動生成したくない場合は、次のように設定を変更します。

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

この設定では2ページだけが生成され、既定のセクションスライドはなくなります。

同様に、独自のセクションスライドを登録することもできます。

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