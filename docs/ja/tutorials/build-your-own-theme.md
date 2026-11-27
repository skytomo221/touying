---
sidebar_position: 11
---

# 独自テーマを作成する

これまでに紹介した概念が多いため、Touyingで独自テーマを作成するのは少し複雑かもしれません。しかし、安心してください。実際にTouyingでテーマを作成すると、その利便性と高いカスタマイズ性を深く実感できるでしょう。[テーマのソースコード](https://github.com/touying-typ/touying/tree/main/themes)を参考にできます。主に実装すべきものは次のとおりです。

- `xxx-theme` 関数のカスタマイズ
- カラーテーマ、すなわち `config-colors()` のカスタマイズ
- ヘッダーのカスタマイズ
- フッターのカスタマイズ
- `slide` メソッドのカスタマイズ
- `title-slide` や `focus-slide` メソッドなど、特殊なスライドメソッドのカスタマイズ
- `notes` メソッドのカスタマイズ

Touyingでのテーマの作成方法を示すため、シンプルで美しいBambooテーマを段階的に作成しましょう。

## 既存テーマの変更

テーマを一から作る代わりにTouyingの組み込みテーマをローカルで変更したい場合は、次の手順で実現できます。

1. [テーマのソースコード](https://github.com/touying-typ/touying/tree/main/themes)を `themes` ディレクトリからローカルへコピーします。たとえば `themes/university.typ` をローカルの `university.typ` にコピーします。
2. `#import "../src/exports.typ": *` という `university.typ` ファイル先頭のコマンドを `#import "@preview/touying:0.8.0": *` に置き換えます。

すると、次のようにテーマをインポートして使用できます。

```typst
#import "@preview/touying:0.8.0": *
#import "university.typ": *

#show: university-theme.with(
  aspect-ratio: "16-9",
  config-info(
    title: [Title],
    subtitle: [Subtitle],
    author: [Authors],
    date: datetime.today(),
    institution: [Institution],
    contact: [contact\@mail.com],
    logo: emoji.school,
  ),
)
```

## インポート

テーマが独自のものかTouyingの一部かによって、インポート方法は2通りあります。

自分で使うだけなら、Touyingを直接インポートできます。

```typst
#import "@preview/touying:0.8.0": *
```

Touyingの一部として `themes` ディレクトリに配置したい場合は、上のインポート文を次のように変更します。

```typst
#import "../src/exports.typ": *
```

そして次の記述を

```typst
#import "bamboo.typ"
```

Touyingの `themes/themes.typ` に追加します。

## register関数とinitメソッド

以降では、テンプレートファイル `bamboo.typ` と、場合によっては省略する `main.typ` ファイルを区別します。

一般に、スライド作成の第一歩はフォントサイズとページの縦横比を決めることなので、初期化メソッドを登録する必要があります。

```example
// bamboo.typ
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

// main.typ
<<< #import "@preview/touying:0.8.0": *
<<< #import "bamboo.typ": *

#show: bamboo-theme.with(aspect-ratio: "16-9")

= First Section

== First Slide

A slide with a title and an *important* information.
```

ご覧のとおり、`bamboo-theme` 関数を作成し、ページの縦横比を設定するための `aspect-ratio` パラメーターを渡しています。また、フォントサイズを指定するために `set text(size: 20pt)` を追加しました。ここには `set par(justify: true)` などの追加のグローバルスタイル設定も配置できます。`self` を使用する必要があれば、`config-methods(init: (self: none, body) => { .. })` で `init` メソッドを登録することも検討できます。

ご覧のとおり、後の `main.typ` では `#show: bamboo-theme.with(aspect-ratio: "16-9")` によりスタイル設定を適用し、内部では `bamboo` が `show: touying-slides.with()` によって対応する設定を適用しています。

## 配色

スライドに美しいカラーテーマを選ぶことは、よいスライドを作るうえで重要です。Touyingは異なるテーマ間のAPIの違いを最小限に抑えるため、カラーテーマのサポートを組み込んでいます。Touyingは色を選択するための2つの軸を提供します。第1の軸は色調を区別する `neutral`、`primary`、`secondary`、`tertiary` で、`primary` が最もよく使われるテーマ色です。第2の軸は明るさの段階を区別する `default`、`light`、`lighter`、`lightest`、`dark`、`darker`、`darkest` です。

Bambooテーマを作成しているので、`primary` テーマ色として竹に近い色 `rgb("#5E8B65")` を選び、背景色とフォント色としてそれぞれ中立色の `neutral-lightest`、`neutral-darkest` を追加しました。

次のコードのように、`config-colors()` メソッドでカラーテーマを変更できます。実質的には `self.colors += (..)` のラッパーです。

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

上のようにカラーテーマを追加すると、その色には `self.colors.primary` でアクセスできます。

また、ユーザーが `main.typ` でいつでも `config-colors()` を使うか、次の方法でカラーテーマを変更できる点も注目に値します。

```typst
#show: touying-set-config.with(config-colors(
  primary: blue,
  neutral-lightest: rgb("#ffffff"),
  neutral-darkest: rgb("#000000"),
))
```

カラーテーマをいつでも変更できるこの機能は、Touyingの高いカスタマイズ性を示しています。

## 実践：独自のalertメソッド

一般に、ユーザー向けに `#alert[..]` 関数を提供する必要があります。これは `#strong[..]` に似ており、どちらも現在のテキストを強調するために使います。通常、`#alert[..]` はテキストの色をテーマ色に変更し、より美しく見せます。これが次の目標です。

`register` 関数に1行追加します。

```typst
config-methods(alert: (self: none, it) => text(fill: self.colors.primary, it))
```

このコードはテキストの色を `self.colors.primary` に変更するという意味です。ここでの `self` は `self: none` パラメーターを通じて渡されるので、`primary` テーマ色をリアルタイムで取得できます。

短縮記法も使えます。

```typst
config-methods(alert: utils.alert-with-primary-color)
```

## 独自のヘッダーとフッター

ここではページレイアウトの節を読んだものとします。したがって、スライドにヘッダーとフッターを追加すべきことは分かっているでしょう。

最初に `config-store(title: none)` を追加します。これは現在のスライドのタイトルをメンバー変数 `self.store.title` として `self` 内に保存するという意味で、ヘッダーで使用したり後から変更したりするのに便利です。同様に `config-store(footer: footer)` も作成し、`footer: none` という `bamboo-theme` 関数のパラメーターを保存して、左下のフッターに表示します。

次に注目すべきなのは、ヘッダーが単純な内容ではなく、`self` をパラメーターとする内容関数、たとえば `let header(self) = { .. }` であることです。これにより最新の `self` から `self.store.title` などの必要な情報を取得できます。フッターも同様です。

ここで使用する `components.cell` は実際には `#let cell = block.with(width: 100%, height: 100%, above: 0pt, below: 0pt, breakable: false)` です。`show: components.cell` は `components.cell(body)` の短縮形であり、フッターの `show: pad.with(.4em)` も同じです。

もう1つ注目すべきなのは、`utils` モジュールにカウンターや状態に関連する内容やメソッドが多数あることです。たとえば `utils.display-current-heading(level: 1)` は現在の `section` を表示するためのもので、`context utils.slide-counter.display() + " / " + utils.last-slide-number` は現在のページ番号と総ページ数を表示するためのものです。

また、`utils.call-or-display(self, self.store.footer)` のような構文で `self.store.footer` を表示しています。これは `self.store.footer = self => {..}` のような場合に対応し、内容関数と通常の内容の表示を統一するためです。

ヘッダーとフッターが正しく表示され、本文から十分な間隔が確保されるように、`config-page(margin: (top: 4em, bottom: 1.5em, x: 2em))` などでマージンを設定する必要があります。

また、`slide` メソッドをカスタマイズする必要があります。`#let slide(title: auto, ..args) = touying-slide-wrapper(self => {..})` とすると、コールバック関数内の `self` は最新の `self` を取得するための必須パラメーター、第2の `title` はヘッダーに表示する `self.store.title` の更新用、第3の `..args` は残りのパラメーターを集めて `touying-slide(self: self, ..args)` に渡すためのもので、これもTouyingの `slide` 機能が正常に動作するために必要です。さらに、下のコードのとおり、`bamboo-theme` 関数で `config-common(slide-fn: slide)` を使用してこのメソッドを登録する必要があります。

`slide-fn` は `config-common` のキーであって `config-methods` のキーではありません。この違いは見落としがちです。`config-methods` は未知のキーをすべて `self.methods` に `..args` 経由で集めるため、`config-methods(slide-fn: slide)` はエラーも警告もなくコンパイルされるものの、設定はまったく有効にならず、ヘッダーとフッターが何も言わずに消えてしまいます。下で使用する他の `*-fn` キー、たとえば `new-section-slide-fn` や `notes-fn` についても同じです。

```example
// bamboo.typ
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


// main.typ
<<< #import "@preview/touying:0.8.0": *
<<< #import "bamboo.typ": *

#show: bamboo-theme.with(aspect-ratio: "16-9")

= First Section

== First Slide

A slide with a title and an *important* information.
```

## 特殊なスライドのカスタマイズ

作成した基本的なスライドを基に、`title-slide`、`focus-slide`、独自の `slides` メソッドなど、特殊なスライド関数をさらに追加します。

`title-slide` メソッドでは、まず `self.info` に保存された情報を `let info = self.info + args.named()` によって取得できます。また、関数のパラメーターを通じて渡された `args.named()` で情報を更新し、後で `info.title` の形で使用できます。ページの具体的な内容 `body` はテーマごとに異なるので、ここではあまり詳しく説明しません。

`new-section-slide` メソッドも同じですが、注意すべき点は `new-section-slide-fn: new-section-slide` を `config-common()` で登録したことです。繰り返しますが、キーを取り込んでしまう `config-methods()` ではありません。これにより、第1レベルの見出しに遭遇すると `new-section-slide` が自動的に呼ばれます。Touyingは本文に `none` を渡して呼び出しますが、`config-common(receive-body-for-new-section-slide-fn: true)` を設定するテーマでは代わりにセクションの内容を受け取るため、引数をそのまま `touying-slide` に渡すことには意味があります。

特殊なスライドでは通常、ページ全体を中央揃えにしたり、余白を付けたり、文字を拡大したりしたいものです。`touying-slide(self: self, align(horizon + center, body))` のように本文を囲みたくなりますが、それでは渡された1つの本文だけが対象になります。組み込みテーマがすべて使用している `touying-slide` の `setting` 引数を使う方が望ましいです。`setting` は*合成済みの*サブスライド、つまりサブスライドの前処理にすべての本文が続き、`#pause` の解決とcomposerによる配置が済んだものを受け取り、実際にページに置かれる内容を返す関数です。本文だけを囲むと `subslide-preamble` は配置の対象外となり、本文だけが中央に配置されます。`setting: align.with(horizon + center)` ならスライド全体をまとめて移動します。また、`setting` は通常の関数なので、スライド全体に適用する `set` ルールや `show` ルールを置くのに自然な場所です。

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
// bamboo.typ
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
    place(hide(heading[Focus Slide])) // スライドに不可視の見出しを配置する。後で役立つ
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


// main.typ
<<< #import "@preview/touying:0.8.0": *
<<< #import "bamboo.typ": *

#show: bamboo-theme.with(
  aspect-ratio: "16-9",
  footer: self => self.info.institution,
  config-info(
    title: [Title],
    subtitle: [Subtitle],
    author: [Authors],
    date: datetime.today(),
    institution: [Institution],
    contact: [contact\@mail.com],
  ),
)

#title-slide()

= First Section

== First Slide

A slide with a title and an *important* information.

#focus-slide[
  Focus on it!
]
```



## 発表者ノートのカスタマイズ

最後の要素は発表者ノートのカスタマイズです。
Touying 0.8.0以降は、`notes` 関数を記述し、`config-common(notes-fn: notes)` に渡すことで、その外観を細かく指定できます。

独自の `notes` 関数は `touying-notes` を基にする必要があります。これはslideと同様に設定関数を定義でき、カスタマイズ可能なヘッダーも備えています。
`notes` 関数には `note-setting` と `preview-setting` の2つの設定関数があります。
第1の関数はノート、すなわち主な内容を変換します。第2の関数はLaTeX Beamerのオプションと同様に、[`show-only-notes` モード](https://touying-typ.github.io/docs/reference/configs/config-common#show-only-notes)で描画される「プレビュースライド」の配置と拡大縮小に使用します。どちらにも適切なデフォルト値があるので、いずれも省略できます。
ノートとは何か、Touyingがどのように出力できるかについては[発表者ノート](./speaker-notes.md)のページを参照してください。
ページの背景などを設定するには `config-page()` を使わず、代わりに色付きの `rect` や `image` を直接 `fill` フィールドに渡せます。`header-fill` パラメーターも同様です。
`header` はノートページの上部を横幅いっぱいに占めます。デフォルトでは現在のセクションの見出しを描画します。

```example
// bamboo.typ
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
    place(hide(heading[Focus Slide])) // スライドに不可視の見出しを配置する。後で役立つ
    body
  })
})

