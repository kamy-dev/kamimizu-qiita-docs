---
title: "Podman + Dev Containers環境構築：必要な設定まとめ"
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

Podman 環境で VS Code の Dev Containers 拡張機能を使おうとしたところ、コンテナへの接続がうまくいかない問題に遭遇しました。

本記事では、この問題の解決方法を共有します。

### 動作確認環境

| 項目 | バージョン |
|------|------------|
| OS | Windows 11 |
| VS Code | 1.106.2 |
| Dev Containers 拡張機能 | 0.431.1 |

## 結論

VS Code の Settings（`settings.json`）に以下の設定を追加します。

```json
{
    "dev.containers.dockerPath": "podman",
    "dev.containers.dockerComposePath": "podman-compose",
    "dev.containers.executeInWSLDistro": "podman-machine-default",
    "dev.containers.forwardWSLServices": false,
    "dev.containers.dockerSocketPath": "npipe://\\\\.\\pipe\\podman-machine-default"
}
```

## Dev Container とは

Dev Container（Development Container）は、開発環境をコンテナ内に構築する仕組みです。

<img width=100% src="https://code.visualstudio.com/assets/docs/devcontainers/containers/architecture-containers.png" alt="devcontainers" />

VS Code の Dev Containers 拡張機能を使うことで、プロジェクトごとに独立した開発環境を簡単に構築・共有できます。

### Dev Container のメリット

:::note info

-   **環境の統一**: チームメンバー全員が同じ開発環境を使用できる
-   **ローカル環境の汚染防止**: 依存関係がコンテナ内に閉じるため、ホスト環境をクリーンに保てる
-   **再現性**: `devcontainer.json` をリポジトリに含めることで、環境構築を自動化できる
    :::

## 発生した問題

### 発生状況

Podman を使用している環境で、Dev Containers 拡張機能からコンテナを起動しようとすると、Podman ソケットへの接続エラーが発生しました。
これは、Dev Containers 拡張機能がデフォルトで Docker を前提とした設定になっているためでした。

## 解決方法

再掲ですが、VS Code の Settings（`settings.json`）に以下の設定を追加します。

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
| `dockerSocketPath`   | Podman のソケットパスを指定                    |

### 設定手順

1. VS Code を開く
2. `Ctrl + ,`（または `Cmd + ,`）で Settings を開く
3. 右上のアイコンから「Open Settings (JSON)」をクリック
4. 上記の設定を追加して保存

## 解決後の確認

設定後、以下の手順で正常に動作することを確認します。

1. VS Code でプロジェクトを開く
2. `.devcontainer/` が存在するディレクトリで、コマンドパレット（`Ctrl + Shift + P`）を開く
3. 「Dev Containers: Reopen in Container」を実行
4. コンテナが正常に起動し、接続できることを確認

## まとめ

Podman 環境で Dev Containers を使用するには、Podman のための VS Code 設定変更が必要です。  
ただし、Dev Container 拡張機能のバージョンによっては正常にコンテナ接続できないことがあるため、その場合は Downgrade すると解決することがあります。

## 参考記事

#### 関連 Issue

-   [vscode-remote-release #10437](https://github.com/microsoft/vscode-remote-release/issues/10437)

#### 公式ドキュメント

-   [VS Code Dev Containers](https://code.visualstudio.com/docs/devcontainers/containers)
-   [Podman 公式サイト](https://podman.io/)
