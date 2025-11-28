# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## プロジェクト概要

このプロジェクトは、Qiita（技術記事プラットフォーム）での記事執筆を管理するリポジトリです。Qiita CLI を使用して記事の作成、プレビュー、管理を行います。

## Qiita CLI コマンドガイド

### Qiita Preview の起動（プレビュー画面の表示）

本文の執筆は、ブラウザでプレビューしながら確認できます。ブラウザでプレビューするためには以下のコマンドを実行します。
コマンド実行時に、Qiita に投稿している記事がダウンロードされます。

```console
npx qiita preview
```

コマンド実行すると、Qiita Preview(プレビュー画面)にアクセスすることが可能になります。
プレビュー画面のデフォルトの URL は http://localhost:8888 です。

### 記事ファイルの配置について

1 つの記事の内容は、1 つの markdown ファイル（◯◯.md）で管理します。
記事ファイルは`public`ディレクトリ内に含める必要があります。

```console
.
└─ public
  ├── newArticle001.md
  └── newArticle002.md
```

### Qiita CLI で記事を管理する

#### 記事の作成

Qiita Preview 上の「新規記事作成」ボタン、または以下のコマンドで新規記事を作成できます。

```console
npx qiita new 記事のファイルのベース名
```

記事のファイルのベース名は自由に変更が可能です。

> 記事のファイル名を`newArticle001.md`にしたい場合は`newArticle001`にします。
>
> 例): `$ npx qiita new newArticle001`

作成された記事ファイルの中身は次のようになっています。

```yaml
---
title: newArticle001 # 記事のタイトル
tags:
    - "" # タグ（ブロックスタイルで複数タグを追加できます）
private: false # true: 限定共有記事 / false: 公開記事
updated_at: "" # 記事を投稿した際に自動的に記事の更新日時に変わります
id: null # 記事を投稿した際に自動的に記事のUUIDに変わります
organization_url_name: null # 関連付けるOrganizationのURL名
slide: false # true: スライドモードON / false: スライドモードOFF
---
# new article body
```

ファイルの上部には`---`に挟まれる形で記事の設定（Front Matter）が含まれています。
ここに記事のタイトル（title）やタグ(tags)などを yaml 形式で指定します。

#### 記事の投稿・更新

Qiita Preview 上の「記事を投稿する」ボタン、または以下のコマンドで投稿・更新ができます。

```console
npx qiita publish 記事のファイルのベース名
```

以下のコマンドで全ての記事を反映させることができます。

```console
npx qiita publish --all
```

`--force`オプションを用いることで、強制的に記事ファイルの内容を Qiita に反映させます。

```console
npx qiita publish 記事ファイルのベース名 --force
# -f は --force のエイリアスとして使用できます。
npx qiita publish 記事ファイルのベース名 -f
```

#### 記事の削除

Qiita CLI、Qiita Preview から記事の削除はできません。
`public`ディレクトリから markdown ファイルを削除しても Qiita 上では削除はされません。

