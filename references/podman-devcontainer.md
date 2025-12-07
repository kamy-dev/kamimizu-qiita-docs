# 基本設定

```yaml
---
title: "Podman環境でDev Containerを使う際の問題に終止符を打つ"
tags:
    - "Podman"
    - "devcontainer"
    - "VSCode"
private: false # true: 限定共有記事 / false: 公開記事
updated_at: "" # 記事を投稿した際に自動的に記事の更新日時に変わります
id: null # 記事を投稿した際に自動的に記事のUUIDに変わります
organization_url_name: kyndryl # 関連付けるOrganizationのURL名
slide: false # true: スライドモードON / false: スライドモードOFF
---
```

## 伝えたいこと

-   これは、VSCode 1.106.2、Dev Containers 0.431.1 の Version の話である
-   VS Code を開き、Settings から以下を追記するとOK

```json
    "dev.containers.dockerPath": "podman",
    "dev.containers.dockerComposePath": "podman-compose",
    "dev.containers.executeInWSLDistro": "podman-machine-default",
    "dev.containers.forwardWSLServices": false,
    "dev.containers.dockerSocketPath": "npipe://\\\\\\\\.\\\\pipe\\\\podman-machine-default",
```

-   基本的なDev Containerとは何かについての説明

## 参考資料

-   https://github.com/microsoft/vscode-remote-release/issues/10437
