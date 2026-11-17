---
sidebar_position: 11
---

# 独自テーマを作成する

Touyingには多くの概念があるため、独自テーマの作成は少し複雑に感じるかもしれません。しかし、作成してみるとTouyingの柔軟性と高いカスタマイズ性を実感できるでしょう。[テーマのソースコード](https://github.com/touying-typ/touying/tree/main/themes)も参考になります。主な実装内容は次のとおりです。

- `xxx-theme` 関数のカスタマイズ
- `config-colors()` による配色のカスタマイズ
- ヘッダーのカスタマイズ
- フッターのカスタマイズ
- `slide` メソッドのカスタマイズ
- `title-slide` や `focus-slide` など特殊なスライドメソッドのカスタマイズ
- `notes` メソッドのカスタマイズ

ここでは、シンプルで美しいBambooテーマを段階的に作成しながら説明します。

## 既存テーマの変更

テーマを一から作る代わりに組み込みテーマをローカルで変更する場合は、次の手順を使います。

1. `themes` ディレクトリの[テーマのソースコード](https://github.com/touying-typ/touying/tree/main/themes)をローカルへコピーします。たとえば `themes/university.typ` を `university.typ` にコピーします。
2. `university.typ` の先頭の `#import "../src/exports.typ": *` を `#import "@preview/touying:0.8.0": *` に置き換えます。

これで、次のようにテーマをインポートして使用できます。

```typst
#import "@preview/touying:0.8.0": *
#import "university.typ": *

#show: university-theme.with(
  aspect-ratio: "16-9",
  config-info(
    title: [タイトル],
    subtitle: [サブタイトル],
    author: [著者],
    date: datetime.today(),
    institution: [所属],
    contact: [contact\@mail.com],
    logo: emoji.school,
  ),
)
```

## インポート

テーマを個人で使用するかTouying本体へ追加するかによって、インポート方法が異なります。

自分で使うだけなら、Touyingを直接インポートします。

```typst
#import "@preview/touying:0.8.0": *
```

Touyingの `themes` ディレクトリに入れて組み込みテーマにする場合は、前述のインポートを次のように変更します。

```typst
#import "../src/exports.typ": *
```

さらに、次の記述を追加します。

```typst
#import "bamboo.typ"
```

追加先はTouyingの `themes/themes.typ` です。

## register関数とinitメソッド

次に、テーマのテンプレートファイル `bamboo.typ` と、場合によっては省略される `main.typ` を分けて説明します。

通常、スライド作成の最初の段階では文字サイズとページの縦横比を決めるため、初期化メソッドを登録します。

```example
// bamboo.typ（Bambooテーマの定義）
#import "@preview/touying:0.8.0": *

#let bamboo-theme(
  aspect-ratio: "16-9",
  ..args,
  body,
) = {
  set text(size: 20pt)

  show: touying-slides.with(
    config-page(paper: "presentation-" + aspect-ratio),
    config-common(
      slide-fn: slide,
    ),
    ..args,
  )

  body
}

// main.typ（メインの文書）
<<< #import "@preview/touying:0.8.0": *
<<< #import "bamboo.typ": *

#show: bamboo-theme.with(aspect-ratio: "16-9")

= 最初のセクション

== 最初のスライド

タイトルと*重要な*情報を含むスライドです。
```

`bamboo-theme` 関数を作成し、`aspect-ratio` パラメーターでページの縦横比を設定しています。`set text(size: 20pt)` では文字サイズを指定します。`set par(justify: true)` などのグローバルなスタイルも追加できます。`self` が必要な場合は、`config-methods(init: (self: none, body) => { .. })` を使って `init` メソッドを登録する方法があります。

後の `main.typ` では `#show: bamboo-theme.with(aspect-ratio: "16-9")` によってスタイルを適用します。Bambooテーマ内部では `show: touying-slides.with()` を使って対応する設定を適用しています。

## 配色

よいスライドには適切な配色が重要です。Touyingにはテーマ間のAPIの違いを減らすため、共通の配色機構があります。色は2つの軸で分類されます。第1の軸は色の役割を表す `neutral`、`primary`、`secondary`、`tertiary` で、`primary` が最もよく使われるテーマの基本色です。第2の軸は明るさを表す `default`、`light`、`lighter`、`lightest`、`dark`、`darker`、`darkest` です。

Bambooテーマでは竹を連想させる `rgb("#5E8B65")` を `primary` に選び、背景色と文字色にそれぞれ `neutral-lightest` と `neutral-darkest` を設定します。

次の例のように `config-colors()` で配色を変更できます。これは実質的に `self.colors += (..)` をラップしたものです。

```typst
#let bamboo-theme(
  aspect-ratio: "16-9",
  ..args,
  body,
) = {
  set text(size: 20pt)

  show: touying-slides.with(
    config-page(paper: "presentation-" + aspect-ratio),
    config-common(
      slide-fn: slide,
    ),
    config-colors(
      primary: rgb("#5E8B65"),
      neutral-lightest: rgb("#ffffff"),
      neutral-darkest: rgb("#000000"),
    ),
    ..args,
  )

  body
}
```

配色を追加すると `self.colors.primary` のように参照できます。

また、ユーザーは `main.typ` で `config-colors()` を使い、いつでも配色を変更できます。あるいは次の方法もあります。

```typst
#show: touying-set-config.with(config-colors(
  primary: blue,
  neutral-lightest: rgb("#ffffff"),
  neutral-darkest: rgb("#000000"),
))
```

このように配色を自由に変更できることは、Touyingのカスタマイズ性の高さを示しています。

## 実践：独自のalertメソッド

通常、`#strong[..]` と同様に文字を強調する `#alert[..]` 関数を用意します。`#alert[..]` で文字色をテーマの基本色に変更する実装を目指します。

`register` 関数に次の1行を追加します。

```typst
config-methods(alert: (self: none, it) => text(fill: self.colors.primary, it))
```

このコードは文字色を `self.colors.primary` に変更します。`self` は `self: none` パラメーターで渡されるため、現在の `primary` 色を取得できます。

短い記法も使用できます。

```typst
config-methods(alert: utils.alert-with-primary-color)
```

## 独自のヘッダーとフッター

ここからは[ページレイアウト](layout.md)の内容を前提として、スライドにヘッダーとフッターを追加します。

最初に `config-store(title: none)` を追加し、現在のスライドのタイトルを `self.store.title` に保存します。これによりヘッダーでの使用や後からの変更が容易になります。同様に `config-store(footer: footer)` を作り、`bamboo-theme` 関数の `footer: none` パラメーターを保存して左下のフッターに表示します。

ヘッダーは単純な内容ではなく、`let header(self) = { .. }` のように `self` を受け取る内容関数です。そのため最新の `self.store.title` などを参照できます。フッターも同様です。

ここで使う `components.cell` は `#let cell = block.with(width: 100%, height: 100%, above: 0pt, below: 0pt, breakable: false)` に相当します。`show: components.cell` は `components.cell(body)` の短縮形で、フッターの `show: pad.with(.4em)` も同様です。

`utils` モジュールにはカウンターや状態を扱う関数が多数あります。現在のセクションには `utils.display-current-heading(level: 1)`、現在のスライド番号と総数には `context utils.slide-counter.display() + " / " + utils.last-slide-number` を使用できます。

`self.store.footer` の表示には `utils.call-or-display(self, self.store.footer)` を使用しています。これは `self.store.footer = self => {..}` のような内容関数にも対応し、通常の内容と関数の表示方法を統一するためです。

ヘッダーやフッターを正しく配置し、本文との間隔を確保するには `config-page(margin: (top: 4em, bottom: 1.5em, x: 2em))` などでマージンを設定します。

`slide` メソッドもカスタマイズします。`#let slide(title: auto, ..args) = touying-slide-wrapper(self => {..})` のコールバック内の `self` は最新の設定を取得する必須引数です。`title` はヘッダー表示用の `self.store.title` を更新します。`..args` は残りの引数をまとめ、`touying-slide(self: self, ..args)` に渡します。これらはTouyingの `slide` 機能を正常に動作させるために必要です。また、`bamboo-theme` 内で `config-common(slide-fn: slide)` として登録します。

`slide-fn` は `config-methods` ではなく `config-common` のキーです。`config-methods` は未知のキーを `..args` で `self.methods` に集めるため、`config-methods(slide-fn: slide)` は警告やエラーなしでコンパイルできますが、設定は有効にならずヘッダーやフッターが消えてしまいます。後述の `new-section-slide-fn` や `notes-fn` などの `*-fn` キーも同様です。

```example
// bamboo.typ（Bambooテーマの定義）
#import "@preview/touying:0.8.0": *

#let slide(title: auto, ..args) = touying-slide-wrapper(self => {
  if title != auto {
    self.store.title = title
  }
  // ページを設定
  let header(self) = {
    set align(top)
    show: components.cell.with(fill: self.colors.primary, inset: 1em)
    set align(horizon)
    set text(fill: self.colors.neutral-lightest, size: .7em)
    utils.display-current-heading(level: 1)
    linebreak()
    set text(size: 1.5em)
    if self.store.title != none {
      utils.call-or-display(self, self.store.title)
    } else {
      utils.display-current-heading(level: 2)
    }
  }
  let footer(self) = {
    set align(bottom)
    show: pad.with(.4em)
    set text(fill: self.colors.neutral-darkest, size: .8em)
    utils.call-or-display(self, self.store.footer)
    h(1fr)
    context utils.slide-counter.display() + " / " + utils.last-slide-number
  }
  self = utils.merge-dicts(
    self,
    config-page(
      header: header,
      footer: footer,
    ),
  )
  touying-slide(self: self, ..args)
})

#let bamboo-theme(
  aspect-ratio: "16-9",
  footer: none,
  ..args,
  body,
) = {
  set text(size: 20pt)

  show: touying-slides.with(
    config-page(
      paper: "presentation-" + aspect-ratio,
      margin: (top: 4em, bottom: 1.5em, x: 2em),
    ),
    config-common(
      slide-fn: slide,
    ),
    config-methods(
      alert: utils.alert-with-primary-color,
    ),
    config-colors(
      primary: rgb("#5E8B65"),
      neutral-lightest: rgb("#ffffff"),
      neutral-darkest: rgb("#000000"),
    ),
    config-store(
      title: none,
      footer: footer,
    ),
    ..args,
  )

  body
}


// main.typ（メインの文書）
<<< #import "@preview/touying:0.8.0": *
<<< #import "bamboo.typ": *

#show: bamboo-theme.with(aspect-ratio: "16-9")

= 最初のセクション

== 最初のスライド

タイトルと*重要な*情報を含むスライドです。
```

## 特殊なスライドのカスタマイズ

作成した基本的なスライドを基に、`title-slide` や `focus-slide` などの特殊なスライド関数や独自のメソッドを追加します。

`title-slide` では `let info = self.info + args.named()` により保存済みの情報を取得し、名前付き引数で上書きできます。以後は `info.title` のように参照できます。具体的な本文 `body` の構成はテーマによって異なるため、ここでは詳述しません。

`new-section-slide` も同様です。ただし `new-section-slide-fn: new-section-slide` は `config-methods()` ではなく `config-common()` に登録します。これにより第1レベルの見出しで自動的に呼ばれます。Touyingは通常、本文として `none` を渡しますが、`config-common(receive-body-for-new-section-slide-fn: true)` を指定したテーマではセクションの内容が渡されます。そのため、引数をそのまま `touying-slide` に渡すと便利です。

特殊なスライドでは中央揃え、余白、文字サイズなどページ全体の装飾が必要です。`touying-slide(self: self, align(horizon + center, body))` のように本文だけを囲みたくなりますが、これでは渡された本文だけが対象になります。組み込みテーマと同様に `touying-slide` の `setting` 引数を使ってください。`setting` は `#pause` の解決とcomposerによる配置が終わった後の、前処理を含む*合成済みの*サブスライドを受け取り、実際にページへ配置する内容を返します。本文だけを囲むと `subslide-preamble` が揃えの対象外になりますが、`setting: align.with(horizon + center)` ならスライド全体を揃えられます。また通常の関数なので、スライド全体に適用するsetルールやshowルールを置く場所としても適しています。

```typst
#let new-section-slide(body) = touying-slide-wrapper(self => {
  let setting(body) = {
    set align(center + horizon)
    set text(size: 2em, fill: self.colors.primary, weight: "bold", style: "italic")
    utils.display-current-heading(level: 1)
    body
  }
  touying-slide(self: self, setting: setting, body)
})
```

```example
// bamboo.typ（Bambooテーマの定義）
#import "@preview/touying:0.8.0": *

#let slide(title: auto, ..args) = touying-slide-wrapper(self => {
  if title != auto {
    self.store.title = title
  }
  // ページを設定
  let header(self) = {
    set align(top)
    show: components.cell.with(fill: self.colors.primary, inset: 1em)
    set align(horizon)
    set text(fill: self.colors.neutral-lightest, size: .7em)
    utils.display-current-heading(level: 1)
    linebreak()
    set text(size: 1.5em)
    if self.store.title != none {
      utils.call-or-display(self, self.store.title)
    } else {
      utils.display-current-heading(level: 2)
    }
  }
  let footer(self) = {
    set align(bottom)
    show: pad.with(.4em)
    set text(fill: self.colors.neutral-darkest, size: .8em)
    utils.call-or-display(self, self.store.footer)
    h(1fr)
    context utils.slide-counter.display() + " / " + utils.last-slide-number
  }
  self = utils.merge-dicts(
    self,
    config-page(
      header: header,
      footer: footer,
    ),
  )
  touying-slide(self: self, ..args)
})

#let title-slide(..args) = touying-slide-wrapper(self => {
  let info = self.info + args.named()
  let body = {
    block(
      fill: self.colors.primary,
      width: 80%,
      inset: (y: 1em),
      radius: 1em,
      text(size: 2em, fill: self.colors.neutral-lightest, weight: "bold", info.title),
    )
    set text(fill: self.colors.neutral-darkest)
    if info.author != none {
      block(info.author)
    }
    if info.date != none {
      block(utils.display-info-date(self))
    }
    if info.contact != none {
      block(info.contact)
    }
  }
  touying-slide(self: self, setting: align.with(center + horizon), body)
})

#let new-section-slide(body) = touying-slide-wrapper(self => {
  let setting(body) = {
    set align(center + horizon)
    set text(size: 2em, fill: self.colors.primary, weight: "bold", style: "italic")
    utils.display-current-heading(level: 1)
    body
  }
  touying-slide(self: self, setting: setting, body)
})

#let focus-slide(body) = touying-slide-wrapper(self => {
  self = utils.merge-dicts(
    self,
    config-page(
      fill: self.colors.primary,
      margin: 2em,
    ),
  )
  set text(fill: self.colors.neutral-lightest, size: 2em)
  touying-slide(self: self, setting: align.with(horizon + center), {
    place(hide(heading[強調スライド])) // スライドに不可視の見出しを置く。後から参照できる
    body
  })
})

#let bamboo-theme(
  aspect-ratio: "16-9",
  footer: none,
  ..args,
  body,
) = {
  set text(size: 20pt)

  show: touying-slides.with(
    config-page(
      paper: "presentation-" + aspect-ratio,
      margin: (top: 4em, bottom: 1.5em, x: 2em),
    ),
    config-common(
      slide-fn: slide,
      new-section-slide-fn: new-section-slide,
    ),
    config-methods(alert: utils.alert-with-primary-color),
    config-colors(
      primary: rgb("#5E8B65"),
      neutral-lightest: rgb("#ffffff"),
      neutral-darkest: rgb("#000000"),
    ),
    config-store(
      title: none,
      footer: footer,
    ),
    ..args,
  )

  body
}


// main.typ（メインの文書）
<<< #import "@preview/touying:0.8.0": *
<<< #import "bamboo.typ": *

#show: bamboo-theme.with(
  aspect-ratio: "16-9",
  footer: self => self.info.institution,
  config-info(
    title: [タイトル],
    subtitle: [サブタイトル],
    author: [著者],
    date: datetime.today(),
    institution: [所属],
    contact: [contact\@mail.com],
  ),
)

#title-slide()

= 最初のセクション

== 最初のスライド

タイトルと*重要な*情報を含むスライドです。

#focus-slide[
  Focus on it!
]
```



## 発表者ノートのカスタマイズ

最後に、発表者ノートをカスタマイズします。
Touying 0.8.0以降は `notes` 関数を定義し、`config-common(notes-fn: notes)` に渡すことでノートの見た目を指定できます。

独自の `notes` 関数は `touying-notes` を基にしてください。スライドと同様に設定関数を定義でき、ヘッダーも変更できます。
`notes` 関数には `note-setting` と `preview-setting` という2つの設定関数があります。
前者はノートの本文を加工します。後者はLaTeX Beamerの類似機能と同じく、[`show-only-notes` モード](https://touying-typ.github.io/docs/reference/configs/config-common#show-only-notes)に表示するプレビュースライドの配置や大きさを制御します。どちらも適切な既定値があり、省略できます。
ノートの仕組みと出力方法は[発表者ノート](./speaker-notes.md)を参照してください。
ページの背景などを設定する際は `config-page()` を使用せず、色付きの `rect` や `image` を直接 `fill` に渡します。`header-fill` も同様です。
`header` はノートページの上部を横幅いっぱいに占めます。既定では現在のセクション見出しを表示します。

```example
// bamboo.typ（Bambooテーマの定義）
#import "@preview/touying:0.8.0": *

#let slide(title: auto, ..args) = touying-slide-wrapper(self => {
  if title != auto {
    self.store.title = title
  }
  // ページを設定
  let header(self) = {
    set align(top)
    show: components.cell.with(fill: self.colors.primary, inset: 1em)
    set align(horizon)
    set text(fill: self.colors.neutral-lightest, size: .7em)
    utils.display-current-heading(level: 1)
    linebreak()
    set text(size: 1.5em)
    if self.store.title != none {
      utils.call-or-display(self, self.store.title)
    } else {
      utils.display-current-heading(level: 2)
    }
  }
  let footer(self) = {
    set align(bottom)
    show: pad.with(.4em)
    set text(fill: self.colors.neutral-darkest, size: .8em)
    utils.call-or-display(self, self.store.footer)
    h(1fr)
    context utils.slide-counter.display() + " / " + utils.last-slide-number
  }
  self = utils.merge-dicts(
    self,
    config-page(
      header: header,
      footer: footer,
    ),
  )
  touying-slide(self: self, ..args)
})

#let title-slide(..args) = touying-slide-wrapper(self => {
  let info = self.info + args.named()
  let body = {
    block(
      fill: self.colors.primary,
      width: 80%,
      inset: (y: 1em),
      radius: 1em,
      text(size: 2em, fill: self.colors.neutral-lightest, weight: "bold", info.title),
    )
    set text(fill: self.colors.neutral-darkest)
    if info.author != none {
      block(info.author)
    }
    if info.date != none {
      block(utils.display-info-date(self))
    }
    if info.contact != none {
      block(info.contact)
    }
  }
  touying-slide(self: self, setting: align.with(center + horizon), body)
})

#let new-section-slide(body) = touying-slide-wrapper(self => {
  let setting(body) = {
    set align(center + horizon)
    set text(size: 2em, fill: self.colors.primary, weight: "bold", style: "italic")
    utils.display-current-heading(level: 1)
    body
  }
  touying-slide(self: self, setting: setting, body)
})

#let focus-slide(body) = touying-slide-wrapper(self => {
  self = utils.merge-dicts(
    self,
    config-page(
      fill: self.colors.primary,
      margin: 2em,
    ),
  )
  set text(fill: self.colors.neutral-lightest, size: 2em)
  touying-slide(self: self, setting: align.with(horizon + center), {
    place(hide(heading[強調スライド])) // スライドに不可視の見出しを置く。後から参照できる
    body
  })
})

#let notes(self: none, ..args) = touying-notes(
  self: self,
  // スライドのヘッダーに合わせ、基本色の帯と明るい文字を使う
  header: self => pad(1em, text(
    fill: self.colors.neutral-lightest,
    size: .7em,
    utils.display-current-heading(depth: self.slide-level),
  )),
  header-fill: self.colors.primary,
  fill: self.colors.neutral-lightest,
  note-setting: note => pad(1.5em, text(size: .8em, note)),
  preview-setting: slide-preview => align(top+right, scale(x:20%, y:20%, slide-preview)),
  ..args,
)

#let bamboo-theme(
  aspect-ratio: "16-9",
  footer: none,
  ..args,
  body,
) = {
  set text(size: 20pt)

  show: touying-slides.with(
    config-page(
      paper: "presentation-" + aspect-ratio,
      margin: (top: 4em, bottom: 1.5em, x: 2em),
    ),
    config-common(
      slide-fn: slide,
      new-section-slide-fn: new-section-slide,
      notes-fn: notes,
    ),
    config-methods(alert: utils.alert-with-primary-color),
    config-colors(
      primary: rgb("#5E8B65"),
      neutral-lightest: rgb("#ffffff"),
      neutral-darkest: rgb("#000000"),
    ),
    config-store(
      title: none,
      footer: footer,
    ),
    ..args,
  )

  body
}


// main.typ（メインの文書）
<<< #import "@preview/touying:0.8.0": *
<<< #import "bamboo.typ": *

#show: bamboo-theme.with(
  aspect-ratio: "16-9",
  footer: self => self.info.institution,
  config-common(show-notes-on-second-screen: right),
  config-info(
    title: [タイトル],
    subtitle: [サブタイトル],
    author: [著者],
    date: datetime.today(),
    institution: [所属],
    contact: [contact\@mail.com],
  ),
)

#title-slide()

= 最初のセクション

== 最初のスライド

タイトルと*重要な*情報を含むスライドです。

#speaker-note[
  Remember to explain why bamboo is the fastest-growing plant on earth.
]

#focus-slide[
  Focus on it!
]
```


## まとめ

これでシンプルで洗練されたテーマを作成できました。Touyingは簡潔さより機能性を重視しているため、最初は概念が多く難しく感じるかもしれません。しかし、包括的で統一された設計によってテーマ間の共通点を把握しやすく、学んだ知識をほかのテーマにも応用できます。グローバル変数の保存、既存テーマの変更、テーマの切り替えなども容易であり、Touyingの疎結合な設計の利点を活用できます。