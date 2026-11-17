---
sidebar_position: 8
---

# 変更履歴

> このページの日本語訳は作業中です。v0.8.1の説明と各バージョンの見出しは翻訳済みですが、以前のリリースには英語の説明が残っています。

## v0.8.1

0.8.0で導入された動作の一部を調整し、不具合を修正したパッチリリースです。

### 動作の変更

- **インラインの内容に挟まれたダッシュではスライドが分割されなくなりました**

  `Text — `code`` のように左右にインライン要素を持つ `—` や `---` は、文章の一部として扱われます。以前は水平線と解釈され、新しいスライドが始まってダッシュが消えていました。インライン要素にはテキスト、`~` のような記号、raw、数式、強調、リンク、参照、引用、脚注、ボックスなどが含まれます。空行、見出し、別のブロック、`#pause` や `#alert[..]` のようなTouyingのマーカー・ラッパーの隣にあるダッシュは、引き続きスライドを分割します。

  記事モードにも同じ規則が適用され、文章中のダッシュは保持され、水平線と解釈されるダッシュは削除されます。

  **移行方法：** 改ページさせる場合は `---` の周囲に空行または見出しを配置してください。

- **単独のウェイポイントラベルは3つの場面で最後のサブスライドを選択します**

  `handout-subslides` で単独のラベルを指定すると、そのウェイポイントの最後のサブスライドが残ります。ラベル付き内容に対する `touying-recall` のラベルも同様です。`measure` はウェイポイント範囲の最大サイズを採用します。範囲マーカー `from-wp`、`until-wp`、`not-wp` は対象範囲の全サブスライドを保持するため、`not-wp(not-wp(<a>))` は `<a>` の範囲そのものになります。`handout-subslides: "waypoints"` は各ウェイポイントの最後のサブスライドを選びます。

  **移行方法：** 最後以外のサブスライドが必要な場合は `from-wp`、`until-wp`、`not-wp` などの範囲マーカーを使用してください。

- **脚注番号はページ単位ではなくスライド単位で振り直されます**

  `reset-footnote-number-per-slide` はスライドの先頭でのみ番号をリセットするようになり、`counter(footnote)` は既定で固定されるカウンターに含まれます。このため各サブスライドで脚注番号が同一になります。以前はページごとにリセットされ、改ページ可能なスライドが次のページにはみ出すと番号が1から始まりました。また `reset-footnote-number-per-slide: false` の場合、サブスライドを進むごとに番号が増えていました。現在ははみ出したページでも番号が継続し、`false` ならスライド間で番号が続きつつ、同じスライド内のサブスライドでは一致します。

- **`touying-reduce` がalchemist 0.2.0に対応しました**

  パッケージ独自の `touying-reducer-bindings` は、`reduce` と `cover` が両方とも関数の場合に使われます。それ以外では、Touyingがパッケージの `name` に対応して保持するバインディングを使います。

- **`slide-preamble` と `default-slide-preamble` が再び実行されるようになりました**

  これらは2026年3月以降、通知なく実行されなくなっていましたが、再び呼び出されます。現在のスライドでは文書の先頭で `preamble`、続いて0.8.0と同じくスライド前に収集された遅延設定変更の内容、`slide-preamble`、サブスライド用のpreambleの順に実行されます。これらはいずれもヘッダー作成後、スライド本文の配置前に実行されます。以前と同様、`page-preamble` だけはヘッダーより前に実行されます。

### 修正
- 最初の見出し、`#slide`、改ページの前にある `touying-set-config` の本文が、失われたり誤ったスライドへ結合されたりする問題を修正しました。
- スライド呼び出しの直後にあるラベル（`#slide[..]<name>`）が、見出しラベルと同様にスライドに付くようになりました。`#link(<name>)` は最初のサブスライドに移動し、プレゼンテーション・配布資料・記事の各モードで `<name.waypoint>` というウェイポイントアンカーが利用できます。また、記事モードではウェイポイントが最後のサブスライドで終わらなくてもアンカーが失われなくなりました。
- スライド全体を再利用しても元のラベルが再出力されなくなりました。各コピーには既定で `<og-label-1>`、`<og-label-2>`、`<og-label-1.waypoint>` などの新しいラベルが付きます。`#touying-recall(<intro>)<intro-again>` のように新しいラベルも指定できます。図など再利用した内容にも適用され、特定のサブスライドだけを再利用した図への参照も可能です。新しいラベルを指定しなければ、ラベルなしで内容が出力されます。
- スライド呼び出しの外側にある `#heading[..]` が `field "depth" in heading is not known at this point` で失敗する問題を修正しました（#434、#437）。
- `#pause` で隠されたキャプションのない `#figure` が `field "caption" in figure is not known at this point` で失敗する問題を修正しました（#437）。
- `animate`、`animate-hidden`、`animate-removed`、`item-by-item-fn` が、`uncover` や `only` と同様に `self.methods` と `utils.methods(self)` の既定メソッドに含まれるようになりました。
- `capitalize` と `titlecase` が非ASCII文字列にも対応しました（#436）。
- reducerが内容の最小反復回数と関数ラッパーを通知するようになりました（#431）。
- pauseされたリスト項目が表示されても、整列済みの内容がずれなくなりました（#442）。
- リストの `measure` が行間隔を適用するようになり、show-setルールが有効になりました（#443）。
- `#pause` を含む番号付きリストで番号が揃うようになりました。非表示部分と表示部分の番号列の幅が一致し、後から項目を表示しても横方向にずれません（#438）。
- `touying-mitex` が非表示の改行部分の高さを保持するようになり、次の行を表示しても既存の行が横方向へずれなくなりました。
- 先行する `#pause` の後、リスト項目間にある `#meanwhile` がリストを分割しなくなりました。次のサブスライドを表示しても行が移動しません（#439）。
- リスト、番号付きリスト、用語一覧の一部を隠しても、コンテナーが分割されなくなりました。`#meanwhile` の後の項目や空行後に隠された番号付き項目は、0.7.0以前と同様に番号、番号列、密度、行間隔を維持します。`#pause` で分割された番号付きリストも `reversed`、`start`、明示的な番号を維持し、`show enum: set enum(numbering: ..)` や `number-align` ルールが各部分に適用されます。用語一覧の可視の `separator` が非表示部分の確保間隔を拡大する問題も修正しました。
- `utils.color-changing-cover` で隠された部分に `state.update` や `counter.step` が含まれても、後続の内容がずれなくなりました。
- 最初のスライドの前に `touying-set-config` があっても、文書の `preamble` が失われなくなりました。最初の見出しより前に関数形式の設定がスライドを生成する場合も、最初に作られたスライドで一度だけ実行されます。スライド内容の後に置いた設定の本文内にある `#pagebreak()` は、設定がない場合と同様に新しいスライドを開始します。
- ctheorems 2.xの状態がサブスライド間で固定されるようになりました（#452、#454）。ctheorems 1.xのサポートは非推奨となり、v0.9.0で削除される予定です。
- `until-wp` と `from-wp` のドキュメントを修正し、`until-wp` がウェイポイントの最初のサブスライドの手前まで、`from-wp` がその位置を含んで末尾までを対象とすることを明記しました。
- APIリファレンスに `src/core/*.typ` と `touying-slides` の関数が掲載され、各リリースのドキュメントが新しい `docs-release` ワークフローで公開されるようになりました。

### 今後の変更予定

- **外部パッケージとの連携強化**

  外部パッケージからpreambleを使うのは難しいため、今後のリリースではこれらの仕組みを見直し、サードパーティーパッケージとの連携を改善する可能性があります。`touying-reducer-bindings` は維持し、パッケージがTouyingに必要な機能を宣言するためのフックを追加する予定です。これにより、利用者自身が連携コードを記述せずに済むようにします。`default-*-preamble` はテーマや利用者が独自のpreambleを設定してもTouying自身の初期化を維持するために存在しますが、非推奨になる可能性があります。0.8.1では上述の修正以外に変更はありません。

## v0.8.0

大規模なリリースであり、**Typst 0.15** を必須とする最初のバージョンです。

主な新機能は**記事モード**です。1つのソースからスライドまたは連続した文章を生成でき、アニメーションは最終状態にまとめられ、画像を横に回り込ませることもできます。新しいアニメーション機能として、選択した段階の内容を描画する `touying-render` と、表示状態と装飾を組み合わせる `animate` が追加されました。`touying-recall` はスライド全体だけでなくラベル付き要素にも対応します。`touying-fn-wrapper-raw` は本文を解析して入れ子にできるようになり、`#alert[.. #pause ..]` なども動作します。`.pdfpc` ファイルは別途 `typst query` を実行せず、バンドルのアセットとして直接出力できます。脚注形式の参考文献はTypst標準の参考文献機能に基づいて再設計され、発表者ノートのパネルはテーマが提供する関数で構築されます。内部では6288行あった `src/core.typ` をモジュールに分割し、内容の処理を統一するとともに、主にutilsにあった関数の配置を変更しました。

アップグレード時の注意点として、`uncover`・`only`・`alternatives` の後の `#pause` が、これらの関数が確保するサブスライドを飛ばさなくなりました（v0.7.0で誤って導入された動作です）。脚注形式の参考文献の設定形式が変更され、`semi-transparent-cover` は非推奨となり、セカンドスクリーンでの背景はスライド側だけに適用されます。以下に移行ガイドがあり、末尾にはテーマ作者向けの別のガイドもあります。ドキュメントの多くも書き直し・修正されています。

### 破壊的変更

- **機能・破壊的変更：TouyingはTypst 0.15.0以降を必須とします**

  `typst.toml` の `compiler` が `0.12.0` から `0.15.0` に変更されました。`.pdfpc` のバンドル出力と新しいカバーメソッドに必要です。リファレンス画像はTypst 0.15とtytanic 0.4で再生成されました。

- **修正・破壊的変更：`uncover`・`only`・`alternatives` の後の `#pause` が確保済みサブスライドを飛ばさなくなりました**

  ```typst
  #uncover("2")[Only on subslide 2]
  On subslide one
  #pause
  On subslide two    // v0.7.0-v0.7.4: on subslide *three*
  ```

  v0.6.xまでは相対ジャンプでカウンターが1つ進みました。v0.7.0では、先行する関数ラッパーが確保した最大のサブスライドの次まで進むよう変更されました。これは一連のpauseの代わりとなる `item-by-item` を想定したものですが、ほかのラッパーと同じ経路で範囲を通知するため、`uncover`、`only`、`alternatives` にも影響しました。検証するテストがなく見逃されていました。今回、サブスライドの確保と消費が再び独立した操作になりました。

  ウェイポイントの配置も修正されました。`#uncover("5")` の後の `#waypoint(<w>)` はサブスライド5の次ではなく、現在の内容の流れにおける次の位置を指します。

  **移行方法：** 以前の自動ジャンプに依存していた場合は、必要な `#pause`、`#jump(n, relative: true)`、ウェイポイントを明示してください。`item-by-item` と `item-by-item-fn` には影響がなく、後続のpauseは引き続き最後の項目より後へ進みます。`#pause` のない後続内容も含め、手動で記述したpause列と一致します。

