---
sidebar_position: 3
---

# Gistd

[Gistd](https://github.com/Myriad-Dreamin/gistd) は、Gitやその他のネットワークストレージ上の [Typst](https://typst.app) 文書を即座に共有します。最も重要な機能は、typst.tsを基盤としてTypst文書をコンパイルするため、テキストを選択してコピーできることです！

完全な編集可能なワークスペース、発表ツール、編集可能または視覚的な再現性を重視したPPTX出力が必要な場合は、推奨される [Tylina連携](./tylina.md) を使用してください。

- [グローバル（Cloudflare CDN）](https://gistd.myriad-dreamin.com)
- [アジア地域（ミラー、亚洲区域镜像）](https://gistd-cn.myriad-dreamin.com)

## GitHub上の文書を読み込む

例えば、次のようなGitHubのリンクがあるとします。

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

以下のURLパラメーターでGistdの動作を変更できます。

- `g-page`：表示するページ番号です。デフォルトは `1` です。スライドモードでのみ使用できます。
- `g-mode`：表示モードです。
  - `doc`：文書を記事モードで表示します。
  - `slide`：文書をスライドモードで表示します。
- `g-version`：使用するTypstコンパイラーのバージョンです。
  - `v0.13.0`、`v0.13.1`、`v0.14.0`、`latest` のいずれかを指定できます。

## スライド表示

上記の `g-mode=slide` を使用して文書をスライドモードで表示します。

文書の例：

- [Touyingのシンプルなスライド。](https://gistd.myriad-dreamin.com/touying-typ/touying/blob/main/examples/simple.typ?g-mode=slide)

## 任意のリンクから文書を読み込む

例えば、次のような任意のリンクがあるとします。

```
https://github.com/typst/templates/blob/main/charged-ieee/template/main.typ
```

リンクの前に `any-gistd.myriad-dreamin.com` を付けます。

```
https://any-gistd.myriad-dreamin.com/github.com/typst/templates/blob/main/charged-ieee/template/main.typ
```

`any-gistd.myriad-dreamin.com` は `gistd.myriad-dreamin.com/@any` の別名です。

特定のドメイン（ホスト）が識別された場合、Gistdはそれに対応する方法で文書を配信します。

- `github.com`：Gitプロトコル。
- `codeberg.org`：Gitプロトコル。
- `localhost` およびその他：ホストが `localhost` の場合はHTTPプロトコル、それ以外の場合はHTTPSプロトコルです。注意：Gistdがドメインを識別できない場合、指定されたドメイン上のほかのファイルは読み込みません。つまり、Typst文書はそのドメインを基準とするほかのリソースを読み込めません。

文書の例：

- [https://any-gistd.myriad-dreamin.com/github.com/Myriad-Dreamin/gistd/raw/main/README.typ](https://any-gistd.myriad-dreamin.com/github.com/Myriad-Dreamin/gistd/raw/main/README.typ)
- [https://gistd.myriad-dreamin.com/@any/github.com/Myriad-Dreamin/gistd/raw/main/README.typ](https://gistd.myriad-dreamin.com/@any/github.com/Myriad-Dreamin/gistd/raw/main/README.typ)

## CORSプロキシを使わずに文書を読み込む

デフォルトでは、Gistdは信頼できるCORSプロキシ（`https://underleaf.mgt.workers.dev`）を使って文書を読み込みます。GitHubとForgejoがGistdによる文書の読み込みを許可していないためです。詳しくは [isomorphic-git: Quickstart](https://isomorphic-git.org/docs/en/quickstart) を参照してください。

しかし、CORSプロキシを使わずに文書を読み込みたい場合もあります。クエリ文字列に `g-cors=false` を追加すると、これが可能になります。

例えば、`http://localhost:11449/main.typ` にある文書を読み込むには：

- [https://gistd.myriad-dreamin.com/@http/localhost:11449/main.typ?g-cors=false](https://gistd.myriad-dreamin.com/@http/localhost:11449/main.typ?g-cors=false)

## HTTPプロトコルで文書を読み込む

`@any` はURLからプロトコルを推測しますが、`@http` を使ってHTTPプロトコルを強制することもできます。例えば、`http://localhost:11449/main.typ` にある文書を読み込むには：

- [https://gistd.myriad-dreamin.com/@http/localhost:11449/main.typ?g-cors=false](https://gistd.myriad-dreamin.com/@http/localhost:11449/main.typ?g-cors=false)

### 開発

依存関係をインストールします。

```
pnpm install
```

ローカルで開発します。

```
pnpm dev
```

ビルドします。

```
pnpm build
```
