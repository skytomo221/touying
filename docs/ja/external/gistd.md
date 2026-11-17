---
sidebar_position: 3
---

# Gistd

[Gistd](https://github.com/Myriad-Dreamin/gistd) は、Gitやほかのネットワークストレージ上の [Typst](https://typst.app) 文書をすぐに共有できるツールです。typst.tsを使って文書をコンパイルするため、表示されたテキストを選択してコピーできます。

編集可能なワークスペース、発表ツール、編集可能または見た目重視のPPTX出力が必要なら、推奨される [Tylina連携](./tylina.md)を利用してください。

- [グローバル（Cloudflare CDN）](https://gistd.myriad-dreamin.com)
- [アジア地域（ミラー）](https://gistd-cn.myriad-dreamin.com)

## GitHub上の文書を読み込む

たとえば、次のGitHubリンクがあるとします。

```
https://github.com/typst/templates/blob/main/charged-ieee/template/main.typ
```

`github.com` を `gistd.myriad-dreamin.com` に置き換えるだけです。

```
https://gistd.myriad-dreamin.com/typst/templates/blob/main/charged-ieee/template/main.typ
```

文書の例：

- [https://gistd.myriad-dreamin.com/johanvx/typst-undergradmath/blob/main/undergradmath.typ](https://gistd.myriad-dreamin.com/johanvx/typst-undergradmath/blob/main/undergradmath.typ)
- [https://gistd.myriad-dreamin.com/Jollywatt/typst-fletcher/blob/main/docs/manual.typ](https://gistd.myriad-dreamin.com/Jollywatt/typst-fletcher/blob/main/docs/manual.typ)
- [https://gistd.myriad-dreamin.com/typst/templates/blob/main/charged-ieee/template/main.typ](https://gistd.myriad-dreamin.com/typst/templates/blob/main/charged-ieee/template/main.typ)

## 表示パラメーター

次のURLパラメーターでGistdの動作を変更できます。

- `g-page`：表示するページ番号。既定値は `1` で、スライドモードでのみ有効です。
- `g-mode`：表示モード。
  - `doc`：文書を記事モードで表示します。
  - `slide`：文書をスライドモードで表示します。
- `g-version`：使用するTypstコンパイラーのバージョン。
  - `v0.13.0`、`v0.13.1`、`v0.14.0`、`latest` などを指定できます。

## スライド表示

前述の `g-mode=slide` で文書をスライドモードで表示できます。

文書の例：

- [Touyingの簡単なスライド](https://gistd.myriad-dreamin.com/touying-typ/touying/blob/main/examples/simple.typ?g-mode=slide)

## 任意のリンクから文書を読み込む

たとえば、次のようなリンクがあるとします。

```
https://github.com/typst/templates/blob/main/charged-ieee/template/main.typ
```

リンクの前に `any-gistd.myriad-dreamin.com` を付けます。

```
https://any-gistd.myriad-dreamin.com/github.com/typst/templates/blob/main/charged-ieee/template/main.typ
```

`any-gistd.myriad-dreamin.com` は `gistd.myriad-dreamin.com/@any` の別名です。

ドメイン（ホスト）を認識できる場合は、それに対応した方法で文書を取得します。

- `github.com`：Gitプロトコル。
- `codeberg.org`：Gitプロトコル。
- `localhost` など：`localhost` ではHTTP、それ以外ではHTTPSを使用します。ドメインを識別できない場合、Gistdはそのドメイン上の別ファイルを読み込めないため、Typst文書から相対パスでリソースを参照できません。

文書の例：

- [https://any-gistd.myriad-dreamin.com/github.com/Myriad-Dreamin/gistd/raw/main/README.typ](https://any-gistd.myriad-dreamin.com/github.com/Myriad-Dreamin/gistd/raw/main/README.typ)
- [https://gistd.myriad-dreamin.com/@any/github.com/Myriad-Dreamin/gistd/raw/main/README.typ](https://gistd.myriad-dreamin.com/@any/github.com/Myriad-Dreamin/gistd/raw/main/README.typ)

## CORSプロキシを使わずに読み込む

既定では、Gistdは信頼されたCORSプロキシ（`https://underleaf.mgt.workers.dev`）で文書を読み込みます。GitHubやForgejoではGistdから直接読み込むことができないためです。詳しくは [isomorphic-git: Quickstart](https://isomorphic-git.org/docs/en/quickstart) を参照してください。

CORSプロキシを使わない場合は、クエリ文字列に `g-cors=false` を追加します。

たとえば `http://localhost:11449/main.typ` を読み込むには次のようにします。

- [https://gistd.myriad-dreamin.com/@http/localhost:11449/main.typ?g-cors=false](https://gistd.myriad-dreamin.com/@http/localhost:11449/main.typ?g-cors=false)

## HTTPプロトコルで文書を読み込む

`@any` infers protocol from the URL, while you could use `@http` to force HTTP protocol. たとえば `http://localhost:11449/main.typ` を読み込むには次のようにします。

- [https://gistd.myriad-dreamin.com/@http/localhost:11449/main.typ?g-cors=false](https://gistd.myriad-dreamin.com/@http/localhost:11449/main.typ?g-cors=false)

### 開発

依存関係のインストール：

```
pnpm install
```

ローカル開発：

```
pnpm dev
```

ビルド：

```
pnpm build
```