- **機能・破壊的変更：`show-bibliography-as-footnote` が真偽値になり、`magic.bibliography()` が削除されました**

  以前は `config-common(show-bibliography-as-footnote: bibliography("ref.bib"))` で参考文献要素を受け取り、preamble内で非表示にして項目を状態へ収集し、`magic.bibliography()` で再描画していました。この状態管理は削除されました。代わりにフラグを設定し、プレゼンテーション内で実際の `#bibliography(..)` を呼び出します。

  ```typst
  #show: simple-theme.with(
    config-common(show-bibliography-as-footnote: true),
  )

  == References
  #bibliography("ref.bib")
  ```

  脚注形式の引用を解決するには参考文献を文書に含める必要があります。文献一覧を表示したくない場合は `hide(bibliography(..))` を使用します。複数の参考文献に対応し、脚注がスライドからはみ出すと文書の収束が止まる問題 [#395](https://github.com/touying-typ/touying/issues/395) も解決されました。`"chicago-notes"` のように引用形式自体が脚注を生成する場合は、このフラグを `false` のままにしてください。詳しくは [FAQ](https://touying-typ.github.io/docs/faq) を参照してください。

  `magic.bibliography-as-footnote` から `bibliography` パラメーターが削除され、引用マーカーの `super` のプロパティを指定する辞書 `footnote-style` が追加されました。既定の `(typographic: false, baseline: 0em, size: 1em)` では通常のテキストとして表示されます。

- **機能・破壊的変更：発表者ノートのパネルを `config-common(notes-fn: ..)` でカスタマイズ可能になり、`config-methods(show-only-notes: ..)` の*描画関数*が削除されました** ([#353](https://github.com/touying-typ/touying/issues/353))

  v0.7.4では `show-only-notes` に2種類のオプションがあり、変更されたのはその一方だけです。発表者ビューを有効にする `config-common(show-only-notes: true)` は従来どおり動作し、[発表者ノート](https://touying-typ.github.io/docs/tutorials/speaker-notes)で初めて説明されました。削除されたのはパネルの描画関数 `config-methods(show-only-notes: ..)` であり、主にテーマ作者が上書きしていました。

  パネルは `slide-fn` がスライドを作るのと同様に、テーマが提供する関数で生成されます。`notes-fn(self: none, note: none, slide-preview: none) -> content` は `slide-fn` に対応し、`touying-notes` は `touying-slide` に対応します。レイアウトの基本を担い、テーマ側が色や配置を指定します。組み込みの6テーマはすべて独自の関数を登録しているため、ノートの見た目もスライドと統一されます。一部の配置の不具合は今後修正予定です。

  `show-only-notes` モードも再構成されました。以前は右上にあったスライドの各部分がページサイズの1つのボックスにまとめられ、`slide-preview` としてテーマに渡されます。テーマ側で自由に配置と縮尺を指定できます。従来の `cutout`、`cutout-height`、`(background, foreground)` を返す方式は削除され、`slide-preview: none` はセカンドスクリーンであることを示します。また、パネルの背景がページヘッダーから配置され、ほぼ全体がページ外に出て `#CCCCCC` と `#E6E6E6` が描画されなかった問題 [#281](https://github.com/touying-typ/touying/issues/281) も修正されました。

  詳しくは[発表者ノートのパネルのカスタマイズ](https://touying-typ.github.io/docs/themes/custom)と[独自テーマを作成する](https://touying-typ.github.io/docs/tutorials/build-your-own-theme)を参照してください。

- **修正・破壊的変更：セカンドスクリーンで背景と前景がスライド側の半分に限定されました** ([#219](https://github.com/touying-typ/touying/issues/219))

  `show-notes-on-second-screen` ではページを1方向に2倍へ拡張し、追加した半分をマージンへ配置します（現在は4方向に対応します）。以前は `page(background: ..)` がページ全体を覆い、`100%` の背景や `fit: "cover"` の画像がノート側まで引き伸ばされていました。現在は `background` と `foreground` をそれぞれスライド側の1ページ分のボックスに配置するため、割合指定や `fit: "cover"` はセカンドスクリーンなしの場合と同様に動作します。

- **機能・破壊的変更：`touying-recall` の `subslide:` が `subslides:` に変更され、`none` と `auto` の意味が入れ替わりました**

  このパラメーターはv0.6.3から単一のサブスライドではなく範囲を指定でき、`uncover`、`only`、`effect` の `visible-subslides` に合わせて複数形になりました。従来の値を包含する指定方法に対応します。

  新しい既定値は `subslides: auto` です。スライド全体が対象ならすべてのサブスライドを、ラベル付き要素ならアニメーションの最終状態を再利用します。`subslides: none` は最後のサブスライドだけを再利用します。v0.7.4では逆で `none` が既定値だったため、既定動作自体は変わりません。明示的に指定していたコードだけがキー名と値の入れ替えを必要とします。

### 軽微な破壊的変更

- **機能・破壊的変更：`utils.semi-transparent-cover` は非推奨になりました** — 代わりに `utils.alpha-changing-cover` または `utils.color-changing-cover` を使用してください

  従来は隠す内容の上に半透明の矩形を重ねていましたが、背景まで薄くなり、隠した領域が重なると効果が蓄積するため、一般には適切に動作しません。表示自体は引き続き可能で、プレゼンテーションもコンパイルできますが、以前より見た目が悪くなる場合があります。`--warnings promote` を指定したビルドでは失敗します。

  ```typst
  // Before
  config-methods(cover: utils.semi-transparent-cover)
  // After: a real alpha cover, with an automatic fallback for content it cannot recolour
  config-methods(cover: utils.alpha-changing-cover)
  // Or, cheaper to compile, flattening everything to one colour
  config-methods(cover: utils.color-changing-cover.with(color: gray))
  ```

  ほかのカバーメソッドの `fallback-hide` として渡す場合は引き続き使用でき、警告も出ません。`utils.cover-with-rect` も警告の対象外です。

- **修正・破壊的変更：`utils.handout-only` が削除されました** — 代わりにトップレベルの `#handout-only[..]` を使ってください。これはコールバックではなくパーサー用のマークなので、スライドを区切る内容を含められます。 See [Handout Mode](https://touying-typ.github.io/docs/tutorials/dynamic/handout).

- **修正・破壊的変更：アニメーション関数に負のサブスライド番号を渡すと、誤動作を黙って続ける代わりにエラーになります。**

  負の番号は最後のサブスライドから逆算する指定であり、サブスライド数が確定して初めて意味が定まります。`config-common(handout-subslides: -1)`、`#touying-recall(.., subslides: -1)`、`#touying-render(.., subslides: -1)` は解析後に指定値を解決するため、負の番号を使用できます。

  `#only`、`#uncover`、`#effect`、`#alternatives`、`#alternatives-match`、`#alternatives-cases`、`#animate`、`#item-by-item` は内容の流れの中で解析されるため、負の番号は無視されずエラーになります。必要な場合はウェイポイントを使用してください。

  ```typst
  // Before: rendered nothing, silently
  #only(-1)[Last]
  // After: a waypoint names the position and keeps naming it
  A #pause B #waypoint(<end>) C
  #only(get-last(<end>))[Last]
  ```

  `#waypoint(start:)` も同様に制限され、開始位置は1以上が必要です。別のウェイポイントとの相対位置を指定する場合はラベルで参照してください。

  注意：文字列に含まれる負の数は別の記法なので影響を受けません。`"-2"` は「最後から2番目」ではなく「サブスライド2まで」という開いた範囲です。

- **修正：はみ出し検出が幅も確認するようになりました。** 以前は高さしか比較していなかったため、幅が大きすぎる固定幅のブロックなどでは警告が出ませんでした。

- **機能・破壊的変更：`touying-equation` が非推奨になりました。** その用途はほかの機能で代替でき、通常の数式アニメーションも強化されました。現在は `mat` や `frac` などの数式レイアウト関数内にもアニメーション関数を記述できます。

- **機能・破壊的変更：`config-common(detect-overflow:)` の既定値が `true` から `auto` に変更されました。** `breakable` と逆の設定になるため、両方が `true` の場合には、はみ出しを検出できない旨の警告が出ます。

- **機能・破壊的変更：`#measure` が `std.measure` を隠蔽し、アニメーション化された内容も正しく測定するようになりました。** 従来どおり周囲に `layout` または `context` が必要です。`width` と `height` に加え、測定前に描画するサブスライドとその基準を整数の `subslide` と `base` で指定できます。

- **機能・破壊的変更：reducerのバインディングがパッケージモジュールを受け取るラムダ式になりました。** 従来の文字列の配列と末尾の `arguments(..)` によるパス指定は使われません。

  ```typst
  // Before
  #let touying-reducer-bindings = (
    reduce: ("canvas",),
    cover: ("draw", "hide", arguments(bounds: true)),
  )
  // After
  #let touying-reducer-bindings = (
    reduce: module => module.canvas,
    cover: module => module.draw.hide.with(bounds: true),
  )
  ```

  従来の形式は動作しません。`touying-reduce` も新しい形式を要求します。

### 移行ガイド

1. **コンパイラーを更新：** Typst 0.15.0以降へ更新してください。
2. **参考文献：** `config-common(show-bibliography-as-footnote: bibliography("ref.bib"))` を `config-common(show-bibliography-as-footnote: true)` に変更し、プレゼンテーション内で実際の `#bibliography("ref.bib")` を呼び出してください。`#magic.bibliography(..)` は削除します。複数のサブスライドを持つスライドに参考文献がある場合は、`only("h", bibliography("ref.bib"))` で囲み、一度だけ生成されるようにしてください。そうしないとサブスライドごとに新しいインスタンスが生成され、引用番号が再び最初から始まります ([#415](https://github.com/touying-typ/touying/issues/415)).
3. **発表者ノート：** `config-methods(show-only-notes: ..)` の*描画関数*を上書きしていた場合は、`touying-notes` を基にした `config-common(notes-fn: ..)` に書き換えてください。`config-common(show-only-notes: true)` は変更不要です。
4. **セカンドスクリーン：** テーマが `config-page(background: ..)` でノート側の背景を描画していた場合は、その設定を `notes-fn` の `touying-notes` にある `fill` または `header-fill` へ移動してください。
5. **`touying-recall`：** `subslide:` を `subslides:` に変更してください。`none` または `auto` を明示していた場合は、その値も入れ替えてください。既定値を利用していた場合は変更不要です。
6. **カバー関数：** `config-methods(cover: utils.semi-transparent-cover)` を `utils.alpha-changing-cover` または `utils.color-changing-cover` に変更してください。そのまま残しても通常は警告だけですが、`--warnings promote` ではエラーになります。
7. **pauseの進行：** 最初の破壊的変更を参照してください。関数ラッパーによるカウンターの自動ジャンプに依存していた部分には、`#pause` や `#jump` を明示するか、ウェイポイントを使ってください。

### 機能追加

- **機能：記事モード — 1つのソースからスライドと連続した文章を生成**

  `config-common(export-mode: "article")` または `typst compile slides.typ --input export-mode=article` を指定すると、スライドをページごとに区切らず、連続した文書として描画します。アニメーションは最終状態にまとめられ、画像は必要に応じて横へ回り込ませられます。`export-mode` は `"slides"`（`handout` フラグで切り替わる既定値）、`"presentation"`、`"handout"`、`"article"` に対応します。内部的な切り替えには `config-common(article-mode: true)` が使われます。

  記事モードはスライドのレイアウトを引き継がず、文章の流れに線形化します。composerの列は連続した文章になり、回り込み要素の幅は元のスライドで指定された値に関係なく、`config-article(wrap: (width: ..))` に従って本文幅の既定50%となります。詳しくは[記事モード](https://touying-typ.github.io/docs/integration/article-mode)を参照してください。

  ```typst
  #show: simple-theme.with(
    config-common(
      export-mode: "article",
      article-theme: themes.article.article-theme.with(numbering: "1.1"),
    ),
    config-article(
      wrap: (image: true),
      available-fields: (title: "info.title"),
    ),
  )
  ```

  記事用には2つのマーカーがあります。`#article-text[..]` はスライド内のどこに記述しても、そのスライドの内容を置き換えるため、箇条書きの代わりに文章を指定できます。`#article-only[..]` は記事にだけ内容を追加します（詳細は後述）。新しい `config-article(..)` は記事側のレイアウトを設定します。`wrap` はmeanderによる要素の回り込み、`linearize` はスライドのレイアウトをどこまで分解するかの指定です。既定では `#columns(..)`、composer、`side-by-side` が分解され、`table` や `grid` はヘッダーやフッターを宣言していない場合に分解されます。要素単位の指定には `#article-linearize[..]` と `#article-keep-layout[..]` を使います。さらに `title-block-fn` や、`info.title` のような設定を記事テーマの引数へ対応付ける `available-fields` もあります。詳しくは[記事モード](./integration/article-mode.md)を参照してください。

- **機能：シンプルなA4記事テーマ `themes.article`**

  これは `article-theme: auto` の既定のテーマです。プレゼンテーションの仕組みに依存しないため、論文やレポートにも単独で利用できます。あくまで実装例であり、ほかの記事用テーマも使用できます。


- **機能：数式要素内のアニメーションに対応** — `frac`、`mat`、`vec`、`cases`、`binom`、`root`、`attach`、`accent`、`cancel`、`lr`、各種括弧関数に対応します。

  以前はこうした数式要素内に `#pause`、`#uncover`、`#only`、`#alternatives` を記述すると、`Unsupported mark` でコンパイルが失敗していました。実際には内容にアクセス可能でしたが、これらの要素は部分的な内容を `body` や `children` ではなく、`frac` の `num`・`denom`、`mat` の `rows`、`attach` のスクリプトなど独自の名前付きフィールドに保持します。従来の走査処理は `body` と `children` しか認識せず、要素を葉として扱っていました。

  ```typst
  $ frac(a #pause + b, c) $
  $ mat(a, #pause b; c, d) $
  $ a^(2 #pause + 1) $
  ```

  装飾用フィールドには影響しません。アニメーション化した `mat` では `delim` と `row-gap`、`accent` では `size` と `dotless` が維持されます。ただしアクセントの*accent*引数だけは内容ではなく単一記号として保持されるため、直接アニメーション化できません。`#alternatives` で要素全体を切り替えてください。

- **機能：出力モード専用の内容とラベル**

  4種類のインラインマーカー `#article-only[..]`、`#handout-only[..]`、`#presentation-only[..]`、`#slides-only[..]` で出力先に応じた内容を指定できます。最後のものは2つのスライドモードを対象とし、記事では表示されません。非表示の内容は領域を残さず削除されます。本文に改ページ要素を含められるため、`#handout-only[..]` 内の見出しは配布資料モードで新しいスライドを開始します。

  マーカーはスライドの本文内にも呼び出し全体の外側にも配置でき、その位置によって適用範囲が決まります。`#slide[a #slides-only[b] c]` は記事から `b` だけを除外し、`#slides-only(slide[a b c])` はスライド全体を除外します。スライド内の `#article-text[..]` も文書レベルと同様に、そのスライドを置き換えます。詳しくは[出力モード](https://touying-typ.github.io/docs/tutorials/output-modes)を参照してください。

  同じ選択をスライドごとの見出しやラベルでも指定できます。`<touying:presentation>`、`<touying:handout>`、`<touying:slides>`、`<touying:article>` はハイフンで組み合わせられ、`<touying:presentation-article>` は論理和を意味します。新しい `<touying:never>` はTypstにおける `\iffalse` に相当し、マークアップをコメントアウトせずにスライドをすべての出力から除外できます。詳しくは[配布資料モード](https://touying-typ.github.io/docs/tutorials/dynamic/handout)と[セクションと見出し](https://touying-typ.github.io/docs/tutorials/sections)を参照してください。

- **機能：`touying-recall` がスライド全体以外のラベル付き要素にも対応**

  `#touying-recall(<lbl>)` はスライド全体だけでなく、ラベル付きのreducer、図、任意のアニメーション内容を受け取り、指定した段階で再表示できます。

  ```typst
  #touying-recall(<my-slide>, subslides: "waypoints")  // one subslide per waypoint
  #touying-recall(<my-diagram>, subslides: 1)          // a reducer at stage 1
  #touying-recall(<my-table>)                          // plain labelled content
  ```

  サブスライドの選択には整数、最後から逆算する負の番号、ウェイポイントのラベルやマーカー（`get-first`、`get-last`、`prev-wp`、`next-wp`）が使えます。これらは再利用対象自身のウェイポイントを基準に解決されます。要素の再利用はインラインで配置され、スライド全体を再利用するときだけ新しいページが始まります。呼び出しは `#only`、`#uncover`、`#alternatives` の本文内の任意の場所に配置でき、`base:` で対象内容の反復番号の起点を指定できます。

- **機能：`touying-render` が指定したアニメーション段階または範囲の内容を描画**

  `touying-recall` がラベルから既存の内容を探すのに対し、`touying-render` は内容の値を直接受け取ります。小さなプレビュー、記事出力で図の途中の状態を示す場合などに便利で、スライド全体の内容も描画できます。

  ```typst
  #touying-render(body, subslides: "2-4")   // steps through 2, 3 and 4
  #touying-render(body, subslides: "!2-3")  // skips 2-3, collapsing the gap
  #touying-render(body, subslides: <wp>)    // the waypoint's whole range
  ```

  整数や `get-first`、`get-last` のように1点を指定すると、その段階に固定されます。`subslides: auto` は外側のスライドの進行に従います。`base:` は内容内部のpause番号をずらし、`start:` は最初に描画されるサブスライドの開始位置を指定します。`repeat-last:` は最終状態を保持するか（既定）、再び非表示にするかを制御します。

- **機能：`animate` により1つの内容に表示状態と装飾を組み合わせる**

  ```typst
  #animate(
    [The original],
    effects: (
      (effect: "cover", subslides: "-2"),
      (effect: swap[Something else], subslides: 3),
      (effect: (body, ..) => text(red, body), subslides: "4-", priority: 2),
    ),
  )
  ```

  配置効果（`"show"`、`"cover"`、`"remove"`、`swap(..)`）はサブスライドごとに1つだけ選ばれ、合成できません。`hide` はスタイルノードではなく、`"remove"` は内容を削除するため、内部から元に戻せません。隠す内容のアルファ値を変更したい場合はスタイル効果を使ってください。スタイル関数は合成・入れ子が可能です。優先度は配置効果の採用と入れ子の順序を決め、同順位ではどちらも最後に書いた効果が優先されます。配置効果では最後の効果が選択され、スタイルでは最後の効果が最も内側となり、共通するプロパティを優先します。`swap` は `alternatives` と同じ `stretch` を持ち、無効なら置き換え中にレイアウトが変わり、有効なら確保済みの領域に合わせてサイズを維持します。測定が行われるのは `stretch` を有効にしたswapがある場合だけで、配置効果とスタイルの組み合わせごとに一度測定します。`animate-hidden` と `animate-removed` は、初期状態を `"show"` の代わりに `"cover"` または `"remove"` とする短縮形であり、効果は既定で内容を表示するタイミングを示します。いずれも上書き可能です。

- **機能：連続したpauseの代わりとなるラッパー向けの `touying-fn-wrapper` の `advances-flow`**

  `item-by-item` が後続のpauseを最後の項目より後へ進める仕組みは通常のパラメーターになったため、独自のラッパーでも有効にできます。

  ```typst
  #let reveal-lines(body) = touying-fn-wrapper(
    (self: none, body) => /* ... */ body,
    last-subslide: repetitions => (repetitions + count-lines(body) - 1, (:)),
    advances-flow: true,   // a following #pause continues after the last line
    body,
  )
  ```

  既定値は `false` で、自分自身の内容だけをアニメーション化する場合に適しています。このようなラッパーは、pauseの位置を動かさずに必要なサブスライドを確保します。

- **機能：`touying-fn-wrapper-raw` が本文を再帰的に解析し、`touying-fn-wrapper` の内部にもraw版を配置可能に**

  以前は `#alert[This is important, #only("2-")[easter egg] don't miss it!]` が *Unsupported mark* で失敗していました。rawラッパーは位置引数をラップ先へ直接渡していたため、内部のアニメーションマーカーが未解決のメタデータとして残っていたためです。現在は通常のスライド内容と同様に解析されるため、`#pause`、`#meanwhile`、すべての関数ラッパーが内部でも動作し、内容はスライドのサブスライド総数にも反映されます。入れ子になったrawラッパーは再帰的に解決され、`#uncover[#alert[a #alert[b #alert[c]]]]` も動作します。

  これにより、マークアップから `self` を参照するには `touying-fn-wrapper-raw` が適した既定の選択肢となりました。ドキュメントとテーマもこれに合わせて更新され、Stargazerの `tblock` も通常のアニメーション関数と併用できるよう変更されました。ただし本文は位置引数として渡す必要があり、`.with(..)` で事前に渡すとパーサーから見えなくなります。詳しくは[複雑なアニメーション](https://touying-typ.github.io/docs/tutorials/dynamic/complex)を参照してください。

- **機能：文書ごとに `.pdfpc` ファイルをバンドルのアセットとして出力** ([#408](https://github.com/touying-typ/touying/issues/408))

  Typst 0.15のバンドル出力は任意のファイルを生成できるため、別途 `typst query .. > example.pdfpc` を実行する必要がなくなりました（従来の方法も利用でき、ドキュメントでは現在 `typst eval` を使用しています）。`#pdfpc.bundle-assets()` を各 `#document(..)` の外側、トップレベルに記述します。

  ```sh
  typst compile --features bundle --format bundle --root . ./example.typ ./out
  # => ./out/deck.pdf and ./out/deck.pdfpc
  ```

  バンドル内でpdfpcのメタデータを持つ各PDFには、対応する `.pdfpc` ファイルが隣に生成されます。ラベル番号は文書ごとの最初のスライドから付けられ、ノート、マーカー、設定もその文書のものだけを含みます。他の出力先では呼び出しは何もしないため、通常のPDFにもコンパイルするファイルに記述したままにできます。詳しくは[pdfpc](https://touying-typ.github.io/docs/external/pdfpc)を参照してください。

- **機能：1つのソースファイルから複数の文書を生成** ([#406](https://github.com/touying-typ/touying/issues/406), thanks @mxmerz)

  Typst 0.15のバンドル出力では、`#document("name.pdf", [..])` を使って1つのファイルから複数のPDFを生成でき、それぞれにスライドを含められます。見出しの検索範囲はその文書内に限定されるため、`utils.current-heading` と、それに基づくヘッダー、ノートパネル、進捗付きアウトラインが隣の文書の見出しを取得することはなくなりました。pdfpcのメタデータも同様に文書ごとに分離されます。

  ```typst
  #document("deck-a.pdf", [
    #show: simple-theme
    == Deck A
  ])
  #document("deck-b.pdf", [
    #show: simple-theme
    == Deck B
  ])
  ```

- **feat: themes style the speaker-note panel through `touying-notes`**

  `touying-notes` は `header`、`header-fill`、`fill`、`note-setting`、`preview-setting` を受け取ります。テーマは `note-setting` でページや内容を装飾できます。`header-height` は既定で内容の高さに合わせて縮み、`header: none` では帯を完全に削除します。`preview-setting` には元のサイズのスライドプレビューが渡されるため、`preview => place(bottom + right, box(scale(x: 30%, y: 15%, reflow: true, preview)))` のように自由に拡大縮小・配置できます。組み込みの6テーマはすべて独自の設定を持ちます。詳しくは[発表者ノート](https://touying-typ.github.io/docs/tutorials/speaker-notes)を参照してください。

- **機能：`show-notes-on-second-screen` が `bottom`・`right` に加えて `top`・`left` に対応しました。** 対応する方向にページが2倍へ広がり、追加領域はマージンに配置されるため、スライド本体の寸法は4方向のいずれでも維持されます。

- **機能：`alpha-changing-cover` と `color-changing-cover` の再設計によるカバー表示の改善** ([#112](https://github.com/touying-typ/touying/issues/112))

  推奨される半透明のカバーは、非推奨となった `semi-transparent-cover` に代わる `utils.alpha-changing-cover` です。装飾可能なすべての色を扱い、要素に色指定がない場合は外側のスコープから継承し、グラデーションやタイルにも対応し、特色をOklchへ変換してから透明度を設定します。代替処理のアルファ値は対象色の明るさに応じて決まり、代替処理自体の使用も抑えられるため、画像や図が灰色のブロックになりにくくなりました。`utils.color-changing-cover` は色を読み取る代わりに上書きするため `context` を呼ばず、コンパイル負荷の小さい代替手段です。両者は同じ木構造を走査し、線、表のセル、`underline`、`highlight`、`strike` の本文を同様に扱い、内容の位置を動かしません。独自の背景を持つ要素では前景と背景を同色にすると読めなくなるため、既定の `color-changing-cover(hide-filled: true)` はそれらを `fallback-hide` に渡します。`fallback-hide` の既定値は `auto` となり、変更後の文字色に合わせて薄くします。詳しくは[カバー関数](https://touying-typ.github.io/docs/tutorials/dynamic/cover)を参照してください。

- **機能：`components.left-mid-right` で内容を3列に配置** ([#397](https://github.com/touying-typ/touying/pull/397), thanks @thomas-saigre)

- **機能：コマンドライン入力を読み取る `utils.get-input`**

  `--input key=value` で渡された値はTypstでは文字列になります。`utils.get-input` は各値をTypstとして解析するため、`accent=red` は色、`count=3` は整数、`flag=true` は真偽値になります。Typstが認識しない単独の単語は文字列として返されるため、`--input export-mode=handout` をシェルで引用符なしに指定できます。現時点では主に出力モードで利用していますが、今後ほかのコマンドライン設定にも利用する可能性があります。キーを省略すると解析済みの辞書全体を取得できます。詳しくは[touying-exporter](https://touying-typ.github.io/docs/external/touying-exporter)を参照してください。

- **機能・修正：`touying-get-config` が `common` を正式なカテゴリーとして公開**

  `touying-get-config("common.handout")` が解決されるようになり、`touying-get-config().common` は以前からドキュメントに記載されていたとおり、トップレベルのキーを部分木として返します。`common.store` のようにその配下でカテゴリーを指定すると、黙って失敗するのではなく明確なエラーが出ます。

- **機能：ウェイポイントがグローバルにリンク可能なアンカー `<slide.waypoint>` を生成します。** `<slide>` ラベルのあるスライド内の `<waypoint>` に対し、最後のサブスライドへのリンクを作成できます。アンカーは可能な限り適切な位置に置かれ、ウェイポイントの最後のサブスライドが描画されるならそこに配置されます（プレゼンテーションモードの既定動作）。そうでなければ直後のできるだけ近い位置、それも不可能ならウェイポイント内のできるだけ後ろに配置されます。描画されるサブスライドがすべて対象範囲より前ならアンカーは生成されません。

- **機能：新しいユーティリティを追加しました。** `core/tree.typ` の `tree.is-math-symbol` と `tree.sequence-to-array`、`core/subslides.typ` の `subslides.resolve-negative-subslides`（反復回数と任意の1以外の基準から負の番号を解決）、`utils.rescale-image` です。

- **修正：スライド内のラベル付き `#place`、`#rotate`、`#columns`、`#terms.item`**

  従来はパーサーがこの4種類を手動で再構築してラベルをコンストラクターへ直接渡していたため、`#place(top + right)[..] <lbl>` はコンパイルエラーになり、`#columns(2)[..] <lbl>` はラベルが失われていました。共通の再構築処理を通すようになり、ラベルが維持されます。

- **修正：本文が `#set` で始まる場合に `#item-by-item` がまとめられなくなりました**

  項目数を数える3つの処理が `#set` や `#show` の生成する `styled` ノードの内部を参照できず、`#item-by-item[#set text(..) \ - one \ - two]` が3つではなく1つのサブスライドとして描画されていました。

- **修正：ラベル付きリストに対する `magic.nontight`**

  要素の `label` を受け付けないコンストラクターへ展開していたため、`nontight-list-enum-and-terms` の設定下では `#list([a], [b]) <lbl>` がエラーになっていました。

- **修正：発表者ノートで装飾された内容が失われなくなりました**

  `utils.markup-text` が `styled` 部分木をすべて捨てていたため、`[a #text(red)[b] c]` は `.pdfpc` ファイルに `"a c"` として渡されていました。pdfpcへの出力に使われるため、ノートの内容が黙って欠落していました。

- **修正：`terms.item` や図のキャプション内のウェイポイント**

  ウェイポイントの前処理ではどちらの内部も走査しませんでしたが、メインの解析では走査していたため、その後のウェイポイントが誤ったサブスライドに配置されていました。

- **修正：特殊なスライドが不可視の見出しとしてタイトルを公開**

  アウトラインスライドのタイトルは見出しにならないため、現在の見出しを参照する関数では取得できませんでした。ヘッダーは明示的に渡されたタイトルで動作していましたが、ノートパネルは何も表示できませんでした。現在は `place(hide(heading(level: self.slide-level, ..)))` を生成します。`hide` だけではアウトラインの内容が約16pt下がるため、`place` で文書の流れから除外します。`level: self.slide-level` は `custom-progressive-outline` が「Outline」を現在のセクションと誤認してすべての項目を薄くする問題を防ぎます。aqua、dewdrop、metropolis、stargazerの `outline-slide` とstargazerの `ending-slide` に適用され、タイトルスライドは現状維持です。

- **修正：見た目だけを変えるカバーで隠された引用は引き続き実際の引用として扱われます**

  `color-changing-cover` は引用を `hide` の代替処理へ渡していたため、引用マーカーだけが消え、対応する参考文献の項目は残っていました。`@key` は内容の木構造では `ref` であり、レイアウト時に `cite` となるため、どちらも色変更の対象に含まれていませんでした。現在は両方に対応し、引用は `raw` と同様に色変更されます。また、`magic.bibliography-as-footnote` は `utils.cover-hides-footnote` を調べず `hide` 要素の有無で仮の脚注マーカーを描画していました。現在は設定を参照し、パーサーの脚注処理と一致します。

  未解決：`cover-with-rect` のような `hide` 以外の不透明なカバーを `cover-hides-footnote: true` と組み合わせると、脚注の項目が漏れ出します。

- **修正：`#pause` の後にある注釈形式の引用が前のサブスライドに脚注を表示しなくなりました** ([#414](https://github.com/touying-typ/touying/pull/414), thanks @SchrodingerBlume)

  [#399](https://github.com/touying-typ/touying/pull/399) では `#pause` の前に文字どおりの `#footnote` が表示される問題を修正しましたが、`chicago-notes` のようなCSL注釈形式の引用では脚注が漏れ出ていました。こうした引用が脚注になるのは、`cover-hides-footnote` が参照する本文走査より後のレイアウト段階だからです。`hide()` はマーカーを隠しても、予約された項目はページ下部に表示されていました。現在は `cite` と `ref` も `footnote` と同じ3種類の分岐に従い、実際に隠すカバーの下では引用自体を生成せず、表示された時点で通常どおり描画します。`show std.cite` ルールから脚注を生成するパッケージにも効果があります。

- **修正：脚注形式の参考文献が文書の収束を妨げなくなりました** ([#395](https://github.com/touying-typ/touying/issues/395)) — fixed by the bibliography rework above. Verified across 30 size × breakable/detect-overflow combinations that previously warned *document did not converge within five attempts*. A CI step runs the regression with `--warnings promote`.

- **修正：`#pause` で隠された `grid` や `table` 内でも `#uncover`、`#only` などのラッパーが表示されます。** ラッパーは自分自身の表示状態を決めるため外側のpause領域から独立しますが、表形式のコンテナーはセルを再解析する前に非表示かどうかを判定し、その古い結果を使い回していました。`block` 内の `grid` のような入れ子でも発生していました。

- **修正：参考文献の描画に先行する `pagebreak()` が不要になりました。** モード専用の内容内の `---` はスライド区切りとして処理され、`pagebreak()` は引き続き改ページとして扱われます。

- **修正：`touying-get-config` の `default:` の番兵値が組み込みの `type` ではなく専用マーカーになりました。** `default: type` は特別な値ではなくなり、キーを位置引数と名前付き引数の両方で渡すと、黙って無視される代わりにエラーになります。

- **修正：脚注が表示開始の `#pause` より前に現れなくなりました** ([#399](https://github.com/touying-typ/touying/pull/399), thanks @RivinHD for the initial contribution)

- **修正：pauseされた内容の後に `touying-fn-wrapper` が続く場合、`hidden-parts` を正しい順序で出力します。** ラッパーが先行する内容より前に描画されることはなくなりました。 ([#400](https://github.com/touying-typ/touying/pull/400))

- **修正：カバーの間隔** ([#387](https://github.com/touying-typ/touying/issues/387), [#405](https://github.com/touying-typ/touying/pull/405))

- **修正：reducerの要素を1つのブロックとしてではなく個別にカバー** ([#371](https://github.com/touying-typ/touying/issues/371), [#381](https://github.com/touying-typ/touying/pull/381))

- **修正：`touying-reducer` が `reduce` と `cover` にパッケージ自身の要素形式を渡します**, so a package whose drawing function takes `..args` rather than one array now binds without any adapter.

  A package's element is whatever its element constructor returns, and that differs: a CeTZ `rect(..)` and an alchemist `molecule(..)` are each a one-element array, while a fletcher `node(..)` is content and a lilaq `plot(..)` is a dictionary. Writing the body as a code block (`#cetz-canvas({ rect(..); circle(..) })`) makes Typst join those arrays into one, so the reducer received the elements already unpacked and had to guess how to put one back together when covering it. It always wrapped, which is right for CeTZ and alchemist and wrong for everything else.

  The two call styles are distinguishable where the arguments arrive, before any flattening: a code block is a single array-typed positional argument, while elements passed directly are separate arguments. The reducer now records that per item and gives each side back what it started with — `cover` gets a whole element, and `reduce` gets one array for a block body or spread arguments for a variadic one such as `lq.diagram(..plots)`.

  ```typst
  // lilaq, whose plots are dictionaries and whose diagram takes `..plots`
  #let lq-diagram = touying-reducer.with(
    reduce: lq.diagram,
    cover: plot => plot + (x: (), y: (), label: none),
  )
  ```

- **修正：コールバック形式の呼び出しが再び動作** ([#374](https://github.com/touying-typ/touying/issues/374), [#380](https://github.com/touying-typ/touying/pull/380))

- **修正：最終ページのメタデータ追加時に空の出力スライドを番号付けしない** ([#382](https://github.com/touying-typ/touying/pull/382))

- **修正：pdfpcコマンドの型の誤り** ([#407](https://github.com/touying-typ/touying/pull/407), thanks @vilaureu)

- **修正：`touying-diagram` のバインディングとCeTZのドキュメント例** ([#389](https://github.com/touying-typ/touying/pull/389))

- **多言語対応：アウトラインのフランス語訳** ([#396](https://github.com/touying-typ/touying/pull/396), thanks @tarikgraba)

- **dewdropテーマ：文書化されたパラメーター一覧から `outline-title` を削除しました。** これは実際には `dewdrop-theme` のパラメーターではなく、アウトラインスライドのタイトルには `title` 引数を使います。

- **stargazerテーマ：リストのマーカー位置を修正** ([#393](https://github.com/touying-typ/touying/pull/393), thanks @joseph-tao)

- **stargazerテーマ：使用していない `footer` と `footer-right` がドキュメントに掲載されていた問題を修正**

### ドキュメント

ほぼすべてのページを更新しました。主な変更は以下のとおりです。

- **ドキュメント：[記事モード](https://touying-typ.github.io/docs/integration/article-mode)のチュートリアルを追加。** 記事テーマとスライドテーマの連携が主な用途なので、連携セクションに配置しました。

- **ドキュメント：[発表者ノート](https://touying-typ.github.io/docs/tutorials/speaker-notes)のチュートリアルを追加**（英語・中国語）。ノートの配置、セカンドスクリーン、発表者ビュー、サブスライドごとのノート、pdfpc向けMarkdown、出力、パネルの装飾を解説します。
- **ドキュメント：** [独自テーマ](https://touying-typ.github.io/docs/themes/custom) に発表者ノートのパネルの装飾、不可視の見出しによる特殊スライドのタイトル取得、ヘルパーで `touying-fn-wrapper-raw` を使う理由を追加しました。[独自テーマを作成する](https://touying-typ.github.io/docs/tutorials/build-your-own-theme) にもノートのカスタマイズの節を追加しました。
- **ドキュメント：** [カバー関数](https://touying-typ.github.io/docs/tutorials/dynamic/cover) を `alpha-changing-cover` と `color-changing-cover` に合わせて書き直し、`cover-hides-footnote` の節を追加しました。`auto` は非表示用カバーを同一性によって認識するため、手書きの `(self: none, body) => hide(body)` は見た目だけのカバーと分類され、脚注が表示開始より前に現れることを説明しています。
- **ドキュメント：** [セクションと見出し](https://touying-typ.github.io/docs/tutorials/sections) で見出しの表示を変えるラベルと出力モードで内容を絞るラベルを区別しました。`<touying:hidden>` はスライドや番号を消さず、その用途には `config-common(freeze-slide-counter: true)` が必要であると注意を追加しました。
- **ドキュメント：** [カウンター](https://touying-typ.github.io/docs/tutorials/progress/counters) とセクションの説明で `appendix` の挙動を修正しました。これは分母（`utils.last-slide-number`）だけを固定し、`utils.slide-counter` は進むため、付録では `4 / 2` のようになります。
- **ドキュメント：** [複雑なアニメーション](https://touying-typ.github.io/docs/tutorials/dynamic/complex) で `touying-fn-wrapper-raw` と `touying-fn-wrapper` に何を入れ子にできるかを説明し、テーマ作者が注意すべき `.with(..)` の落とし穴も記載しました。
- **ドキュメント：** [ウェイポイント](https://touying-typ.github.io/docs/tutorials/dynamic/waypoints) で `<part:intro>` と `<part:main>` が `<part>` にまとめられる階層的ラベルを説明しました。また、`start` は `#uncover` ではなく `#waypoint` の引数であると修正しました。
- **ドキュメント：** [Fit To](https://touying-typ.github.io/docs/tutorials/utilities/fit-to) を `fit-to-height` と `fit-to-width` の実際のシグネチャに合わせました。`height` と `width` は既定値 `1fr` の名前付き引数で、位置引数も引き続き受け付け、`reflow` と `force-height` もあります。
- **ドキュメント：** [レイアウト](https://touying-typ.github.io/docs/tutorials/layout) とFAQの記述を修正しました。`detect-overflow` は警告を出してコンパイルを続行し、`panic()` は発生しません。
- **ドキュメント：** [pdfpc](https://touying-typ.github.io/docs/external/pdfpc) で発表者ノートに加え `end-slide`、`save-slide`、`hidden-slide` とバンドル出力を説明しました。[touying-exporter](https://touying-typ.github.io/docs/external/touying-exporter) では `utils.get-input` を説明しました。
- **ドキュメント：** [CeTZ連携](https://touying-typ.github.io/docs/integration/cetz) のコールバック形式の例から不要になった `(uncover(..),)` 配列構文を削除しました。FAQのFletcherの例も同様です。
- **ドキュメント：** [FAQ](https://touying-typ.github.io/docs/faq) を大幅に改訂しました。参考文献、セクションスライドの本文、`touying-fn-wrapper-raw`、カバーメソッド、サブスライド指定の `"h"` と `"!"`、セカンドスクリーンの配置、終了したTypst Preview拡張機能に代わるTinymistなどを取り上げています。
- **ドキュメント：** [記事モード](https://touying-typ.github.io/docs/integration/article-mode) の新しいページで出力モード、記事テーマと `available-fields`、両方の出力向けの記述、アニメーション内容の再利用、画像の回り込みを説明します。
- **ドキュメント：** [出力モード](https://touying-typ.github.io/docs/tutorials/output-modes) の新しいページに4つの出力モード、4つのモードマーカー、`<touying:..>` のセクションラベルをまとめました。配布資料モードのページはそのまま残し、新しいページへのリンクを追加しました。
- **ドキュメント：** [複雑なアニメーション](https://touying-typ.github.io/docs/tutorials/dynamic/complex) に `animate`（配置とスタイルの違い、優先度、最後に指定した効果の優先）、`swap` と `stretch`、`animate-hidden`、`animate-removed`、`touying-render`、`touying-recall`、コールバック形式の関数を追加しました。
- **ドキュメント：** テーマ、FAQ、設定、セクション、ナビゲーション、新しい発表者ノートのページを含む大規模な中国語翻訳を行いました。中国語版の `settings` と `custom` では記事モードも説明します。

### その他

- **リファクタリング：`src/core.typ` をモジュールに分割しました。** 6288行のファイルは `src/core/parser.typ`、`src/core/animation.typ`、`src/core/slides.typ`、`src/core/blocks.typ`、`src/core/waypoints.typ`、新しい `src/core/article.typ` に分割されました。`src/slides.typ` は `src/entrypoint.typ` に置き換わり、新しい `src/bundle.typ` がバンドル出力用のヘルパーを保持します。`src/exports.typ` も同様に整理されました。前述の変更以外では公開APIに影響しませんが、`touying/src/core.typ` を直接インポートするコードは更新が必要です。
- **リファクタリング：内容の木構造の処理を `src/core/tree.typ` に統一しました。** 内容の種類の判定、分解、再構築が約30か所に重複しており、ラベルの扱いが呼び出し箇所によって異なり、`#set` や `#show` が生成する `styled` ノードの内部を走査できない処理もありました。新しいモジュールはTouyingに依存せず、ほかの処理からも利用できます。`shape-of` は部分的な内容の保持形式を分類し、`children-of` と `rebuild` は元の要素への復元を保証し、`map-tree` は同一性による短絡処理を持つ走査を行います。`is-*` 述語、`typst-builtin-*` ハンドル、`reconstruct*`、`trim`、`label-it` は `utils` から移動しました。`core/subslides.typ` は可視範囲指定の解決（`check-visible`、`resolve-negative-subslides`、`last-required-subslide` など）を担当し、`resolve-waypoints` は `core/waypoints.typ` に移りました。旧名称は `utils` に残り、内容を返す関数は非推奨警告付きで転送され、それ以外は新しいモジュール名を示すエラーになります。
- **リファクタリング：** ブロックの描画、ウェイポイントから整数への変換、数式・MiTeX・rawの処理、記事モードの走査処理を、それぞれ重複した複数の実装から単一の実装に統一しました。
- **テスト：** Typst 0.15.0とtytanic 0.4.0でレイアウトが変わったためリファレンス画像を再生成しました。記事モード（同一ソースのプレゼンテーション・配布資料・記事出力）、`recall-content`、`render-subslides`、`mode-never`、`notes-second-screen`、`pdfpc`、`cover-citation`、#395・#408・#415の回帰テストを追加しました。

### テーマの移行ガイド

**v0.8.0へ移行するテーマ作者向け：**

1. **本文を囲むのではなく `setting:` で装飾します。** 組み込みテーマは本文を `align(..)` やブロックで囲むのではなく、`touying-slide(setting: ..)` で配置と装飾を渡すようになりました。本文を囲むとパーサーから見えなくなり、アニメーションの段階数計算と記事モードの線形化が壊れます。
   ```typst
   // Before
   touying-slide(self: self, config: config, align(center + horizon, body))

   // After
   touying-slide(self: self, config: config, setting: align.with(center + horizon), body)
   ```
2. **`notes-fn` を登録します。** `config-methods(show-only-notes: ..)` の描画関数の上書きを、`touying-notes` を基にした `config-common(notes-fn: ..)` に置き換えてください。テーマごとに約10行で色などを指定できます。別のオプションである `config-common(show-only-notes: ..)` フラグは変更ありません。
3. **特殊なスライドには不可視の見出しを付けます。** `utils.display-current-heading` からタイトルを取得でき、ヘッダーとノートパネルの両方で利用できます。
   ```typst
   place(hide(heading(
     level: self.slide-level, title,
     bookmarked: false, outlined: false, numbering: none,
   )))
   ```
4. **`self` が必要なヘルパーコンポーネントでは `touying-fn-wrapper-raw` を優先します。** 本文を位置引数として渡し、内部のアニメーションが解析されるようにします。
   ```typst
   #let tblock(title: none, it) = touying-fn-wrapper-raw(_tblock.with(title: title), it)
   ```
5. **ノート側の背景に `config-page(background: ..)` を使わないでください。** `touying-notes` の `fill` と `header-fill` を使用します。

## v0.7.4

### 機能追加

- **機能：パッケージからreducerのバインディングを自動検出する `touying-reduce` を追加** ([#363](https://github.com/touying-typ/touying/pull/363))
- **多言語対応：アウトラインメッセージにポーランド語を追加** ([#372](https://github.com/touying-typ/touying/pull/372))
- **機能：左右キーによるナビゲーションに対応** ([#352](https://github.com/touying-typ/touying/pull/352))
- **機能：`lazy-layout` に位置の量子化グループ化用 `grid-size` パラメーターを追加し、列・行の検出を安定化**
- **機能：左右のマージンを相殺してページ幅全体に広がる `components.full-width-block` を追加**

### 修正

- **修正（軽微な破壊的変更）：`cols` の `lazy-layout` の既定値を `false` に変更**
- **修正：マーク警告で `fn` フィールドが存在しない場合を適切に処理**
- **修正：`hide` が見出しに予期しない影響を与える問題** ([#366](https://github.com/touying-typ/touying/pull/366))
- **修正：`touying-fn-wrapper-raw` を公開APIとしてエクスポート** ([#362](https://github.com/touying-typ/touying/pull/362))
- **修正：マーク警告を改善し、`enable-mark-warning` が `false` の場合も `magic.warning` として表示** ([#348](https://github.com/touying-typ/touying/pull/348))
- **修正：警告を `uniwarn` 経由で出力** ([#359](https://github.com/touying-typ/touying/pull/359))

## v0.7.3

### 軽微な破壊的変更

- **機能・破壊的変更：`#speaker-note[]` を常に前のスライドへ関連付け、`receive-body-for-new-*-slide-fn` の既定値を `false` に変更** ([#354](https://github.com/touying-typ/touying/pull/354))
  - `#speaker-note[]` は、明示的なスライド呼び出し、見出しから生成されたセクションスライド、通常の内容スライドのいずれでも、常に**直前のスライド**へ関連付けられます。スライドの後に記述したノートが意図しない空の「ゴーストスライド」を生成する問題を防ぎます。
  - `receive-body-for-new-section-slide-fn` と関連する設定の**既定値が `true` から `false` に変更**されました。

### 移行ガイド

`= Section` の後の内容がセクションスライドの本文に入る動作に依存していた場合は、`config-common(...)` で `receive-body-for-new-section-slide-fn: true` を明示してください。

### 機能追加

- **機能：`item-by-item-fn` とそのプリセットを追加** ([#347](https://github.com/touying-typ/touying/pull/347))
- **機能：`custom-progressive-outline` の改善、`section-relationship` などを追加** ([#345](https://github.com/touying-typ/touying/pull/345))
- **機能：混在したレイアウトでの `lazy-layout` を改善** ([#355](https://github.com/touying-typ/touying/pull/355))
- **機能：`side-by-side` の別名 `cols` を追加し、`cols` や `lazy-xxx` などのコンポーネントを公開** ([#356](https://github.com/touying-typ/touying/pull/356))
- **metropolisテーマ：`outline-slide` を追加** ([#349](https://github.com/touying-typ/touying/pull/349))
- **機能：スライドの内容の高さが空の場合に警告を追加**

### ドキュメント

- **ドキュメント：複数列の例を追加し、構造を改善**

## v0.7.1

### 機能追加

- **機能（エージェント）：スライドのはみ出しを防ぐ `breakable` と `clip` オプションを追加** ([#336](https://github.com/touying-typ/touying/pull/336))
- **機能（コンポーネント）：複数列・行のブロックの高さ・幅を揃える `lazy-v`（`lazy-h`）と `lazy-layout` を追加** ([#339](https://github.com/touying-typ/touying/pull/339))
- **機能：`config-info` に `contact` と `extra` フィールドを追加** ([#342](https://github.com/touying-typ/touying/pull/342))
- **機能：`touying-get-config` 関数を追加** ([#333](https://github.com/touying-typ/touying/pull/333))

### 修正

- **修正：`fit-to-height` と `size-to-pt` を修正し、テキストの再配置に対応** ([#332](https://github.com/touying-typ/touying/pull/332))
- **修正：ウェイポイントマーカー** ([#341](https://github.com/touying-typ/touying/pull/341))


## v0.7.0

### 機能追加

- **主要機能：名前付きウェイポイント** ([#298](https://github.com/touying-typ/touying/pull/298))
- **機能（ウェイポイント）：`start` パラメーターと配布資料のサブスライド指定への対応** ([#304](https://github.com/touying-typ/touying/pull/304))
- **機能：サブスライド番号とウェイポイントに `auto`、現在位置を表す `"h"`、反転指定を追加** ([#301](https://github.com/touying-typ/touying/pull/301))
- **機能：reducerを通じて関数ラッパーによるアニメーションを暗黙に利用可能に** ([#300](https://github.com/touying-typ/touying/pull/300))

### 修正

- **修正：一部を隠した長い行を `cover-with-rect` が分割する問題と、色・アルファ値のカバーの代替処理** ([#328](https://github.com/touying-typ/touying/pull/328))
- **修正：`style=auto` の `display-current-heading` で明示的な番号付けを使用** ([#329](https://github.com/touying-typ/touying/pull/329))
- **修正：showルールによるゴーストスライドを解消し、showルールとdeferキーワードの扱いを統一** ([#317](https://github.com/touying-typ/touying/pull/317))
- **修正：alertの適用が遅延しないように変更** ([#316](https://github.com/touying-typ/touying/pull/316))
- **修正：重複した入れ子のtext呼び出しを削除** ([#324](https://github.com/touying-typ/touying/pull/324))
- **修正：`alternatives-match` が `stretch` パラメーターを考慮するように変更** ([#320](https://github.com/touying-typ/touying/pull/320))
- **修正：ページマージンの統合と優先順位の扱い** ([#322](https://github.com/touying-typ/touying/pull/322))
- **修正：リスト周辺のカバーの間隔** ([#303](https://github.com/touying-typ/touying/pull/303))
- **修正：配布資料のサブスライド指定で負の番号（整数・配列）を正しく解析** ([#307](https://github.com/touying-typ/touying/pull/307))
- **修正：スコープ付きインポートを介してスライド関数が更新されない問題** ([#310](https://github.com/touying-typ/touying/pull/310))

以下の方々の貢献に感謝します： [@zral0kh](https://github.com/zral0kh), [@Andrew15-5](https://github.com/Andrew15-5), [@navdeeprana](https://github.com/navdeeprana), and [@Cemoixerestre](https://github.com/Cemoixerestre).

## v0.6.3

長年残っていた多くの不具合を修正し、実用的な機能を多数追加した大規模な修正リリースです。

### 機能追加

- **機能：統一的なアニメーション制御として `#jump(n, relative: bool)` を追加し、`#pause` と `#meanwhile` をその簡略構文として再定義**
- **機能：インライン内容向けの `#handout-only` と配布資料専用スライド向けの `<touying:handout>` ラベルを追加** ([#286](https://github.com/touying-typ/touying/pull/286))
- **機能：配布資料モードに出力するサブスライドを指定する `handout-subslides` を追加** ([#288](https://github.com/touying-typ/touying/pull/288))
- **機能：コードブロックを段階的に表示する `#touying-raw` を追加** ([#283](https://github.com/touying-typ/touying/pull/283))
- **機能：スライドのサムネイル付きの全画面発表者ノートモード（`show-only-notes`）を追加** ([#281](https://github.com/touying-typ/touying/pull/281))
- **機能：すべてのテーマと発表者ノートのセカンドスクリーンで16:10など任意の縦横比に対応** ([#280](https://github.com/touying-typ/touying/pull/280))
- **機能：list、enum、terms向けの `#item-by-item` アニメーションを追加** ([#278](https://github.com/touying-typ/touying/pull/278))
- **機能（再利用）：`#touying-recall` にサブスライド指定のパラメーターを追加** ([#285](https://github.com/touying-typ/touying/pull/285))
- **機能：スライド全体のレイアウトを設定する `default-composer` をconfig-commonに追加** ([#284](https://github.com/touying-typ/touying/pull/284))
- **機能：Fletcherなどの外部パッケージ連携向けに `uncover` へ `cover-fn` を追加** ([#267](https://github.com/touying-typ/touying/pull/267))
- **機能：ミニスライドをインライン表示可能に** ([#228](https://github.com/touying-typ/touying/pull/228))
- **テーマ：universityとstargazerで長い著者一覧の表示を改善** ([#242](https://github.com/touying-typ/touying/pull/242))
- **simpleテーマ：装飾形式で配色設定を反映するよう修正** ([#252](https://github.com/touying-typ/touying/pull/252))
- **aqua・stargazerテーマ：title-slideにextraパラメーターを追加** ([#291](https://github.com/touying-typ/touying/pull/291))

### 修正

- **修正：`touying-set-config` のアンカーの回帰によってゴーストスライドが生成される問題を防止** ([#289](https://github.com/touying-typ/touying/pull/289))
- **修正：最初のスライドの装飾付き内容によって余分なスライドが生成される問題** ([#287](https://github.com/touying-typ/touying/pull/287))
- **修正：目次から除外した見出しをナビゲーションからも除外**
- **修正：グリッドのセル、ボックスなどのコンテナー内で `#meanwhile` が無視される問題** ([#274](https://github.com/touying-typ/touying/pull/274))
- **修正：全テーマで `config:` パラメーターが黙って無視される問題** ([#273](https://github.com/touying-typ/touying/pull/273))
- **修正：`#show`・`#set` ルールの後のスライドが描画されない問題** ([#268](https://github.com/touying-typ/touying/pull/268))
- **修正：タイトルページのPDFページラベルによってpdfpcの発表者ノートがずれる問題** ([#277](https://github.com/touying-typ/touying/pull/277))
- **修正：`#pause` によるアニメーション中のラベル付き脚注でラベル重複エラーが発生する問題** ([#275](https://github.com/touying-typ/touying/pull/275))
- **修正：`#speaker-note` の本文内（入れ子のリスト項目）にある `#pause` の動作** ([#282](https://github.com/touying-typ/touying/pull/282))
- **dewdropテーマ：第1レベルの見出しの下の本文が黙って削除される問題を修正** ([#279](https://github.com/touying-typ/touying/pull/279))
- **stargazerテーマ：マージンを更新し、問題を修正** [#259](https://github.com/touying-typ/touying/pull/259)

### ドキュメント

- **ドキュメント（大きな変更）：ドキュメントサイトを再構成し、リファレンスページを追加**
- **ドキュメント：READMEの冗長な部分を減らし、第一印象を改善** ([#297](https://github.com/touying-typ/touying/pull/297))
- **ドキュメント：構成を整理し、PR向けのプレビューCIを追加** ([#296](https://github.com/touying-typ/touying/pull/296))
- **ドキュメント：すべてのソースファイルでdocstringを包括的に改善** ([#294](https://github.com/touying-typ/touying/pull/294))

### その他

- **保守：`copilot-setup-steps.yml` を追加し、`copilot-instructions.md` を改善** ([#292](https://github.com/touying-typ/touying/pull/292))

### テーマの移行ガイド

**v0.6.3へ移行するテーマ作者向け：**

1. **利用者側で上書きできるよう `utils.merge-dicts` の最後に `config` を移動します。**
   ```typst
   // Before
   self = utils.merge-dicts(self, config, config-page(...))
   
   // After
   self = utils.merge-dicts(self, config-page(...), config)
   ```

2. **任意の縦横比に対応するため `paper` を `utils.page-args-from-aspect-ratio` に置き換えます。**
   ```typst
   // Before
   config-page(paper: "presentation-" + aspect-ratio, ...)
   
   // After
   config-page(..utils.page-args-from-aspect-ratio(aspect-ratio), ...)
   ```

## v0.6.2

### 機能追加

- **機能：`components.checkerboard` のカスタマイズに対応** ([#161](https://github.com/touying-typ/touying/pull/161))

### 修正

- **修正：ページ幅全体を使うヘッダーで比率や相対マージンをサポート** ([#256](https://github.com/touying-typ/touying/pull/256))
- **修正：Typst 0.14での `magic.bibliography-as-footnote` の動作** ([#249](https://github.com/touying-typ/touying/pull/249))
- **修正：Typst 0.14.0でTheorionパッケージが動作しない問題** ([#237](https://github.com/touying-typ/touying/pull/237))
- **修正：`components.typ` を更新し、gridに名前付き引数を渡すよう変更** ([#207](https://github.com/touying-typ/touying/pull/207))
- **修正：CeTZ内の `#meanwhile` の動作** ([#205](https://github.com/touying-typ/touying/pull/205))
- **修正：ドキュメント内のrawテキストが閉じられていないエラー** ([#187](https://github.com/touying-typ/touying/pull/187))
- **修正：正しい円の記号を使用** ([#171](https://github.com/touying-typ/touying/pull/171))
- **修正：正規表現を使って数式の色を上書き** ([#167](https://github.com/touying-typ/touying/pull/167))
- **修正：完全なenumでの `show-hide-set-list-marker-none` の動作** ([#157](https://github.com/touying-typ/touying/pull/157))
- **修正：キャッシュの改善のためdumpとlabel-it関数を削除**

### その他

- **ドキュメント：READMEの更新、依存関係の更新、コメントの説明の修正**
- **CI：テストを追加し、`tytanic` のバージョンとtypstyleワークフローを更新** ([#221](https://github.com/touying-typ/touying/pull/221), [#261](https://github.com/touying-typ/touying/pull/261))


## v0.6.1

[Theorion](https://github.com/OrangeX4/typst-theorion) パッケージに対応し、数式の定理環境の既定値として採用しました。

## v0.6.0

大きな更新ではありませんが、Typst 0.13の公開後、最初のTouyingのリリースです。

### 機能追加

- **機能：display-current-headingに自動スタイルを追加**
  - 利用者は `show heading: set text(blue)` でdewdropなどのテーマの見出し色を変更できます。
  - テーマ作者は `utils.display-current-heading(level: 1, style: auto)` のようにして同じ効果を実現できます。
- **機能：config-infoの情報を `set document` に適用**
- **機能：`alternatives` の `stretch` の既定値を `false` に変更しました。** 軽微な破壊的変更ですが、自動的に空白を作らない動作の方が直感的です。

### 修正

- **修正：semi-transparent-coverを使うuncoverでのエラー**
- **修正：型を表す文字列の比較** https://github.com/touying-typ/touying/pull/153
- **修正：Typst 0.13.0での水平線の不具合**
- **リファクタリング：display-current-short-headingの動作を修正**


## v0.5.4 & v0.5.5

### 機能追加

- docs: improve param documentation and we have better hints for tinymist https://github.com/touying-typ/touying/pull/98
- feat: fake frozon states support for `heading` https://github.com/touying-typ/touying/pull/124
- feat: add alpha-changing-cover and color-changing-cover https://github.com/touying-typ/touying/pull/129
- feat: add effect function https://github.com/touying-typ/touying/issues/111
  - Example: `#effect(text.with(fill: red), "2-")[Something]` will display `[Something]` if the current slide is 2 or later.
- feat: add argument `config: (..)` for `xxx-slide` functions
- feat: add `align` argument for university theme

### 修正

- fix: also hide enum numbers with show-hide-set-list-marker-none https://github.com/touying-typ/touying/pull/114
- fix: fixed progress bar not to break apart when global figure gutter is set nonzero https://github.com/touying-typ/touying/pull/120
- fix: fixed frozen-counters bug with multiple #pause commands https://github.com/touying-typ/touying/pull/124
- fix: fixed incorrect page num when draft is true https://github.com/touying-typ/touying/pull/125
- fix: fix behaviors of fit-to-height and fit-to-width partially https://github.com/touying-typ/touying/pull/131
- fix: duplicated footnotes in headings https://github.com/touying-typ/touying/pull/132
- fix: do not hardcode page sizes https://github.com/touying-typ/touying/pull/134
- fix: add default numbering for page https://github.com/touying-typ/touying/issues/100
- refactor: move show-strong-with-alert to per-slide level https://github.com/touying-typ/touying/issues/123
- refactor: remove unnecessary `config-page(fill: ...)`
- theme(metropolis): fix color of title page and fix https://github.com/touying-typ/touying/issues/103
- theme(metropolis): fixed metropolis slide's header to return content if title is specified https://github.com/touying-typ/touying/pull/126
- theme(metropolis): respect colors dict in metropolis theme https://github.com/touying-typ/touying/pull/133
- fix: fix bug of `#effect` function

以下の方々の貢献に感謝します： [@enklht](https://github.com/enklht).


## v0.5.3

### 機能追加

- feat: add `stretch` parameter for `#alternatives[]` function class. This allows us to handle cases where the internal element is a context expression.
- feat: add `config-common(align-enum-marker-with-baseline: true)` for aligning the enum marker with the baseline.
- feat: add `linebreaks` option to `components.mini-slides`. https://github.com/touying-typ/touying/pull/96
- feat: add `<touying:skip>` label to skip a new-section-slide.
- feat: add `config-common(show-hide-set-list-marker-none: true)` to make the markers of `list` and `enum` invisible after `#pause`.
- feat: add `config-common(bibliography-as-footnote: bibliography(title: none, "ref.bib"))` to display the bibliography in footnotes.
- refactor: add `config-common(show-strong-with-alert: true)` configuration to display strong text with an alert. (small breaking change for some themes)
- refactor: refactor `display-current-heading` for preserving heading style in title and subtitle. https://github.com/touying-typ/touying/issues/71
- refactor: make `new-section-slide-fn` function class can receive `body` parameter. We can use `receive-body-for-new-section-slide-fn` to control it. **(Breaking change)**
  - For example, you can add `#speaker-note[]` for a new section slide, like `= Section Title \ #speaker-note[]`.
  - If you don't want to append content to the body of the new section slide, you can use `---` after the section title.

### 修正

- fix outdated documentation.
- fix bug of `enable-frozen-states-and-counters` in handout mode.
- fix unusable `square()` function. https://github.com/touying-typ/touying/issues/73
- fix hidden footer for `show-notes-on-second-screen: bottom`. https://github.com/touying-typ/touying/issues/89
- fix metadata element in table cells. https://github.com/touying-typ/touying/issues/77 https://github.com/touying-typ/touying/issues/95
- fix `auto-offset-for-heading` to `false` by default.
- fix uncover/only hides more content than it should. https://github.com/touying-typ/touying/issues/85
- theme(simple): fix wrong title and subtitle. https://github.com/touying-typ/touying/issues/70


## v0.5.1 & v0.5.2

- Fix somg bugs.
- `nontight-list-enum-and-terms` now defaults to `false`

## v0.5.0

This is a significant disruptive version update. Touying has removed many mistakes that resulted from incorrect decisions. We have redesigned numerous features. The goal of this version is to make Touying more user-friendly, more flexible, and more powerful.

**Major changes include:**

- Avoiding closures and OOP syntax, which makes Touying's configuration simpler and allows for the use of document comments to provide more auto-completion information for the slide function.
  - The existing `#let slide(self: none, ..args) = { .. }` is now `#let slide(..args) = touying-slide-wrapper(self => { .. })`, where `self` is automatically injected.
  - We can use `config-xxx` syntax to configure Touying, for example, `#show: university-theme.with(aspect-ratio: "16-9", config-colors(primary: blue))`.
- The `touying-slide` function no longer includes parameters like `section`, `subsection`, and `title`. These will be automatically inserted into the slide as invisible level 1, 2, or 3 headings via `self.headings` (controlled by the `slide-level` configuration).
  - We can leverage the powerful headings provided by Typst to support numbering, outlines, and bookmarks.
  - Headings within the `#slide[= XXX]` function will be adjusted to level `slide-level + 1` using the `offset` parameter.
  - We can use labels on headings to control many aspects, such as supporting  the `<touying:hidden>` and other special labels, implementing short headings, or recalling a slide with `#touying-recall()`.
- Touying now supports the normal use of `set` and `show` rules at any position, without requiring them to be in specific locations.

A simple usage example is shown below, and more examples can be found in the `examples` directory:

```typst
#import "@preview/touying:0.5.0": *
#import themes.university: *

#show: university-theme.with(
  aspect-ratio: "16-9",
  config-info(
    title: [Title],
    subtitle: [Subtitle],
    author: [Authors],
    date: datetime.today(),
    institution: [Institution],
    logo: emoji.school,
  ),
)

#set heading(numbering: "1.1")

#title-slide()

= The Section

== Slide Title

#lorem(40)
```

**Theme Migration Guide:**

For detailed changes to specific themes, you can refer to the `themes` directory. Generally, if you want to migrate an existing theme, you should:

1. Rename the `register` function to `xxx-theme` and remove the `self` parameter.
2. Add a `show: touying-slides.with(..)` configuration.
   - Change `self.methods.colors` to `config-colors(primary: rgb("#xxxxxx"))`.
   - Change `self.page-args` to `config-page()`.
   - Change `self.methods.slide = slide` to `config-methods(slide: slide)`.
   - Change `self.methods.new-section-slide = new-section-slide` to `config-methods(new-section-slide: new-section-slide)`.
   - Change private theme variables like `self.xxx-footer` to `config-store(footer: [..])`, which you can access through `self.store.footer`.
   - Move the configuration of headers and footers into the `slide` function rather than in the `xxx-theme` function.
   - You can directly use `set` or `show` rules in `xxx-theme` or configure them through `config-methods(init: (self: none, body) => { .. })` to fully utilize the `self` parameter.
3. For `states.current-section-with-numbering`, you can use `utils.display-current-heading(level: 1)` instead.
   - If you only need the previous heading regardless of whether it is a section or a subsection, use `self => utils.display-current-heading(depth: self.slide-level)`.
4. The `alert` function can be replaced with `config-methods(alert: utils.alert-with-primary-color)`.
5. The `touying-outline()` function is no longer needed; you can use `components.adaptive-columns(outline())` instead. Consider using `components.progressive-outline()` or `components.custom-progressive-outline()`.
6. Replace `states.slide-counter.display() + " / " + states.last-slide-number` with `context utils.slide-counter.display() + " / " + utils.last-slide-number`. That is, we no longer use `states` but `utils`.
7. Remove the `slides` function; we no longer need this function. Instead of implicitly injecting `title-slide()`, explicitly use `#title-slide()`. If necessary, consider adding it in the `xxx-theme` function.
8. Change `#let slide(self: none, ..args) = { .. }` to `#let slide(..args) = touying-slide-wrapper(self => { .. })`, where `self` is automatically injected.
   - Change specific parameter configurations to `self = utils.merge-dicts(self, config-page(fill: self.colors.neutral-lightest))`.
   - Remove `self = utils.empty-page(self)` and use `config-common(freeze-slide-counter: true)` and `config-page(margin: 0em)` instead.
   - Change `(self.methods.touying-slide)()` to `touying-slide()`.
9. You can insert visible headings into slides by configuring `config-common(subslide-preamble: self => text(1.2em, weight: "bold", utils.display-current-heading(depth: self.slide-level)))`.
10. Finally, don't forget to add document comments to your functions so your users can get better auto-completion hints, especially when using the Tinymist plugin.

**Other Changes:**

- theme(stargazer): new stargazer theme modified from [Coekjan/touying-buaa](https://github.com/Coekjan/touying-buaa).
- feat: implemented fake frozen states support, allowing you to use numbering and `#pause` normally. This behavior can be controlled with `enable-frozen-states-and-counters`, `frozen-states`, and `frozen-counters` in `config-common()`.
- feat: implemented `label-only-on-last-subslide` functionality to prevent non-unique label warnings when working with `@equation` and `@figure` in conjunction with `#pause` animations.
- feat: added the `touying-recall(<label>)` function to replay a specific slide.
- feat: implemented `nontight-list-enum-and-terms`, which defaults to `true` and forces `list`, `enum`, and `terms` to have their `tight` parameter set to `false`. You can control spacing size with `#set list(spacing: 1em)`.
- feat: replaced `list` with `terms` implementation to achieve `align-list-marker-with-baseline`, which is off by default.
- feat: implemented `scale-list-items`, scaling list items by a factor, e.g., `scale-list-items: 0.8` scales list items by 0.8.
- feat: supported direct use of `#pause` and `#meanwhile` in math expressions, such as `$x + pause y$`.
- feat: provided `#pause` and `#meanwhile` support for most layout functions, such as `grid` and `table`.
- feat: added `#show: appendix` support, essentially equivalent to `#show: touying-set-config.with((appendix: true))`.
- feat: Introduced special labels `<touying:hidden>`, `<touying:unnumbered>`, `<touying:unoutlined>`, `<touying:unbookmarked>` to simplify control over heading behavior.
- feat: added basic `utils.short-heading` support to display short headings using labels, such as displaying `<sec:my-section>` as "My Section".
- feat: added `#components.adaptive-columns()` to achieve adaptive columns that span a page, typically used with the `outline()` function.
- feat: added `#show: magic.bibliography-as-footnote.with(bibliography("ref.bib"))` to display the bibliography in footnotes.
- feat: added components like `custom-progressive-outline`, `mini-slides`.
- feat: removed `touying-outline()`, which can be directly replaced with `outline()`.
- fix: replaced potentially incompatible code, such as `type(s) == "string"` and `locate(loc => { .. })`.
- fix: Fixed some bugs.


## v0.4.2

- theme(metropolis): decoupled text color with `neutral-dark` (Breaking change)
- feat: add mark-style uncover, only and alternatives
- feat: add warning for styled block for slides
- feat: add warning for touying-temporary-mark
- feat: add markup-text for speaker-note
- fix: fix bug of slides


## v0.4.1

### 機能追加

- feat: support builtin outline and bookmark
- feat: support speaker note for dual-screen
- feat: add touying-mitex function

### 修正

- fix: add outline-slide for dewdrop theme
- fix: fix regression of default value "auto" for repeat

### その他の改善

- feat: add list support for `touying-outline` function
- feat: add auto-reset-footnote
- feat: add `freeze-in-empty-page` for better page counter
- feat: add `..args` for register method to capture unused arguments


## v0.4.0

### 機能追加

- **feat:** support `#footnote[]` for all themes.
- **feat:** access subslide and repeat in footer and header by `self => self.subslide`.
- **feat:** support numbered theorem environments by [ctheorems](https://typst.app/universe/package/ctheorems).
- **feat:** support numbering for sections and subsections.

### 修正

- **fix:** make nested includes work correctly.
- **fix:** disable multi-page slides from creating the same section multiple times.

## 破壊的変更

- **refactor:** remove `self.padding` and add `self.full-header` `self.full-footer` config.


## v0.3.3

- **template:** move template to `touying-aqua` package, make Touying searchable in [Typst Universe Packages](https://typst.app/universe/search?kind=packages)
- **themes:** fix bugs in university and dewdrop theme
- **feat:** make set-show rule work without `setting` parameter
- **feat:** make `composer` parameter more simpler
- **feat:** add `empty-slide` function

## v0.3.2

- **fix critical bug:** fix `is-sequence` function, make `grid` and `table` work correctly in touying
- **theme:** add aqua theme, thanks for pride7
- **theme:** make university theme more configurable
- **refactor:** don't export variable `s` by default anymore, it will be extracted by `register` function (**Breaking Change**)
- **meta:** add `categories` and `template` config to `typst.toml` for Typst 0.11


## v0.3.1

- fix some typos
- fix slide-level bug
- fix bug of pdfpc label


## v0.3.0

### 機能追加

- better show-slides mode.
- support align and pad.

### ドキュメント

- Add more detailed documentation.

### リファクタリング

- simplify theme.

### 修正

- fix many bugs.

## v0.2.1

### 機能追加

- **Touying-reducer**: support cetz and fletcher animation
- **university theme**: add university theme

### 修正

- fix footer progress in metropolis theme
- fix some bugs in simple and dewdrop themes
- fix bug that outline does not display more than 4 sections


## v0.2.0

- **Object-oriented programming:** Singleton `s`, binding methods `utils.methods(s)` and `(self: obj, ..) => {..}` methods.
- **Page arguments management:** Instead of using `#set page(..)`, you should use `self.page-args` to retrieve or set page parameters, thereby avoiding unnecessary creation of new pages.
- **`#pause` for sequence content:** You can use #pause at the outermost level of a slide, including inline and list.
- **`#pause` for layout functions:** You can use the `composer` parameter to add yourself layout function like `utils.side-by-side`, and simply use multiple pos parameters like `#slide[..][..]`.
- **`#meanwhile` for synchronous display:** Provide a `#meanwhile` for resetting subslides counter.
- **`#pause` and `#meanwhile` for math equation:** Provide a `#touying-equation("x + y pause + z")` for math equation animations.
- **Slides:** Create simple slides using standard headings.
- **Callback-style `uncover`, `only` and `alternatives`:** Based on the concise syntax provided by Polylux, allow precise control of the timing for displaying content.
  - You should manually control the number of subslides using the `repeat` parameter.
- **Transparent cover:** Enable transparent cover using oop syntax like `#let s = (s.methods.enable-transparent-cover)(self: s)`.
- **Handout mode:** enable handout mode by `#let s = (s.methods.enable-handout-mode)(self: s)`.
- **Fit-to-width and fit-to-height:** Fit-to-width for title in header and fit-to-height for image.
  - `utils.fit-to-width(grow: true, shrink: true, width, body)`
  - `utils.fit-to-height(width: none, prescale-width: none, grow: true, shrink: true, height, body)`
- **Slides counter:** `states.slide-counter.display() + " / " + states.last-slide-number` and `states.touying-progress(ratio => ..)`.
- **Appendix:** Freeze the `last-slide-number` to prevent the slide number from increasing further.
- **Sections:** Touying's built-in section support can be used to display the current section title and show progress.
  - `section` and `subsection` parameter in `#slide` to register a new section or subsection.
  - `states.current-section-title` to get the current section.
  - `states.touying-outline` or `s.methods.touying-outline` to display a outline of sections.
  - `states.touying-final-sections(sections => ..)` for custom outline display.
  - `states.touying-progress-with-sections((current-sections: .., final-sections: .., current-slide-number: .., last-slide-number: ..) => ..)` for powerful progress display.
- **Navigation bar**: Navigation bar like [here](https://github.com/zbowang/BeamerTheme) by `states.touying-progress-with-sections(..)`, in `dewdrop` theme.
- **Pdfpc:** pdfpc support and export `.pdfpc` file without external tool by `typst query` command simply.
