---
sidebar_position: 4
---

# カバー関数

`uncover` と `#pause` は、まだ表示しない内容を隠すために `cover` 関数を使用します。この関数の仕組みを説明します。

## 既定のカバー関数：`hide`

`cover` 関数は `self.methods.cover` に格納されるメソッドで、`uncover` と `#pause` から使用されます。

既定のカバー関数は [hide](https://typst.app/docs/reference/layout/hide/) です。レイアウトに影響させずに内容を不可視にします。以下のように設定に登録されています。

## カバー関数を変更する

独自の `cover` 関数を使用したい場合は、次のように設定します。 

```typst
config-methods(cover: utils.hiding-cover)
```

独自のメソッドに求められる仕様は、後述の[独自のカバー関数を作成する](#writing-your-own-cover-function)を参照してください。

## 透明度や色を変えるカバー関数

Touyingは半透明にするカバー関数にも対応しています。次のように有効化できます。

```typst
config-methods(cover: utils.alpha-changing-cover)
```

`.with(alpha: 30%)` を追加すれば、`alpha: ..` パラメーターで透明度を調整できます。

また、すべての色を指定色に変更する `utils.color-changing-cover` も使用できます。


:::tip[内部の仕組み]

`utils.alpha-changing-cover` は見つかった色のアルファ値を下げることで動作します。入れ子になったスタイルやコンテキストの各階層でTypstのコンテキストを参照するため、処理負荷が大きくなる場合があります。

コンパイルが遅い場合は `utils.color-changing-cover` への切り替えを試してください。

両方のメソッドには、画像やタイル状の描画など色を変更できない内容があります。そのため `fallback-hide` を使用し、`utils.semi-transparent-cover` による灰色の半透明の矩形を重ねて似た効果を実現します。ただし、この関数を主要なカバー関数として使うことは、未修正の問題が複数あるため推奨されません。

:::

### 塗りつぶした図形のカバー

独自の背景を描画する要素は、通常は色を変更すべきではありません。図形とその上の文字が同色になって読めなくなることがあります。画像が灰色の矩形に変わる場合を想像してください。
そのため `utils.color-changing-cover` は、塗りつぶし済みの要素を代替の非表示処理に渡せます。既定では `fallback-for-filled: true` です。これを無効にすると、ほかの要素と同様に色を変更してスライド全体を均一に灰色化します。

```typst
config-methods(cover: utils.color-changing-cover.with(fallback-for-filled: false))
```

`utils.alpha-changing-cover` にはこのパラメーターがありません。アルファ値を下げることで、内容と同色にせず図形を薄く表示できるためです。

## 独自のカバー関数を作成する

カバー関数は名前付き引数 `self` と、位置引数として隠す内容を受け取り、カバー済みの内容を返します。

```typst
#let my-cover(self: none, body) = block(hide(body))

config-methods(cover: my-cover)
```

### メソッドの動作を宣言する

Touyingは、メソッドが内容を*削除・非表示*にするのか、単に*装飾を変更*するのかを知る必要があります。これにより脚注の描画方法が変わるためです。

- 内容を削除するメソッドでは、本物の脚注を生成してはいけません。本文が非表示でも脚注だけが区切り線の下に現れるからです。代わりにTouyingは同じ幅を確保する仮のマーカーを描画します。
- 装飾だけを変更するメソッドでは、本物の脚注を生成し、ほかの内容とともに薄く表示する必要があります。

`utils.cover-kind-query` が渡されたときには次のように応答します。

```typst
#let my-cover(self: none, body) = if self == utils.cover-kind-query {
  "hide"
} else {
  block(hide(body))
}
```

内容を削除・非表示にするなら `"hide"`、装飾を変更するなら `"recolour"`、上から描画するなら `"paint"` を返します。

Touyingの組み込みメソッドはすべてこの問い合わせに応答します。応答しないメソッドは返り値から推測されますが、単純な `hide` は認識できても `block` や `place` で囲んだものは認識できないため、明示する価値があります。

### 図のキャプションをカバーする

`"recolour"` を返した場合、Touyingは `self` として `utils.cover-caption-query` を渡すことがあります。キャプションの `Figure 1:` などの補足文字列と番号はレイアウト時に生成されるため、渡された内容だけをカバーしても隠れません。一方、キャプション要素そのものをカバーすると独立したブロックになり、改行が生じます。代わりに `show figure.caption` ルールで受け取った内容を囲んで返してください。

```typst
#let my-cover(self: none, body) = if self == utils.cover-kind-query {
  "recolour"
} else if self == utils.cover-caption-query {
  show figure.caption: set text(fill: gray)
  body
} else {
  text(fill: gray, body)
}
```

非表示や上書き描画を行うメソッドにはこの問い合わせは行われません。それらはキャプション全体をほかの内容と同様に処理します。

### 脚注マーカーのカスタマイズ

`config-common(footnote-style: ..)` に、通常なら `show footnote: ..` に渡す関数を指定してください。Touyingはそれを `show footnote: footnote-style` として登録し、仮のマーカーにも適用するため、通常の脚注と見た目を統一できます。独自に書いた `show footnote: it => ..` ルールは、実際に表示される脚注にしか適用されず、仮のマーカーには適用されません。
