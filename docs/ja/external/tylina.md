---
sidebar_position: 1
---

# Tylina

[Tylina](https://tylina.github.io/) は、Touying向けに推奨されるAI対応のWYSIWYGエディター兼発表ツールです。通常の `.typ` ファイルを正式なソースとして維持し、ブラウザまたはデスクトップで動作します。

> [Touyingの簡単な例をTylina Webで開く](https://tylina.github.io/app/?repo=https%3A%2F%2Fgithub.com%2Ftouying-typ%2Ftouying&provider=github&ref=main&load=dependencies&main=examples%2Fsimple.typ&file=examples%2Fsimple.typ&view=slides)

このリンクはGitHubリポジトリを読み込み、必要な依存関係を取得して `examples/simple.typ` をスライド表示で開きます。WebAssemblyによるローカルコンパイルを行い、作業コピーはブラウザ内に保存されます。

## TouyingでTylinaを使う理由

- 組版済みページを直接編集するか、Source LensとSplitを使ってソースコードを操作できます。
- 同じワークスペースでスライドのサムネイル、発表者ノート、ページの並べ替え、発表を管理できます。
- GitHub/GitLabリポジトリ全体か、現在のファイルに必要な依存関係だけを読み込めます。
- 文書を理解するAIと連携し、Web版では独自モデル、デスクトップ版ではACPエージェントを使って編集、コンパイル、検証を行えます。
- エディターから離れずにテンプレートを探し、PDFやPowerPointへ出力できます。

## 編集可能なPowerPointへの出力

Tylinaの出力ツールで **PPTX** を選び、用途に応じたモードを指定します。どちらでも発表者ノートは保持されます。

| モード | 適した用途 | 制約 |
| --- | --- | --- |
| **編集可能（実験的）** | PowerPointでの編集を続ける用途です。テキスト、簡単な数式、対応画像・図形・リンクがPowerPointのオブジェクトになります。 | 複雑な内容はベクター形式の代替レイヤーになります。フォントの置換や文字幅の違いで改行が変わるため、完全な見た目の一致や全要素の編集は保証されません。 |
| **見た目の再現性** | Typstのレイアウトに近い状態で発表する用途です。 | 各スライドは画像として出力されるため、文字や個別要素は編集できません。 |

横長のスライドでは **Export as PPTX** 操作が直接表示される場合があります。モードの指定や変換結果の確認には通常の出力ツールを使用します。

## Webリンクの再利用

例のURLでは `repo` と `ref` でリビジョンを指定し、`main` でコンパイル対象、`file` で最初に表示するファイル、`view=slides` でスライド表示を指定します。必要な依存関係だけを読み込むには `load=dependencies`、リポジトリ全体とGit操作が必要なら `load=all` を使います。

軽量な閲覧専用リンクには [Gistd](./gistd.md) も便利です。編集可能なワークスペース、発表機能、PowerPoint出力が必要ならTylinaを選びます。
