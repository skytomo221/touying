---
sidebar_position: 8
---

# 変更履歴

## v0.8.1

0.8.0で導入された動作の一部を調整し、不具合を修正したパッチリリースです。

### 動作の変更

- **インラインの内容に挟まれた全角ダッシュでスライドが分割されなくなりました**

  `—` や `---` の両側にインラインの内容がある場合、たとえば `Text — `code`` のような場合は、文の一部として保持されます。以前は水平線として解釈され、新しいスライドが始まり、ダッシュが削除されていました。インラインの内容とはテキスト、`~` のような記号、raw、数式、強調、リンク、参照、文献引用、脚注、ボックスなどです。空行、見出し、その他のブロック、あるいは `#pause` や `#alert[..]` のようなTouyingのマーカーやラッパーの隣にあるダッシュでは、引き続きスライドが分割されます。

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
- `touying-set-config` の本文が最初の見出し、`#slide`、改ページより前にある場合に、内容が削除されたり、誤ったスライドに統合されたりすることがなくなりました。
- スライド呼び出しの直後にあるラベル（`#slide[..]<name>`）が、見出しラベルと同様にスライドに付くようになりました。`#link(<name>)` は最初のサブスライドに移動し、プレゼンテーション・配布資料・記事の各モードで `<name.waypoint>` というウェイポイントアンカーが利用できます。また、記事モードではウェイポイントが最後のサブスライドで終わらなくてもアンカーが失われなくなりました。
- スライド全体を再利用しても、そのスライドのラベルが再度出力されることはなくなりました。各コピーにはデフォルトで `<og-label-1>`、`<og-label-2>`、…（および `<og-label-1.waypoint>`）のようにカウンター付きの新しいラベルが割り当てられます。`#touying-recall(<intro>)<intro-again>` と書けば、新しいラベルを指定できます。これは再利用される内容にも適用されるため、たとえば特定の1つのサブスライドで再利用した図を参照できます。新しいラベルを指定せずに内容を再利用すると、単にラベルなしで出力されます。
- スライド呼び出しの外側にある `#heading[..]` が `field "depth" in heading is not known at this point` で失敗する問題を修正しました（#434、#437）。
- キャプションのない `#figure` が `#pause` で隠されていても、`field "caption" in figure is not known at this point` で失敗しなくなりました（#437）。
- `animate`、`animate-hidden`、`animate-removed`、`item-by-item-fn` が `self.methods` と `utils.methods(self)` を通じてデフォルトメソッドの一覧に含まれるようになりました。`uncover` と `only` と同様です。
- `capitalize` と `titlecase` が非ASCII文字列にも対応しました（#436）。
- reducerが内容の最小反復回数と関数ラッパーを通知するようになりました（#431）。
- pauseされたリスト項目が表示されても、整列済みの内容がずれなくなりました（#442）。
- リストの `measure` が行間隔を適用するようになり、show-setルールが有効になりました（#443）。
- `#pause` を含む番号付きリストで番号が揃うようになりました。非表示部分と表示部分の番号列の幅が一致し、後から項目を表示しても横方向にずれません（#438）。
- `touying-mitex` が非表示の改行部分の高さを保持するようになり、次の行を表示しても既存の行が横方向へずれなくなりました。
- リスト項目の間にある `#meanwhile` は、先行する `#pause` があってもリストを分割しなくなったため、次のサブスライドが表示されても行が移動しなくなりました（#439）。
- リスト、番号付きリスト、用語一覧の一部をカバーしても、コンテナーはまとまったままになります。`#meanwhile` の後の項目と、空行の後のカバーされた番号付きリストの項目は、0.7.0以前と同様に番号、番号列、詰め具合、行間隔を維持します。`#pause` で分割された番号付きリストも `reversed`、`start`、明示的な番号を維持し、`show enum: set enum(numbering: ..)` や `number-align` のルールが各部分にも適用されます。可視の `separator` が `terms` のカバーされた部分のために確保する間隔を広げることもなくなりました。
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

主な新機能は**記事モード**です。同じソースファイルをスライドにも連続した文章の文書にもコンパイルでき、アニメーションは最終状態にまとめられ、画像は必要に応じて横に回り込ませられます。それに伴い、**2つの新しいアニメーション**のエントリーポイントが追加されます。`touying-render` は指定されたアニメーション段階で内容を描画し、`animate` は1つの内容に可視性とスタイルを組み合わせます。`touying-recall` はスライド全体から任意のラベル付き要素へと拡張されました。`touying-fn-wrapper-raw` は本文を解析し入れ子にできるようになったため、`#alert[.. #pause ..]` が動作し、それ以外にも多くのことが可能になりました。`.pdfpc` ファイルは別途 `typst query` を実行する代わりにバンドルのアセットとして直接書き出せます。脚注形式の参考文献はTypstの実際の参考文献機能に基づいて再構築され、発表者ノートのパネルはテーマが提供する関数になり、それに対応するドキュメントも整備されました。内部では `src/core.typ`（6288行）がモジュールに分割され、内容の処理が統一され、いくつかの関数（主にutilsのもの）の配置が変更されました。

アップグレード時の注意点：`#pause` は `uncover`・`only`・`alternatives` の後に置いても、これらが確保するサブスライドを飛ばさなくなりました（v0.7.0では誤って飛ばしていました）。脚注形式の参考文献の設定形式が変更され、`semi-transparent-cover` は非推奨となり、セカンドスクリーンでのページ背景はスライド側の半分に限定されます。以下に移行ガイドがあり、末尾にはテーマ作者向けの別のガイドもあります。ドキュメントの大部分は書き直しまたは修正されました。

### 破壊的変更

- **機能・破壊的変更：TouyingはTypst 0.15.0以降を必須とします**

  `typst.toml` の `compiler` が `0.12.0` から `0.15.0` に変更されました。`.pdfpc` のバンドル出力と新しいカバーメソッドに必要です。リファレンス画像はTypst 0.15とtytanic 0.4で再生成されました。

- **修正・破壊的変更：`#pause` が `uncover`・`only`・`alternatives` の後にある場合に、これらが確保したサブスライドを飛ばさなくなりました**

  ```typst
  #uncover("2")[Only on subslide 2]
  On subslide one
  #pause
  On subslide two    // v0.7.0-v0.7.4: サブスライド*3*で表示
  ```

  v0.6.xまでは、相対ジャンプによってカウンターが1つ進んでいました。v0.7.0では、先行する関数ラッパーが確保した最大のサブスライドの次まで一気に進むように変更されました。これは `item-by-item` のための変更でした。これは連続するpauseの代わりとなり、後続の `#pause` をアニメーション全体の後まで進めるべきものです。しかし `item-by-item` は他のすべてのラッパーと同じ経路で自身の範囲を通知するため、変更は `uncover`、`only`、`alternatives` にも及びました。この動作を検証するテストがなかったため見逃されていました。サブスライドの確保と消費は再び別々の操作になりました。

  ウェイポイントの配置にも同じ修正が加えられました。`#waypoint(<w>)` を `#uncover("5")` の後に置くと、サブスライド5の次ではなく、現在の進行位置の1つ後に配置されるようになりました。

  **移行方法：** 以前の自動ジャンプに依存していた場合は、追加の `#pause`（または `#jump(n, relative: true)` やウェイポイント）を自分で記述してください。`item-by-item` と `item-by-item-fn` に影響はありません。後続のpauseを引き続き最後の項目より後へ進めます。また、これらは省略形の元となる手書きの `#pause` の連続と一致するようになりました。後続の内容の前に `#pause` がない場合も含みます。

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

  `magic.bibliography-as-footnote` から `bibliography` パラメーターが削除され、`footnote-style` が追加されました。これは引用マーカー用の `super` のプロパティを指定する辞書です。デフォルトの `(typographic: false, baseline: 0em, size: 1em)` では通常のテキストとして読めるようになります。