#let notes(self: none, ..args) = touying-notes(
  self: self,
  // スライドのヘッダーに合わせる。同じ基本色の帯と明るい文字。
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


// main.typ
<<< #import "@preview/touying:0.8.0": *
<<< #import "bamboo.typ": *

#show: bamboo-theme.with(
  aspect-ratio: "16-9",
  footer: self => self.info.institution,
  config-common(show-notes-on-second-screen: right),
  config-info(
    title: [Title],
    subtitle: [Subtitle],
    author: [Authors],
    date: datetime.today(),
    institution: [Institution],
    contact: [contact\@mail.com],
  ),
)

#title-slide()

= First Section

== First Slide

A slide with a title and an *important* information.

#speaker-note[
  Remember to explain why bamboo is the fastest-growing plant on earth.
]

#focus-slide[
  Focus on it!
]
```


## まとめ

おめでとうございます！ シンプルで美しいテーマを作成できました。Touyingには多くの概念が導入されているため、最初は理解しづらいと感じるかもしれません。これは正常なことです。Touyingは簡潔さよりも機能性を選択しているからです。しかし、Touyingの包括的で統一されたアプローチのおかげで、異なるテーマ間の共通点を簡単に抽出し、知識をそのまま応用できます。グローバル変数を保存したり、既存テーマを変更したり、テーマを切り替えたりすることも容易であり、Touyingの疎結合の利点が表れています。