[Qiita](https://qiita.com)上で記事の削除を行なえます。

### GitHub で記事を管理する

Qiita では、Qiita CLI を使うことで、Qiita の記事を GitHub リポジトリで管理できます。
Qiita の記事を GitHub リポジトリで管理する方法については以下の記事をご覧ください。

https://qiita.com/Qiita/items/32c79014509987541130

### Qiita CLI のコマンド、オプションについて

#### help

簡単なヘルプが見れます。

```console
npx qiita help
```

#### pull

記事ファイルを Qiita と同期します。
Qiita 上で更新を行い、手元で変更を行っていない記事ファイルのみ同期されます。

```console
npx qiita pull
```

`--force`オプションを用いることで、強制的に Qiita 上の内容を記事ファイルに反映させます。

```console
npx qiita pull --force
# -f は --force のエイリアスとして使用できます。
npx qiita pull -f
```

#### version

Qiita CLI のバージョンを確認できます。

```console
npx qiita version
```

### ユーザー設定ファイルについて

`npx qiita init`コマンドで生成される`qiita.config.json`について説明します。
このファイルを用いて、Qiita CLI の設定を行うことができます。
設定できるオプションは以下の通りです。

-   includePrivate: 限定共有記事を含めるかどうかを選べます。デフォルトは`false`です。
-   port: `qiita preview`コマンドで利用するポートを指定できます。デフォルトは`8888`です。

### オプション

#### --credential \<credential_dir>

Qiita CLI の認証情報（`credentials.json`）を配置する・しているディレクトリを指定できます。
デフォルトでは`$XDG_CONFIG_HOME/qiita-cli`もしくは`$HOME/.config/qiita-cli`になっています。

```console
npx qiita login --credential ./my_conf/
npx qiita preview --credential ./my_conf/
```

#### --config \<config_dir>

Qiita CLI の設定情報（`qiita.config.json`）を配置する・しているディレクトリを指定できます。

デフォルトでは、カレントディレクトリになります。

例）

```console
npx qiita login --config ./my_conf/
npx qiita preview --config ./my_conf/
```

#### --root \<root_dir>

記事ファイルがダウンロードされるディレクトリを指定できます。
デフォルトでは、カレントディレクトリになります。

例）

```console
npx qiita preview --root ./my_articles/
npx qiita publish c732657828b83976db47 --root ./my_articles/
```

#### --verbose

詳細なログを出力できます。

```console
npx qiita login --verbose
npx qiita preview --verbose
```

## プロジェクト構成

-   `public/` - 記事ファイル（Markdown）
-   `templates/` - 記事テンプレート
    -   `introduction-template.md` - 入門記事テンプレート
    -   `bug-fix-template.md` - バグ修正記事テンプレート
    -   `tried-it-out-template.md` - 試してみた記事テンプレート
-   `rules/` - プロジェクト運用ルール
    -   `qiita-writing-rules.md` - 記事執筆ルール
    -   `git-commit-rules.md` - コミットメッセージルール
-   `references/` - 各記事で伝えたい内容、参考リンクをまとめた md ファイル(<記事名>.md) ※記事作成時はこちらを必ず参照してください
-   `draft/` - 記事ファイルのドラフトとなるバーバラミント流ピラミッド型のメモ(Markdown、mermaid)

---

## 個人設定

これ以降は利用者自身の状況に合わせて設定を追加してください

## 記事作成の重要なルール

### 記事メタデータフォーマット

```yaml
---
title: newArticle001 # 記事のタイトル
tags:
    - "" # タグ（ブロックスタイルで複数タグを追加できます） 
    **Qiitaで最も検索されているタグかつ記事タイトルに関係するタグを適用してください。reference配下で5つに満たない場合、必ず5つタグを設定してください**  
    **タグは、https://qiita.com/tags から探してください。**
private: false # true: 限定共有記事 / false: 公開記事
updated_at: "" # 記事を投稿した際に自動的に記事の更新日時に変わります
id: null # 記事を投稿した際に自動的に記事のUUIDに変わります
organization_url_name: kyndryl # 関連付けるOrganizationのURL名 (default: null)
slide: false # true: スライドモードON / false: スライドモードOFF
---
# new article body
```

### コミットメッセージフォーマット

`type/scope: subject`

-   **type**: `feat`, `fix`, `style`, `refactor`, `chore`, `docs`
-   **scope**: 記事のスラッグ（例: `eventbridge-context-20250928`）
-   **subject**: 変更内容を簡潔に記述

例:

-   `feat/new-article-slug: 新規記事「〇〇について」を追加`
-   `fix/eventbridge-context-20250928: 誤字を修正`

### 記事執筆スタイル

-   ですます調で統一
-   専門用語には注釈を入れる
-   読者が再現可能な手順を記載
-   参考記事やドキュメントへのリンクを記載
-   コードブロックには言語を指定
-   絵文字を適切に使用してエンゲージメントを高める
