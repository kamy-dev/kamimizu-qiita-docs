---
title: "Podman環境でDev Containers利用時の問題に終止符を打つ"
tags:
  - "Podman"
  - "Docker"
  - "devcontainer"
  - "VSCode"
  - "Windows"
private: false
updated_at: ""
id: null
organization_url_name: kyndryl
slide: false
ignorePublish: false
---

## はじめに

Podman 環境で VS Code の拡張機能「Dev Containers」を利用しようとしたところ、コンテナへの接続ができない問題が発生しました。
Dev Containers は、コンテナ内で開発を進められるためローカル環境を汚さず、また共同開発において開発環境の差異を吸収してくれるメリットがあります。ぜひ活用したかったのですが、解決に半年ほどかかりました。
本記事では、この問題の解決方法を共有します。

### 動作確認環境

| 項目                    | バージョン |
| ----------------------- | ---------- |
| OS                      | Windows 11 |
| VS Code                 | 1.106.2    |
| Dev Containers 拡張機能 | 0.431.1    |

## 想定読者

- Podman 環境でこれから開発を始めたい方
- Podman 環境で開発しており、Dev Containers の使い方がわからない方
- Docker から Podman への移行を検討している方
- Podman や Dev Containers に興味のある方

## 結論

VS Code の **ユーザー設定**（User Settings の `settings.json`）に以下の設定を追加します。

```json
{
  "dev.containers.dockerPath": "podman",
  "dev.containers.dockerComposePath": "podman-compose",
  "dev.containers.executeInWSLDistro": "podman-machine-default",
  "dev.containers.forwardWSLServices": false,
  "dev.containers.dockerSocketPath": "npipe://\\\\.\\pipe\\podman-machine-default"
}
```

:::note warn
ワークスペース設定やプロファイル設定ではなく、必ず**ユーザー設定**に追加してください。VS Code 1.95.0 以降では設定スコープの仕様変更により、ワークスペース設定に記述しても正しく動作しない場合があります。
:::

## Dev Containers とは

Dev Containers（Development Containers）は、開発環境をコンテナ内に構築する仕組みです。

<img width=100% src="https://code.visualstudio.com/assets/docs/devcontainers/containers/architecture-containers.png" alt="devcontainers" />

https://code.visualstudio.com/docs/devcontainers/containers

### Dev Containers のメリット

:::note info
- **環境の統一**: チームメンバー全員が同じ開発環境を使用できる
- **ローカル環境の汚染防止**: 依存関係がコンテナ内に閉じるため、ホスト環境をクリーンに保てる
- **再現性**: `devcontainer.json` をリポジトリに含めることで、環境構築を自動化できる
:::

## 発生した問題

### 発生状況

Podman を使用している環境で、Dev Containers 拡張機能からコンテナを起動しようとすると、Podman ソケットへの接続エラーが発生しました。
これは、Dev Containers 拡張機能がデフォルトで Docker を前提とした設定になっているためです。

Podman のリモートクライアント接続アーキテクチャについては、以下の Red Hat 公式記事が参考になります。

https://www.redhat.com/en/blog/podman-clients-macos-windows

## 解決方法

再掲ですが、VS Code の **ユーザー設定**（`settings.json`）に以下の設定を追加します。

```json
{
  "dev.containers.dockerPath": "podman",
  "dev.containers.dockerComposePath": "podman-compose",
  "dev.containers.executeInWSLDistro": "podman-machine-default",
  "dev.containers.forwardWSLServices": false,
  "dev.containers.dockerSocketPath": "npipe://\\\\.\\pipe\\podman-machine-default"
}
```

### 各設定項目の説明

| 設定項目             | 説明                                           |
| -------------------- | ---------------------------------------------- |
| `dockerPath`         | コンテナランタイムのパスを `podman` に変更     |
| `dockerComposePath`  | Compose ツールのパスを `podman-compose` に変更 |
| `executeInWSLDistro` | 実行する WSL ディストリビューションを指定      |
| `forwardWSLServices` | WSL サービスの転送を無効化                     |
| `dockerSocketPath`   | Podman のソケットパスを指定。<br>`npipe://\\\\.\\pipe\\podman-machine-default` は Windows 環境で使われる「名前付きパイプ（Named Pipe）」を指す URI 形式です。<br>なお、`podman-machine-default` は Podman マシン名です。環境構築時に別名を指定した場合は、その名前に置き換えてください。  |

### 設定手順

1. VS Code を開く
2. `Ctrl + ,`（または `Cmd + ,`）で Settings を開く
3. 右上のアイコンから「Open Settings (JSON)」をクリック
4. 上記の設定を追加して保存

## まとめ

Podman 環境で Dev Containers を使用するには、Podman 用の VS Code 設定が必要です。

:::note warn
Dev Containers 拡張機能のバージョンによっては正常にコンテナ接続できないことがあります。その場合は、拡張機能を以前のバージョンにダウングレードすることで解決する場合があります（VS Code の拡張機能画面から「他のバージョンをインストール」を選択）。
:::

## 参考記事

#### 関連 Issue

- [vscode-remote-release #10437](https://github.com/microsoft/vscode-remote-release/issues/10437)

#### 公式ドキュメント

- [VS Code Dev Containers](https://code.visualstudio.com/docs/devcontainers/containers)
- [Podman 公式サイト](https://podman.io/)