- **機能・破壊的変更：発表者ノートのパネルを `config-common(notes-fn: ..)` でカスタマイズ可能になり、`config-methods(show-only-notes: ..)` の*描画関数*が削除されました** ([#353](https://github.com/touying-typ/touying/issues/353))

  v0.7.4では `show-only-notes` に2種類のオプションがあり、変更されたのはその一方だけです。発表者ビューを有効にする `config-common(show-only-notes: true)` は従来どおり動作し、[発表者ノート](https://touying-typ.github.io/docs/tutorials/speaker-notes)で初めて説明されました。削除されたのはパネルの描画関数 `config-methods(show-only-notes: ..)` であり、主にテーマ作者が上書きしていました。

  パネルは `slide-fn` がスライドを作るのと同様に、テーマが提供する関数で生成されます。`notes-fn(self: none, note: none, slide-preview: none) -> content` は `slide-fn` に対応し、`touying-notes` は `touying-slide` に対応します。レイアウトの基本を担い、テーマ側が色や配置を指定します。組み込みの6テーマはすべて独自の関数を登録しているため、ノートの見た目もスライドと統一されます。一部の配置の不具合は今後修正予定です。

  `show-only-notes` モードも再構成されました。以前は右上にあったスライドの各部分がページサイズの1つのボックスにまとめられ、`slide-preview` としてテーマに渡されます。テーマ側で自由に配置と縮尺を指定できます。従来の `cutout`、`cutout-height`、`(background, foreground)` を返す方式は削除され、`slide-preview: none` はセカンドスクリーンであることを示します。また、パネルの背景がページヘッダーから配置され、ほぼ全体がページ外に出て `#CCCCCC` と `#E6E6E6` が描画されなかった問題 [#281](https://github.com/touying-typ/touying/issues/281) も修正されました。

  詳しくは[発表者ノートのパネルのカスタマイズ](https://touying-typ.github.io/docs/themes/custom)と[独自テーマを作成する](https://touying-typ.github.io/docs/tutorials/build-your-own-theme)を参照してください。

- **修正・破壊的変更：セカンドスクリーンで背景と前景がスライド側の半分に限定されました** ([#219](https://github.com/touying-typ/touying/issues/219))

  `show-notes-on-second-screen` ではページを1方向に2倍へ拡張し、追加した半分をマージンへ配置します（現在は4方向に対応します）。以前は `page(background: ..)` がページ全体を覆い、`100%` の背景や `fit: "cover"` の画像がノート側まで引き伸ばされていました。現在は `background` と `foreground` をそれぞれスライド側の1ページ分のボックスに配置するため、割合指定や `fit: "cover"` はセカンドスクリーンなしの場合と同様に動作します。

- **機能・破壊的変更：`touying-recall` の `subslide:` が `subslides:` に変更され、`none` と `auto` の意味が入れ替わりました**

  このパラメーターは単一のサブスライドではなく範囲を受け取るため（v0.6.3以降）、複数形になりました。`visible-subslides` と同じ形式で、これは `uncover`、`only`、`effect` のパラメーターです。従来の値を包含する、より広い範囲の値を受け付けます。

  新しい既定値は `subslides: auto` です。スライド全体が対象ならすべてのサブスライドを、ラベル付き要素ならアニメーションの最終状態を再利用します。`subslides: none` は最後のサブスライドだけを再利用します。v0.7.4では逆で `none` が既定値だったため、既定動作自体は変わりません。明示的に指定していたコードだけがキー名と値の入れ替えを必要とします。

### 軽微な破壊的変更

- **機能・破壊的変更：`utils.semi-transparent-cover` は非推奨になりました** — 代わりに `utils.alpha-changing-cover` または `utils.color-changing-cover` を使用してください

  従来は隠す内容の上に半透明の矩形を重ねていましたが、背景まで薄くなり、隠した領域が重なると効果が蓄積するため、一般には適切に動作しません。表示自体は引き続き可能で、プレゼンテーションもコンパイルできますが、以前より見た目が悪くなる場合があります。`--warnings promote` を指定したビルドでは失敗します。

  ```typst
  // 変更前
  config-methods(cover: utils.semi-transparent-cover)
  // 変更後：実際にアルファ値を変更し、色を変更できない内容には自動で代替処理を適用
  config-methods(cover: utils.alpha-changing-cover)
  // または、すべてを単一色に変更するコンパイル負荷の小さい方法
  config-methods(cover: utils.color-changing-cover.with(color: gray))
  ```

  ほかのカバーメソッドの `fallback-hide` として渡す場合は引き続き使用でき、警告も出ません。`utils.cover-with-rect` も警告の対象外です。

- **修正・破壊的変更：`utils.handout-only` が削除されました** — トップレベルの `#handout-only[..]` を使用してください。これはコールバックではなくパーサーのマークなので、スライドを分割する内容を含められます。[配布資料モード](https://touying-typ.github.io/docs/tutorials/dynamic/handout)を参照してください。

- **修正・破壊的変更：アニメーション関数に負のサブスライド番号を渡すと、誤動作を黙って続ける代わりにエラーになります。**

  負の番号は最後のサブスライドから逆算する指定であり、サブスライド数が確定して初めて意味が定まります。`config-common(handout-subslides: -1)`、`#touying-recall(.., subslides: -1)`、`#touying-render(.., subslides: -1)` は解析後に指定値を解決するため、負の番号を使用できます。

  `#only`、`#uncover`、`#effect`、`#alternatives`、`#alternatives-match`、`#alternatives-cases`、`#animate`、`#item-by-item` は内容の流れの中で解析されるため、負の番号は無視されずエラーになります。必要な場合はウェイポイントを使用してください。

  ```typst
  // 変更前：何も表示されず、エラーも出ない
  #only(-1)[Last]
  // 変更後：ウェイポイントで位置を指定して参照する
  A #pause B #waypoint(<end>) C
  #only(get-last(<end>))[Last]
  ```

  `#waypoint(start:)` も同じ制限を受け、位置は `>= 1` である必要があります。あるウェイポイントを別のウェイポイントに対して相対的に配置するには、ラベルで参照してください。

  注意：文字列に含まれる負の数は別の記法なので影響を受けません。`"-2"` は「最後から2番目」ではなく「サブスライド2まで」という開いた範囲です。

- **修正：はみ出し検出が幅も確認するようになりました。** 以前は高さしか比較していなかったため、幅が大きすぎる固定幅のブロックなどでは警告が出ませんでした。

- **機能・破壊的変更：`touying-equation` が非推奨になりました。** その用途はほかの機能で代替でき、通常の数式アニメーションも強化されました。現在は `mat` や `frac` などの数式レイアウト関数内にもアニメーション関数を記述できます。

- **機能・破壊的変更：`config-common(detect-overflow:)` のデフォルト値は `auto` になりました**（以前は `true`）。これは `breakable` の設定の逆であり、両方が `true` の場合は、はみ出しを検出できないという警告が出ます。

- **機能・破壊的変更：`#measure` が `std.measure` を隠蔽し、アニメーション化された内容も正しく測定するようになりました。** 従来どおり周囲に `layout` または `context` が必要です。`width` と `height` に加え、測定前に描画するサブスライドとその基準を整数の `subslide` と `base` で指定できます。

- **機能・破壊的変更：reducerのバインディングがパッケージモジュールを受け取るラムダ式になりました。** 従来の文字列の配列と末尾の `arguments(..)` によるパス指定は使われません。

  ```typst
  // 変更前
  #let touying-reducer-bindings = (
    reduce: ("canvas",),
    cover: ("draw", "hide", arguments(bounds: true)),
  )
  // 変更後
  #let touying-reducer-bindings = (
    reduce: module => module.canvas,
    cover: module => module.draw.hide.with(bounds: true),
  )
  ```

  従来の形式は動作しません。`touying-reduce` も新しい形式を要求します。

### 移行ガイド

1. **コンパイラーを更新：** Typst 0.15.0以降へ更新してください。
2. **参考文献：** `config-common(show-bibliography-as-footnote: bibliography("ref.bib"))` を `config-common(show-bibliography-as-footnote: true)` に変更し、プレゼンテーション内で実際の `#bibliography("ref.bib")` を呼び出してください。`#magic.bibliography(..)` は削除します。複数のサブスライドを持つスライドに参考文献がある場合は、`only("h", bibliography("ref.bib"))` で囲み、一度だけ生成されるようにしてください。そうしないとサブスライドごとに新しいインスタンスが生成され、引用番号が再び最初から始まります ([#415](https://github.com/touying-typ/touying/issues/415)).
3. **発表者ノート：** `config-methods(show-only-notes: ..)` の*描画関数*を上書きしていた場合は、`config-common(notes-fn: ..)` に書き換え、`touying-notes` を基に構築してください。`config-common(show-only-notes: true)` は変更不要です。
4. **セカンドスクリーン：** テーマが `config-page(background: ..)` を通じてノート側の背景を描画していた場合は、そのスタイルを `notes-fn`（`fill` または `header-fill` を `touying-notes` に指定する）へ移してください。
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

  記事出力向けの記述には2つのマーカーが使えます。`#article-text[..]` はスライド内のどこに記述しても、そのスライドの内容を置き換えるため、箇条書きの代わりに文章の段落を指定できます。`#article-only[..]` は記事だけに現れる内容を追加します（詳細は下の出力モード専用の内容を参照）。新しい `config-article(..)` グループは記事側のレイアウトを扱います。`wrap` は特定の要素を横へ回り込ませる設定（`meander` で実装）、`linearize` はスライドのレイアウトをどこまで分解するかの設定です（デフォルトでは `#columns(..)`、composer、`side-by-side` は分解され、`table` や `grid` はヘッダーやフッターを宣言するまで分解されます）。`#article-linearize[..]` と `#article-keep-layout[..]` はこれを要素ごとに指定します。さらに、任意の `title-block-fn` と、最後に `available-fields` があります。これは `info.title` などの設定値を記事テーマ自身のパラメーターに渡す対応付けです。詳しくは[ドキュメント／記事モード](./integration/article-mode.md)を参照してください。

- **機能：シンプルなA4記事テーマ `themes.article`**

  これは `article-theme: auto` の既定のテーマです。プレゼンテーションの仕組みに依存しないため、論文やレポートにも単独で利用できます。あくまで実装例であり、ほかの記事用テーマも使用できます。


- **機能：数式要素内のアニメーションに対応** — `frac`、`mat`、`vec`、`cases`、`binom`、`root`、`attach`、`accent`、`cancel`、`lr`、各種括弧関数に対応します。

  以前はこれらの内部に `#pause`、`#uncover`、`#only`、`#alternatives` があると、走査で入れない場所にマークがあるとして `Unsupported mark` でコンパイルが中断されていました。しかし実際には以前から到達可能でした。これらの要素は部分的な内容を独自の名前付きフィールド（`frac` の `num`・`denom`、`mat` の `rows`、`attach` のスクリプト）に保持するのであって、`body` や `children` に保持するのではありません。内容の走査は後者2つしか認識しなかったため、それらの要素を葉として扱っていました。

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
  #touying-recall(<my-slide>, subslides: "waypoints")  // 各ウェイポイントから1つのサブスライドを選択
  #touying-recall(<my-diagram>, subslides: 1)          // reducerの第1段階
  #touying-recall(<my-table>)                          // 通常のラベル付き内容
  ```

  サブスライドの選択には整数、最後から逆算する負の番号、ウェイポイントのラベルやマーカー（`get-first`、`get-last`、`prev-wp`、`next-wp`）が使えます。これらは再利用対象自身のウェイポイントを基準に解決されます。要素の再利用はインラインで配置され、スライド全体を再利用するときだけ新しいページが始まります。呼び出しは `#only`、`#uncover`、`#alternatives` の本文内の任意の場所に配置でき、`base:` で対象内容の反復番号の起点を指定できます。

- **機能：`touying-render` が指定したアニメーション段階または範囲の内容を描画**

  `touying-recall` がラベルから既存の内容を探すのに対し、`touying-render` は内容の値を直接受け取ります。小さなプレビュー、記事出力で図の途中の状態を示す場合などに便利で、スライド全体の内容も描画できます。

  ```typst
  #touying-render(body, subslides: "2-4")   // 2、3、4の順に表示
  #touying-render(body, subslides: "!2-3")  // 2〜3を除外し、空いた部分を詰める
  #touying-render(body, subslides: <wp>)    // ウェイポイントの範囲全体
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

  配置効果（`"show"`、`"cover"`、`"remove"`、`swap(..)`）はサブスライドごとに1つだけ選ばれます。合成はできません。唯一のカバーメソッドである `hide` はスタイルノードではなく、`"remove"` は内容そのものを削除するため、どちらも内部から元に戻すことはできません。アルファ値を変更してカバーした内容が必要なら、代わりにスタイル効果関数を使ってください。スタイル関数は合成も入れ子もできます。優先度によって採用する配置効果と入れ子の順序が決まり、同順位の場合の処理は両者で一貫しており、最後に書いた効果が勝ちます。配置の場合はその効果が使われ、スタイルの場合はその効果が最も内側になるので、両方が設定するプロパティではその効果が勝ちます。`swap` は `stretch` を受け取ります。`alternatives` と同様に、指定しなければ置き換えが表示されている間にレイアウトが再調整され、指定すれば置き換え後の内容も確保する領域に含まれ、ブロックのサイズが一定に保たれます。測定は、いずれかのswapが `stretch` を設定している場合だけ行われ、配置とスタイルの異なる組み合わせごとに1回だけ行われます。`animate-hidden` と `animate-removed` はそれぞれ `"cover"` と `"remove"` から開始する同じ関数であり、`"show"` から開始するのではありません。そのため、効果はデフォルトで `body` が消える時点ではなく現れる時点を指定します。もちろんいずれも上書きでき、これらは単なる短縮形です。

- **機能：`advances-flow` を `touying-fn-wrapper` に追加し、連続するpauseの代わりとなるラッパーに対応**

  `item-by-item` が後続のpauseを最後の項目より後へ進める仕組みは通常のパラメーターになったため、独自のラッパーでも有効にできます。

  ```typst
  #let reveal-lines(body) = touying-fn-wrapper(
    (self: none, body) => /* ... */ body,
    last-subslide: repetitions => (repetitions + count-lines(body) - 1, (:)),
    advances-flow: true,   // 後続の #pause は最後の行の次から進む
    body,
  )
  ```

  既定値は `false` で、自分自身の内容だけをアニメーション化する場合に適しています。このようなラッパーは、pauseの位置を動かさずに必要なサブスライドを確保します。

- **機能：`touying-fn-wrapper-raw` が本文を再帰的に解析し、`touying-fn-wrapper` の内部にもraw版を配置可能に**

  以前は `#alert[This is important, #only("2-")[easter egg] don't miss it!]` が *Unsupported mark* で失敗していました。rawラッパーは位置引数をラップ先へ直接渡していたため、内部のアニメーションマーカーが未解決のメタデータとして残っていたためです。現在は通常のスライド内容と同様に解析されるため、`#pause`、`#meanwhile`、すべての関数ラッパーが内部でも動作し、内容はスライドのサブスライド総数にも反映されます。入れ子になったrawラッパーは再帰的に解決され、`#uncover[#alert[a #alert[b #alert[c]]]]` も動作します。

  このため `touying-fn-wrapper-raw` がマークアップから `self` にアクセスする場合のより適切なデフォルトの選択肢となり、ドキュメントとテーマにもその旨が記載されました。Stargazerの `tblock` も切り替えられ、通常のアニメーション関数と共に使用できるようになりました。これらが機能するには本文を位置引数として渡す必要があります。`.with(..)` で組み込むとパーサーから見えなくなります。[複雑なアニメーション](https://touying-typ.github.io/docs/tutorials/dynamic/complex)を参照してください。

- **機能：文書ごとに `.pdfpc` ファイルをバンドルのアセットとして出力** ([#408](https://github.com/touying-typ/touying/issues/408))

  Typst 0.15のバンドル出力は任意のファイルを生成できるため、別途 `typst query .. > example.pdfpc` を実行する必要がなくなりました（従来の方法も利用でき、ドキュメントでは現在 `typst eval` を使用しています）。`#pdfpc.bundle-assets()` を各 `#document(..)` の外側、トップレベルに記述します。

  ```sh
  typst compile --features bundle --format bundle --root . ./example.typ ./out
  # => ./out/deck.pdf と ./out/deck.pdfpc
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

- **機能：テーマが `touying-notes` を通じて発表者ノートのパネルを装飾可能に**

  `touying-notes` は `header`、`header-fill`、`fill`、`note-setting`、`preview-setting` を受け取ります。テーマは `note-setting` でページや内容を装飾できます。`header-height` は既定で内容の高さに合わせて縮み、`header: none` では帯を完全に削除します。`preview-setting` には元のサイズのスライドプレビューが渡されるため、`preview => place(bottom + right, box(scale(x: 30%, y: 15%, reflow: true, preview)))` のように自由に拡大縮小・配置できます。組み込みの6テーマはすべて独自の設定を持ちます。詳しくは[発表者ノート](https://touying-typ.github.io/docs/tutorials/speaker-notes)を参照してください。

- **機能：`show-notes-on-second-screen` が `top` と `left` にも対応しました**。従来の `bottom` と `right` も引き続き利用できます。対応する軸に沿ってページを2倍にし、追加の半分をマージンへ押し出すため、4方向すべてでスライド自体の寸法が保たれます。

- **機能：`alpha-changing-cover` と `color-changing-cover` の再設計によるカバー表示の改善** ([#112](https://github.com/touying-typ/touying/issues/112))

  `utils.alpha-changing-cover` が推奨の半透明カバーになり、前述の非推奨の `semi-transparent-cover` を置き換えます。スタイル設定可能なすべての色を扱い、要素が色を宣言していない場合は外側のスコープの色を継承し、グラデーションとタイルを理解し、特色をOklchに変換してから不透明度を変更します。自動フォールバックのアルファ値は薄くする色の明るさに依存し、フォールバック経路は従来よりもはるかに限定的に使われるため、画像や図が灰色のブロックに変わらなくなりました。`utils.color-changing-cover` はすべてを1つの色に統一する、コンパイル負荷の小さい代替手段のままです。色を読み取るのではなく上書きするので `context` を呼び出しません。両者は同じ木構造を走査するため、線、表のセル、`underline`、`highlight`、`strike` の本文を同じように扱い、どちらもカバーする内容を移動させません。要素が独自の背景を描く場合、背景とその上の内容を同じ色にすると読めなくなるため、`color-changing-cover(hide-filled: true)`（デフォルト）はこれらを `fallback-hide` に渡します。そのデフォルトは `auto` となり、色が変更された文字に合わせて薄く表示します。[カバー関数](https://touying-typ.github.io/docs/tutorials/dynamic/cover)を参照してください。

- **機能：`components.left-mid-right` で内容を3列に配置** ([#397](https://github.com/touying-typ/touying/pull/397), thanks @thomas-saigre)

- **機能：コマンドライン入力を読み取る `utils.get-input`**

  `--input key=value` で渡された値はTypstでは文字列になります。`utils.get-input` は各値をTypstとして解析するため、`accent=red` は色、`count=3` は整数、`flag=true` は真偽値になります。Typstが認識しない単独の単語は文字列として返されるため、`--input export-mode=handout` をシェルで引用符なしに指定できます。現時点では主に出力モードで利用していますが、今後ほかのコマンドライン設定にも利用する可能性があります。キーを省略すると解析済みの辞書全体を取得できます。詳しくは[touying-exporter](https://touying-typ.github.io/docs/external/touying-exporter)を参照してください。

- **機能・修正：`touying-get-config` が `common` を正式なカテゴリーとして公開**

  `touying-get-config("common.handout")` が解決されるようになり、`touying-get-config().common` は以前からドキュメントに記載されていたとおり、トップレベルのキーを部分木として返します。`common.store` のようにその配下でカテゴリーを指定すると、黙って失敗するのではなく明確なエラーが出ます。

- **機能：ウェイポイントがグローバルにリンク可能なアンカー `<slide.waypoint>` を生成します。** `<slide>` ラベルのあるスライド内の `<waypoint>` に対し、最後のサブスライドへのリンクを作成できます。アンカーは可能な限り適切な位置に置かれ、ウェイポイントの最後のサブスライドが描画されるならそこに配置されます（プレゼンテーションモードの既定動作）。そうでなければ直後のできるだけ近い位置、それも不可能ならウェイポイント内のできるだけ後ろに配置されます。描画されるサブスライドがすべて対象範囲より前ならアンカーは生成されません。

- 機能：新しいユーティリティ。`tree.is-math-symbol` と `tree.sequence-to-array` は新しい `core/tree.typ` に、`subslides.resolve-negative-subslides`（反復回数と任意の1以外の基準に対して負のサブスライド番号を解決する）は `core/subslides.typ` に追加され、さらに `utils.rescale-image` も追加されました。

- **修正：スライド内のラベル付き `#place`、`#rotate`、`#columns`、`#terms.item`**

  従来はパーサーがこの4種類を手動で再構築してラベルをコンストラクターへ直接渡していたため、`#place(top + right)[..] <lbl>` はコンパイルエラーになり、`#columns(2)[..] <lbl>` はラベルが失われていました。共通の再構築処理を通すようになり、ラベルが維持されます。

- **修正：`#item-by-item` は本文が `#set` で始まっても1つのサブスライドにまとめられなくなりました**

  「項目を数える」処理の3つの別々の実装は `styled` ノードの内部を見ることができませんでした。このノードは `#set` や `#show` が作成するため、`#item-by-item[#set text(..) \ - one \ - two]` は3つではなく1つのサブスライドを描画していました。

- **修正：ラベル付きリストに対する `magic.nontight`**

  要素の `label` をコンストラクターの引数へ展開していましたが、コンストラクターはそれを拒否するため、`#list([a], [b]) <lbl>` は `nontight-list-enum-and-terms` の下でコンパイルエラーになっていました。

- **修正：発表者ノートで装飾された内容が失われなくなりました**

  `utils.markup-text` はすべての `styled` 部分木を削除していたため、`[a #text(red)[b] c]` は `.pdfpc` ファイルに「a c」として渡されていました。この関数はpdfpc出力に使われるため、ノートが気付かないうちに不完全になっていました。

- **修正：`terms.item` や図のキャプション内のウェイポイント**

  ウェイポイントの前処理ではどちらの内部も走査しませんでしたが、メインの解析では走査していたため、その後のウェイポイントが誤ったサブスライドに配置されていました。

- **修正：特殊なスライドが不可視の見出しとしてタイトルを公開**

  アウトラインスライドのタイトルは見出しにならなかったので、現在の見出しを読み取る処理からは取得できませんでした。スライドのヘッダーが機能していたのはタイトルが明示的に渡されていたからで、ノートパネルには何も表示されませんでした。該当するスライドは現在 `place(hide(heading(level: self.slide-level, ..)))` を出力します。`place` はそれを内容の流れから外します。単なる `hide` ではアウトラインの内容が約16pt下へ押し出されていたためです。また `level: self.slide-level` は `custom-progressive-outline` が「Outline」を現在のセクションと誤認してすべての項目を薄くすることを防ぎます。aqua、dewdrop、metropolis、stargazerの `outline-slide` とstargazerの `ending-slide` に適用されます。タイトルスライドは今のところ変更していません。

- **修正：見た目だけを変えるカバーで隠された引用は引き続き実際の引用として扱われます**

  `color-changing-cover` は引用を `hide` のフォールバックへ送っていたため、引用マーカーは消えても、それが登録した参考文献の項目は残っていました。`@key` は内容の木構造では `ref` であり、レイアウト中に初めて `cite` になるため、どちらの関数も色変更可能な集合に含まれていませんでした。現在は両方が含まれ、引用は隠される代わりに `raw` と同様に色が変更されます。別の問題として、`magic.bibliography-as-footnote` は幅を確保するプレースホルダーを描画するかどうかを、`hide` 要素を探すことで判断しており、`utils.cover-hides-footnote` に問い合わせていませんでした。現在は設定に問い合わせるため、パーサー自身の脚注処理の分岐と一致します。

  未解決：`hide` ではない不透明なカバー（たとえば `cover-with-rect`）を `cover-hides-footnote: true` と組み合わせると、脚注の項目が漏れ出します。

- **修正：`#pause` の後にある注釈形式の引用が前のサブスライドに脚注を表示しなくなりました** ([#414](https://github.com/touying-typ/touying/pull/414), thanks @SchrodingerBlume)

  [#399](https://github.com/touying-typ/touying/pull/399) は直接記述した `#footnote` が対応する `#pause` より前に表示される問題を修正しましたが、`chicago-notes` などのCSL注釈形式の引用ではまだ脚注が漏れ出ていました。このような引用が脚注になるのはレイアウト処理中で、`cover-hides-footnote` が依存する本文走査の後です。そのため `hide()` はマーカーを隠しても、登録された項目はページ下部に描画されていました。`cite` と `ref` は現在 `footnote` と同じ3方向の分岐に従います。実際に隠すカバーの下では引用がまったく描画されないため項目は作成されず、表示された時点で通常どおり現れます。これは `show std.cite` ルールで脚注を生成するパッケージの問題も修正します。

- **修正：脚注形式の参考文献が文書の収束を妨げなくなりました** ([#395](https://github.com/touying-typ/touying/issues/395)) — 上述の参考文献機能の再設計により修正されました。以前は *document did not converge within five attempts*（文書が5回の試行で収束しなかった）という警告が出ていた、サイズとbreakable/detect-overflowの30通りの組み合わせで検証しました。CIのステップは `--warnings promote` で回帰テストを実行します。

- 修正：`#uncover`、`#only` などの関数ラッパーは `grid` や `table` の内部でも可視のままになります。そのコンテナーが `#pause` の後ろにある場合も同様です。関数ラッパーは自分自身の可視性を決定するため、周囲のpause領域から抜け出せますが、表形式のコンテナーはセルを再解析する*前*に隠れるかどうかを判断し、その古い結果を再利用して、すでに表示されたラッパーをカバーしていました。入れ子にしても変わりませんでした。`grid` が `block` の内側にある場合も影響を受けていました。

- **修正：参考文献の描画に先行する `pagebreak()` が不要になりました。** モード専用の内容内の `---` はスライド区切りとして処理され、`pagebreak()` は引き続き改ページとして扱われます。

- **修正：`touying-get-config` の `default:` の番兵値が組み込みの `type` ではなく専用マーカーになりました。** `default: type` は特別な値ではなくなり、キーを位置引数と名前付き引数の両方で渡すと、黙って無視される代わりにエラーになります。

- **修正：脚注が表示開始の `#pause` より前に現れなくなりました** ([#399](https://github.com/touying-typ/touying/pull/399), thanks @RivinHD for the initial contribution)

- 修正：`hidden-parts` は `touying-fn-wrapper` がpauseされた内容の後に続く場合も正しい順序で出力されるようになり、関数ラッパーがその前に書かれた内容より先に描画されなくなりました ([#400](https://github.com/touying-typ/touying/pull/400))

- **修正：カバーの間隔** ([#387](https://github.com/touying-typ/touying/issues/387), [#405](https://github.com/touying-typ/touying/pull/405))

- **修正：reducerの要素を1つのブロックとしてではなく個別にカバー** ([#371](https://github.com/touying-typ/touying/issues/371), [#381](https://github.com/touying-typ/touying/pull/381))

- **修正：`touying-reducer` は `reduce` と `cover` にパッケージ自身が使う要素の形式を渡します**。そのため、描画関数が1つの配列ではなく `..args` を受け取るパッケージもアダプターなしでバインドできるようになりました。

  パッケージの要素はそのコンストラクターが返す値であり、形式はさまざまです。CeTZの `rect(..)` とalchemistの `molecule(..)` は単一要素の配列、Fletcherの `node(..)` は内容、lilaqの `plot(..)` は辞書です。本文をコードブロック（`#cetz-canvas({ rect(..); circle(..) })`）として記述するとTypstが配列を結合するため、reducerは展開済みの要素を受け取り、カバー時に元へ戻す方法を推測する必要がありました。以前は常に配列で囲んでいたため、CeTZやalchemistでは正しく、それ以外では誤っていました。

  2種類の呼び出し方法は、引数が渡されてから平坦化されるまでの段階で区別できます。コードブロックは配列型の位置引数1つですが、要素を直接渡した場合は別々の引数になります。reducerはこの情報を要素ごとに記録し、元の形式で渡すようになりました。`cover` は要素全体を受け取り、`reduce` はブロック本文の場合は1つの配列、`lq.diagram(..plots)` のような可変長引数の場合は展開した引数を受け取ります。

  ```typst
  // plotが辞書で、diagramが `..plots` を受け取るlilaq
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
- ドキュメント：[ウェイポイント](https://touying-typ.github.io/docs/tutorials/dynamic/waypoints) で、`<part:intro>` と `<part:main>` が `<part>` の下で結合される階層的ラベルを説明しました。また `start` は `#waypoint` に属し、`#uncover` には属さないことを修正しました。
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
- **リファクタリング：内容の木構造の処理を1つの層 `src/core/tree.typ` に統一しました。** 内容が何であるかを認識し、分解して再構築する処理は約30か所に書かれ、互いにずれていました。ラベルの扱いは呼び出し箇所ごとに異なり、いくつかの走査は `styled` ノードの内部を見ることができず、`#set` や `#show` ルールが有効になると探しているものを見逃していました。新しいモジュールはTouyingについて何も知らず、何もインポートしないので、どこからでも使用できます。`shape-of` はノードが部分的な内容をどのように保持しているかを分類し、`children-of` と `rebuild` は分解と再構築で元に戻ることを保証し、`map-tree` は同一性による短絡処理を伴って走査します。`utils` から移動したものは、`is-*` 述語、`typst-builtin-*` ハンドル、`reconstruct*`、`trim`、`label-it` です。`core/subslides.typ` は可視範囲指定の解決（`check-visible`、`resolve-negative-subslides`、`last-required-subslide` など）を担当し、`resolve-waypoints` は `core/waypoints.typ` に移りました。旧名称は `utils` に残ります。内容を返す関数は非推奨警告とともに転送し、それ以外は新しいモジュール名を示してパニックを起こします。
- **リファクタリング：** ブロックの描画、ウェイポイントから整数への変換、数式・MiTeX・rawの処理、記事モードの走査処理を、それぞれ重複した複数の実装から単一の実装に統一しました。
- **テスト：** Typst 0.15.0とtytanic 0.4.0でレイアウトが変わったためリファレンス画像を再生成しました。記事モード（同一ソースのプレゼンテーション・配布資料・記事出力）、`recall-content`、`render-subslides`、`mode-never`、`notes-second-screen`、`pdfpc`、`cover-citation`、#395・#408・#415の回帰テストを追加しました。

### テーマの移行ガイド

**v0.8.0へ移行するテーマ作者向け：**

1. **本文を囲むのではなく `setting:` を通じて装飾します。** 組み込みテーマはすべて、配置と装飾を `touying-slide(setting: ..)` として渡すようになり、本文を `align(..)` やブロックで囲むことはなくなりました。囲むと本文がパーサーから見えなくなり、アニメーションの段階数計算と記事モードの線形化が壊れます。
   ```typst
   // 変更前
   touying-slide(self: self, config: config, align(center + horizon, body))

   // 変更後
   touying-slide(self: self, config: config, setting: align.with(center + horizon), body)
   ```
2. **`notes-fn` を登録します。** `config-methods(show-only-notes: ..)` の描画関数の上書きはすべて、`config-common(notes-fn: ..)` に置き換え、`touying-notes` を基に構築してください。テーマごとに約10行の色設定になります。`config-common(show-only-notes: ..)` フラグは別のオプションであり、変更はありません。
3. **特殊なスライドには不可視の見出しを付けます。** `utils.display-current-heading` からタイトルを取得でき、ヘッダーとノートパネルの両方で利用できます。
   ```typst
   place(hide(heading(
     level: self.slide-level, title,
     bookmarked: false, outlined: false, numbering: none,
   )))
   ```
4. **ヘルパーコンポーネントでは `touying-fn-wrapper-raw` を優先します。** `self` が必要な場合に使用し、本文を位置引数として渡すことで内部のアニメーションが解析されるようにします。
   ```typst
   #let tblock(title: none, it) = touying-fn-wrapper-raw(_tblock.with(title: title), it)
   ```
5. **ノート側の背景に `config-page(background: ..)` を使わないでください。** `touying-notes` の `fill` と `header-fill` を使用します。

## v0.7.4

### 機能追加

- **機能：パッケージからreducerのバインディングを自動検出する `touying-reduce` を追加** ([#363](https://github.com/touying-typ/touying/pull/363))
- **多言語対応：アウトラインメッセージにポーランド語を追加** ([#372](https://github.com/touying-typ/touying/pull/372))
- **機能：左右キーによるナビゲーションに対応** ([#352](https://github.com/touying-typ/touying/pull/352))
- 機能：位置を量子化してグループ化する `grid-size` パラメーターを `lazy-layout` に追加し、列・行の検出の堅牢性を向上させました
- **機能：左右のマージンを相殺してページ幅全体に広がる `components.full-width-block` を追加**

### 修正

- 修正（軽微な破壊的変更）：`lazy-layout` のデフォルト値を `false` に設定しました。`cols` の場合に適用されます
- **修正：マーク警告で `fn` フィールドが存在しない場合を適切に処理**
- **修正：`hide` が見出しに予期しない影響を与える問題** ([#366](https://github.com/touying-typ/touying/pull/366))
- **修正：`touying-fn-wrapper-raw` を公開APIとしてエクスポート** ([#362](https://github.com/touying-typ/touying/pull/362))
- 修正：マーク警告を改善し、`magic.warning` としても表示されるようになりました。`enable-mark-warning` が `false` の場合にも表示されます ([#348](https://github.com/touying-typ/touying/pull/348))
- **修正：警告を `uniwarn` 経由で出力** ([#359](https://github.com/touying-typ/touying/pull/359))

## v0.7.3

### 軽微な破壊的変更

- **機能・破壊的変更：`#speaker-note[]` を常に前のスライドへ関連付け、`receive-body-for-new-*-slide-fn` の既定値を `false` に変更** ([#354](https://github.com/touying-typ/touying/pull/354))
  - `#speaker-note[]` はスライドの作成方法（明示的なスライド呼び出し、見出しによって生成されたセクションスライド、通常の内容スライド）を問わず、常に**その上にあるスライド**に関連付けられます。これにより、スライドの後に配置した `#speaker-note[]` が意図しない空の「ゴースト」スライドを黙って作成するという、よくある落とし穴が解消されます。
  - `receive-body-for-new-section-slide-fn` とその派生設定の**デフォルト値は `false` になりました**（以前は `true`）。

### 移行ガイド

`= Section` 見出しの後の内容がセクションスライドの本文に取り込まれる動作に依存していた場合は、`receive-body-for-new-section-slide-fn: true` を `config-common(...)` で明示的に設定してください。

### 機能追加

- **機能：`item-by-item-fn` とそのプリセットを追加** ([#347](https://github.com/touying-typ/touying/pull/347))
- **機能：`custom-progressive-outline` の改善、`section-relationship` などを追加** ([#345](https://github.com/touying-typ/touying/pull/345))
- 機能：混在したレイアウトでのlazy-layoutを改善しました ([#355](https://github.com/touying-typ/touying/pull/355))
- 機能：`cols` を `side-by-side` の別名として追加し、`cols`、`lazy-xxx` などのコンポーネントを外部に公開しました ([#356](https://github.com/touying-typ/touying/pull/356))
- metropolisテーマ：metropolisにoutline-slideを追加しました ([#349](https://github.com/touying-typ/touying/pull/349))
- **機能：スライドの内容の高さが空の場合に警告を追加**

### ドキュメント

- **ドキュメント：複数列の例を追加し、構造を改善**

## v0.7.1

### 機能追加

- **機能（エージェント）：スライドのはみ出しを防ぐ `breakable` と `clip` オプションを追加** ([#336](https://github.com/touying-typ/touying/pull/336))
- **機能（コンポーネント）：複数列・行のブロックの高さ・幅を揃える `lazy-v`（`lazy-h`）と `lazy-layout` を追加** ([#339](https://github.com/touying-typ/touying/pull/339))
- 機能：追加の `contact` と `extra` フィールドを `config-info` に追加しました ([#342](https://github.com/touying-typ/touying/pull/342))
- **機能：`touying-get-config` 関数を追加** ([#333](https://github.com/touying-typ/touying/pull/333))

### 修正

- **修正：`fit-to-height` と `size-to-pt` を修正し、テキストの再配置に対応** ([#332](https://github.com/touying-typ/touying/pull/332))
- **修正：ウェイポイントマーカー** ([#341](https://github.com/touying-typ/touying/pull/341))


## v0.7.0

### 機能追加

- **主要機能：名前付きウェイポイント** ([#298](https://github.com/touying-typ/touying/pull/298))
- 機能（ウェイポイント）：startパラメーターとhandout-subslides内のウェイポイントに対応しました ([#304](https://github.com/touying-typ/touying/pull/304))
- 機能：文字列形式のサブスライド番号とウェイポイントに対するauto、現在位置を表す「h」文字列、反転機能を追加しました ([#301](https://github.com/touying-typ/touying/pull/301))
- **機能：reducerを通じて関数ラッパーによるアニメーションを暗黙に利用可能に** ([#300](https://github.com/touying-typ/touying/pull/300))

### 修正

- 修正：テキストの長い行を部分的に隠した場合にcover-with-rectが行を分割する問題と、色・アルファ値のカバーのフォールバック関数を修正しました ([#328](https://github.com/touying-typ/touying/pull/328))
- 修正：style=autoの場合にdisplay-current-headingで明示的な番号付けを使用します ([#329](https://github.com/touying-typ/touying/pull/329))
- **修正：showルールによるゴーストスライドを解消し、showルールとdeferキーワードの扱いを統一** ([#317](https://github.com/touying-typ/touying/pull/317))
- **修正：alertの適用が遅延しないように変更** ([#316](https://github.com/touying-typ/touying/pull/316))
- **修正：重複した入れ子のtext呼び出しを削除** ([#324](https://github.com/touying-typ/touying/pull/324))
- 修正：alternatives-match関数がstretchパラメーターを考慮するようになりました ([#320](https://github.com/touying-typ/touying/pull/320))
- **修正：ページマージンの統合と優先順位の扱い** ([#322](https://github.com/touying-typ/touying/pull/322))
- **修正：リスト周辺のカバーの間隔** ([#303](https://github.com/touying-typ/touying/pull/303))
- **修正：配布資料のサブスライド指定で負の番号（整数・配列）を正しく解析** ([#307](https://github.com/touying-typ/touying/pull/307))
- **修正：スコープ付きインポートを介してスライド関数が更新されない問題** ([#310](https://github.com/touying-typ/touying/pull/310))

[@zral0kh](https://github.com/zral0kh)、[@Andrew15-5](https://github.com/Andrew15-5)、[@navdeeprana](https://github.com/navdeeprana)、[@Cemoixerestre](https://github.com/Cemoixerestre) の皆さんの貢献に感謝します。

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
- 機能：`cover-fn` パラメーターを `uncover` に追加し、外部パッケージ（たとえばFletcher）との連携に対応しました ([#267](https://github.com/touying-typ/touying/pull/267))
- **機能：ミニスライドをインライン表示可能に** ([#228](https://github.com/touying-typ/touying/pull/228))
- **テーマ：universityとstargazerで長い著者一覧の表示を改善** ([#242](https://github.com/touying-typ/touying/pull/242))
- **simpleテーマ：装飾形式で配色設定を反映するよう修正** ([#252](https://github.com/touying-typ/touying/pull/252))
- **aqua・stargazerテーマ：title-slideにextraパラメーターを追加** ([#291](https://github.com/touying-typ/touying/pull/291))

### 修正

- **修正：`touying-set-config` のアンカーの回帰によってゴーストスライドが生成される問題を防止** ([#289](https://github.com/touying-typ/touying/pull/289))
- **修正：最初のスライドの装飾付き内容によって余分なスライドが生成される問題** ([#287](https://github.com/touying-typ/touying/pull/287))
- **修正：目次から除外した見出しをナビゲーションからも除外**
- **修正：グリッドのセル、ボックスなどのコンテナー内で `#meanwhile` が無視される問題** ([#274](https://github.com/touying-typ/touying/pull/274))
- 修正：全テーマで `config: parameter` が黙って無視される問題を修正しました ([#273](https://github.com/touying-typ/touying/pull/273))
- **修正：`#show`・`#set` ルールの後のスライドが描画されない問題** ([#268](https://github.com/touying-typ/touying/pull/268))
- **修正：タイトルページのPDFページラベルによってpdfpcの発表者ノートがずれる問題** ([#277](https://github.com/touying-typ/touying/pull/277))
- **修正：`#pause` によるアニメーション中のラベル付き脚注でラベル重複エラーが発生する問題** ([#275](https://github.com/touying-typ/touying/pull/275))
- 修正：`#pause` が `#speaker-note` の本文内（入れ子のリスト項目）にある場合の動作を修正しました ([#282](https://github.com/touying-typ/touying/pull/282))
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

1. **`config` を `utils.merge-dicts` の最後の位置へ移動します**。これにより利用者側で上書きできるようになります。
   ```typst
   // 変更前
   self = utils.merge-dicts(self, config, config-page(...))
   
   // 変更後
   self = utils.merge-dicts(self, config-page(...), config)
   ```

2. **任意の縦横比に対応するため `paper` を `utils.page-args-from-aspect-ratio` に置き換えます。**
   ```typst
   // 変更前
   config-page(paper: "presentation-" + aspect-ratio, ...)
   
   // 変更後
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
  - 利用者は `show heading: set text(blue)` を使用して、`dewdrop` など一部のテーマの見出しの色を変更できます。
  - テーマ作者は `utils.display-current-heading(level: 1, style: auto)` のようにして同じ効果を実現できます。
- **機能：config-infoの情報を `set document` に適用**
- 機能：`stretch: false` を `alternatives` 関数のデフォルトに設定しました。これは**軽微な破壊的変更**ですが、私は自動的な空白がない方が直感的だと考えています。

### 修正

- **修正：semi-transparent-coverを使うuncoverでのエラー**
- **修正：型を表す文字列の比較** https://github.com/touying-typ/touying/pull/153
- **修正：Typst 0.13.0での水平線の不具合**
- **リファクタリング：display-current-short-headingの動作を修正**


## v0.5.4 & v0.5.5

### 機能追加

- **ドキュメント：パラメーターの説明を改善し、Tinymistの補完ヒントを強化** https://github.com/touying-typ/touying/pull/98
- **機能：`heading` に疑似的な状態の固定を追加** https://github.com/touying-typ/touying/pull/124
- **機能：alpha-changing-coverとcolor-changing-coverを追加** https://github.com/touying-typ/touying/pull/129
- **機能：effect関数を追加** https://github.com/touying-typ/touying/issues/111
  - 例：`#effect(text.with(fill: red), "2-")[Something]` は、現在のサブスライドが2以降の場合に `[Something]` を表示します。
- 機能：引数 `config: (..)` を `xxx-slide` 関数に追加しました
- **機能：universityテーマに `align` 引数を追加**

### 修正

- **修正：show-hide-set-list-marker-noneでenumの番号も非表示にする** https://github.com/touying-typ/touying/pull/114
- **修正：図のグローバルなgutterが0以外でもプログレスバーが分割されないように変更** https://github.com/touying-typ/touying/pull/120
- **修正：複数の #pause を使ったときのfrozen-countersの不具合** https://github.com/touying-typ/touying/pull/124
- **修正：draftがtrueの場合のページ番号の誤り** https://github.com/touying-typ/touying/pull/125
- **修正：fit-to-heightとfit-to-widthの動作を一部修正** https://github.com/touying-typ/touying/pull/131
- **修正：見出し内の脚注の重複** https://github.com/touying-typ/touying/pull/132
- **修正：ページサイズを固定値で記述しないよう変更** https://github.com/touying-typ/touying/pull/134
- **修正：ページに既定の番号付けを追加** https://github.com/touying-typ/touying/issues/100
- **リファクタリング：show-strong-with-alertをスライド単位へ移動** https://github.com/touying-typ/touying/issues/123
- **リファクタリング：不要な `config-page(fill: ...)` を削除**
- **metropolisテーマ：タイトルページの色などを修正** https://github.com/touying-typ/touying/issues/103
- **metropolisテーマ：タイトルを指定した場合、ヘッダーが内容を返すよう修正** https://github.com/touying-typ/touying/pull/126
- **metropolisテーマ：colors辞書を尊重するよう変更** https://github.com/touying-typ/touying/pull/133
- **修正：`#effect` 関数の不具合**

以下の方々の貢献に感謝します： [@enklht](https://github.com/enklht).


## v0.5.3

### 機能追加

- 機能：`stretch` パラメーターを `#alternatives[]` 関数群に追加しました。これにより内部の要素がコンテキスト式である場合にも対応できます。
- **機能：番号付きリストのマーカーをベースラインに揃える `config-common(align-enum-marker-with-baseline: true)` を追加**
- 機能：`linebreaks` オプションを `components.mini-slides` に追加しました。 https://github.com/touying-typ/touying/pull/96
- **機能：新しいセクションスライドをスキップする `<touying:skip>` ラベルを追加**
- 機能：`config-common(show-hide-set-list-marker-none: true)` を追加しました。これにより `list` と `enum` のマーカーが `#pause` の後に不可視になります。
- **機能：参考文献を脚注に表示する `config-common(bibliography-as-footnote: bibliography(title: none, "ref.bib"))` を追加**
- **リファクタリング：strongで強調した文字をalertとして表示する `config-common(show-strong-with-alert: true)` を追加。** 一部のテーマに軽微な破壊的変更があります。
- **リファクタリング：タイトルとサブタイトルの見出しスタイルを保持するよう `display-current-heading` を変更** https://github.com/touying-typ/touying/issues/71
- **リファクタリング：`new-section-slide-fn` 関数群が `body` を受け取れるよう変更。** `receive-body-for-new-section-slide-fn` で制御できます。**破壊的変更です。**
  - たとえば、新しいセクションスライドに `#speaker-note[]` を追加できます。`= Section Title \ #speaker-note[]` のように記述します。
  - 新しいセクションスライドの本文に内容を追加したくない場合は、セクションタイトルの後に `---` を使用できます。

### 修正

- **修正：古くなったドキュメントを更新**
- **修正：配布資料モードでの `enable-frozen-states-and-counters` の不具合**
- **修正：`square()` 関数が使用できない問題** https://github.com/touying-typ/touying/issues/73
- **修正：`show-notes-on-second-screen: bottom` でフッターが隠れる問題** https://github.com/touying-typ/touying/issues/89
- **修正：表のセル内のメタデータ要素の扱い** https://github.com/touying-typ/touying/issues/77 https://github.com/touying-typ/touying/issues/95
- **修正：`auto-offset-for-heading` の既定値を `false` に変更**
- **修正：uncover・onlyが必要以上の内容を非表示にする問題** https://github.com/touying-typ/touying/issues/85
- **simpleテーマ：タイトルとサブタイトルの誤りを修正** https://github.com/touying-typ/touying/issues/70


## v0.5.1 & v0.5.2

- **修正：いくつかの不具合を修正**
- **`nontight-list-enum-and-terms` の既定値を `false` に変更**

## v0.5.0

このバージョンは大きな破壊的変更を含む更新です。過去の設計上の判断に起因する問題を多数取り除き、多くの機能を再設計しました。Touyingをより使いやすく、柔軟で、高機能にすることが目的です。

**主な変更点：**

- クロージャーとオブジェクト指向的な構文を避け、設定を単純化しました。文書コメントによってスライド関数の補完情報を増やせるようにもなりました。
  - 従来の `#let slide(self: none, ..args) = { .. }` は現在 `#let slide(..args) = touying-slide-wrapper(self => { .. })` となり、`self` は自動的に注入されます。
  - `config-xxx` 構文でTouyingを設定できます。例：`#show: university-theme.with(aspect-ratio: "16-9", config-colors(primary: blue))`。
- `touying-slide` 関数には `section`、`subsection`、`title` などのパラメーターが含まれなくなりました。これらは `self.headings` を通じて不可視の第1、第2、または第3レベルの見出しとして自動的にスライドに挿入されます（`slide-level` の設定で制御されます）。
  - Typstの見出し機能を利用して番号付け、目次、ブックマークに対応できます。
  - `#slide[= XXX]` 関数内の見出しは `slide-level + 1` のレベルに調整されます。これには `offset` パラメーターを使います。
  - 見出しのラベルでさまざまな動作を制御できます。`<touying:hidden>` などの特殊なラベル、短い見出し、`#touying-recall()` によるスライドの再利用などが可能です。
- `set` と `show` のルールを特定の位置に制限せず、任意の場所で通常どおり使用できるようになりました。

簡単な使用例を以下に示します。ほかの例は `examples` ディレクトリを参照してください。

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

**テーマの移行ガイド：**

テーマごとの詳細な変更は `themes` ディレクトリを参照してください。既存のテーマを移行する場合は、おおむね次の手順に従います。

1. `register` 関数を `xxx-theme` に改名し、`self` パラメーターを削除します。
2. `show: touying-slides.with(..)` の設定を追加します。
   - `self.methods.colors` を `config-colors(primary: rgb("#xxxxxx"))` に変更します。
   - `self.page-args` を `config-page()` に変更します。
   - `self.methods.slide = slide` を `config-methods(slide: slide)` に変更します。
   - `self.methods.new-section-slide = new-section-slide` を `config-methods(new-section-slide: new-section-slide)` に変更します。
   - `self.xxx-footer` のようなテーマの内部変数を `config-store(footer: [..])` に変更します。`self.store.footer` から参照できます。
   - ヘッダーとフッターの設定を `slide` 関数へ移動し、`xxx-theme` 関数内には置かないようにします。
   - `set` や `show` ルールを `xxx-theme` で直接使用するか、`config-methods(init: (self: none, body) => { .. })` を通じて設定し、`self` パラメーターを十分に活用できます。
3. `states.current-section-with-numbering` の代わりに `utils.display-current-heading(level: 1)` を使えます。
   - セクションかサブセクションかを区別せず、直前の見出しだけが必要なら `self => utils.display-current-heading(depth: self.slide-level)` を使います。
4. `alert` 関数は `config-methods(alert: utils.alert-with-primary-color)` で置き換えられます。
5. `touying-outline()` は不要になりました。代わりに `components.adaptive-columns(outline())` を使えます。`components.progressive-outline()` や `components.custom-progressive-outline()` の利用も検討してください。
6. `states.slide-counter.display() + " / " + states.last-slide-number` を `context utils.slide-counter.display() + " / " + utils.last-slide-number` に変更します。`states` の代わりに `utils` を使います。
7. 不要になった `slides` 関数を削除します。`title-slide()` を暗黙に挿入する代わりに `#title-slide()` を明示します。必要に応じて `xxx-theme` 関数に追加してください。
8. `#let slide(self: none, ..args) = { .. }` を `#let slide(..args) = touying-slide-wrapper(self => { .. })` に変更します。`self` は自動的に渡されます。
   - 個別のパラメーター設定を `self = utils.merge-dicts(self, config-page(fill: self.colors.neutral-lightest))` に変更します。
   - `self = utils.empty-page(self)` を削除し、代わりに `config-common(freeze-slide-counter: true)` と `config-page(margin: 0em)` を使います。
   - `(self.methods.touying-slide)()` を `touying-slide()` に変更します。
9. `config-common(subslide-preamble: self => text(1.2em, weight: "bold", utils.display-current-heading(depth: self.slide-level)))` を設定すると、スライドに見える見出しを挿入できます。
10. 最後に、関数に文書コメントを追加しておきましょう。特にTinymistプラグインを使用する場合に、利用者へよりよい補完ヒントを提供できます。

**その他の変更：**

- **stargazerテーマ：** [Coekjan/touying-buaa](https://github.com/Coekjan/touying-buaa) を基に変更した新しいテーマを追加しました。
- 機能：疑似的な状態の固定を実装し、番号付けと `#pause` を通常どおり使用できるようにしました。この動作は `enable-frozen-states-and-counters`、`frozen-states`、`frozen-counters` を `config-common()` で設定することで制御できます。
- 機能：`label-only-on-last-subslide` を実装しました。`@equation` と `@figure` を `#pause` アニメーションと共に使用するときのラベル重複警告を防ぎます。
- **機能：** 特定のスライドを再表示する `touying-recall(<label>)` 関数を追加しました。
- **機能：** `nontight-list-enum-and-terms` を実装しました。既定値は `true` で、`list`、`enum`、`terms` の `tight` を `false` にします。間隔は `#set list(spacing: 1em)` で設定できます。
- 機能：`list` を `terms` による実装に置き換え、`align-list-marker-with-baseline` を実現しました。デフォルトでは無効です。
- **機能：** リスト項目を倍率で拡大縮小する `scale-list-items` を実装しました。`scale-list-items: 0.8` なら0.8倍になります。
- 機能：`#pause` と `#meanwhile` を数式内で直接使用できるようにしました。たとえば `$x + pause y$` のように記述できます。
- 機能：`#pause` と `#meanwhile` を `grid` や `table` などほとんどのレイアウト関数で使用できるようにしました。
- **機能：** `#show: appendix` に対応しました。`#show: touying-set-config.with((appendix: true))` とほぼ同じです。
- **機能：** 見出しの動作を制御する `<touying:hidden>`、`<touying:unnumbered>`、`<touying:unoutlined>`、`<touying:unbookmarked>` を導入しました。
- **機能：** `utils.short-heading` の基本的な対応を追加しました。たとえば `<sec:my-section>` を「My Section」という短い見出しとして表示できます。
- **機能：** ページ幅に合わせて段組みを調整する `#components.adaptive-columns()` を追加しました。通常は `outline()` と組み合わせます。
- **機能：** 参考文献を脚注に表示する `#show: magic.bibliography-as-footnote.with(bibliography("ref.bib"))` を追加しました。
- **機能：** `custom-progressive-outline` や `mini-slides` などのコンポーネントを追加しました。
- 機能：`touying-outline()` を削除しました。これは `outline()` で直接置き換えられます。
- **修正：** `type(s) == "string"` や `locate(loc => { .. })` など、互換性の問題があるコードを置き換えました。
- **修正：** いくつかの不具合を修正しました。


## v0.4.2

- **metropolisテーマ：** 文字色を `neutral-dark` に分離しました（破壊的変更）。
- **機能：** マーク形式のuncover、only、alternativesを追加しました。
- **機能：** スライド内の装飾付きブロックに対する警告を追加しました。
- **機能：** touying-temporary-markの警告を追加しました。
- **機能：** 発表者ノート用のmarkup-textを追加しました。
- **修正：** スライドの不具合を修正しました。


## v0.4.1

### 機能追加

- **機能：** 標準の目次とブックマークに対応しました。
- **機能：** デュアルスクリーン向けの発表者ノートに対応しました。
- **機能：** touying-mitex関数を追加しました。

### 修正

- **修正：** dewdropテーマにoutline-slideを追加しました。
- 修正：repeatのデフォルト値「auto」に関する回帰を修正しました。

### その他の改善

- **機能：** `touying-outline` 関数でリストに対応しました。
- **機能：** auto-reset-footnoteを追加しました。
- **機能：** ページカウンターを改善する `freeze-in-empty-page` を追加しました。
- **機能：** 未使用の引数を取得するため、registerメソッドに `..args` を追加しました。


## v0.4.0

### 機能追加

- **機能：** すべてのテーマで `#footnote[]` に対応しました。
- **機能：** `self => self.subslide` を使ってヘッダーとフッターからサブスライドや反復回数を参照できます。
- **機能：** [ctheorems](https://typst.app/universe/package/ctheorems) の番号付き定理環境に対応しました。
- **機能：** セクションとサブセクションの番号付けに対応しました。

### 修正

- **修正：** 入れ子になったincludeを正しく処理します。
- **修正：** 複数ページにまたがるスライドで同じセクションを繰り返し生成しないようにしました。

## 破壊的変更

- **リファクタリング：** `self.padding` を削除し、`self.full-header` と `self.full-footer` の設定を追加しました。


## v0.3.3

- **テンプレート：** `touying-aqua` パッケージへ移動し、[Typst Universe Packages](https://typst.app/universe/search?kind=packages) からTouyingを検索できるようにしました。
- **テーマ：** universityとdewdropの不具合を修正しました。
- **機能：** `setting` パラメーターなしでset・showルールが動作するようにしました。
- **機能：** `composer` パラメーターを簡略化しました。
- **機能：** `empty-slide` 関数を追加しました。

## v0.3.2

- **重大な不具合の修正：** `is-sequence` を修正し、Touyingで `grid` と `table` が正しく動作するようにしました。
- **テーマ：** pride7氏の貢献によりaquaテーマを追加しました。
- **テーマ：** universityテーマの設定を柔軟にしました。
- **リファクタリング：** 変数 `s` を既定ではエクスポートせず、`register` 関数から取得するように変更しました（**破壊的変更**）。
- **メタデータ：** `categories` と `template` の設定を `typst.toml` に追加しました。Typst 0.11向けの変更です。


## v0.3.1

- いくつかの誤字を修正しました。
- slide-levelの不具合を修正しました。
- pdfpcのラベルの不具合を修正しました。


## v0.3.0

### 機能追加

- show-slidesモードを改善しました。
- alignとpadに対応しました。

### ドキュメント

- より詳しいドキュメントを追加しました。

### リファクタリング

- テーマを簡略化しました。

### 修正

- 多数の不具合を修正しました。

## v0.2.1

### 機能追加

- **Touying-reducer：** CeTZとFletcherのアニメーションに対応しました。
- **universityテーマ：** universityテーマを追加しました。

### 修正

- metropolisテーマのフッターの進捗表示を修正しました。
- simpleとdewdropの不具合を修正しました。
- 目次に5つ以上のセクションを表示できない問題を修正しました。


## v0.2.0

- **オブジェクト指向プログラミング：** シングルトン `s`、バインディングメソッド `utils.methods(s)`、`(self: obj, ..) => {..}` 形式のメソッド。
- **ページ引数の管理：** `#set page(..)` の代わりに `self.page-args` でページ設定を取得・変更し、不要なページの生成を避けます。
- **連続した内容の `#pause`：** インラインやリストを含め、スライドの最外層で#pauseを使用できます。
- **レイアウト関数の `#pause`：** `composer` に `utils.side-by-side` などの独自レイアウト関数を指定し、`#slide[..][..]` のような複数の位置引数を使えます。
- **同時表示の `#meanwhile`：** サブスライドカウンターをリセットする `#meanwhile` を提供します。
- **数式の `#pause` と `#meanwhile`：** 数式アニメーション向けの `#touying-equation("x + y pause + z")` を提供します。
- **スライド：** 標準の見出しで簡単にスライドを作成できます。
- **コールバック形式の `uncover`・`only`・`alternatives`：** Polyluxの簡潔な構文を基に、表示タイミングを細かく制御できます。
  - `repeat` パラメーターでサブスライド数を手動指定する必要があります。
- **透明なカバー：** `#let s = (s.methods.enable-transparent-cover)(self: s)` のようなオブジェクト指向構文で有効化できます。
- **配布資料モード：** `#let s = (s.methods.enable-handout-mode)(self: s)` で有効化します。
- **Fit-to-widthとfit-to-height：** ヘッダーのタイトルを幅に合わせ、画像を高さに合わせます。
  - `utils.fit-to-width(grow: true, shrink: true, width, body)`
  - `utils.fit-to-height(width: none, prescale-width: none, grow: true, shrink: true, height, body)`
- **スライドカウンター：** `states.slide-counter.display() + " / " + states.last-slide-number` と `states.touying-progress(ratio => ..)`。
- **付録：** `last-slide-number` を固定し、総スライド数が増えないようにします。
- **セクション：** Touyingの組み込み機能で現在のセクション名と進捗を表示できます。
  - `section` と `subsection` パラメーターを `#slide` で使い、新しいセクションまたはサブセクションを登録します。
  - `states.current-section-title` で現在のセクションを取得します。
  - `states.touying-outline` または `s.methods.touying-outline` でセクションの目次を表示します。
  - `states.touying-final-sections(sections => ..)` で独自の目次を表示します。
  - `states.touying-progress-with-sections((current-sections: .., final-sections: .., current-slide-number: .., last-slide-number: ..) => ..)` で高度な進捗表示が可能です。
- **ナビゲーションバー**：[この例](https://github.com/zbowang/BeamerTheme)のようなナビゲーションバーを `states.touying-progress-with-sections(..)` で表示します。`dewdrop` テーマで使用されています。
- **Pdfpc：** pdfpcに対応し、外部ツールなしで `.pdfpc` ファイルを単に `typst query` コマンドによって出力できます。
