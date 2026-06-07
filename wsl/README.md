---
title: WSL
description: 
published: true
date: 2026-06-07T07:51:40.410Z
tags: 
editor: markdown
dateCreated: 2026-06-07T07:50:28.547Z
---

# 🚀 WSL2 & wsltty 導入・管理ガイド

Windows 上で Linux 環境を構築する **WSL2** の導入から、軽量ターミナル **wsltty** のセットアップ、そして不要になった際の削除手順までをまとめたガイドです。

---

## I. WSLインストール

### 1. Windows の機能有効化
まずは WSL2 を動かすための土台作りを行います。

1.  **[コントロール パネル]** > **[プログラム]** > **[Windows の機能の有効化または無効化]** を開く。
2.  以下の 2 項目にチェックを入れる：
    * [x] **Linux 用 Windows サブシステム**
    * [x] **仮想マシン プラットフォーム**
3.  **[OK]** をクリックし、必ず **Windows を再起動** する。

> [!CAUTION]
> **再起動をスキップすると、次のステップでエラーが発生します。**

### 2. WSL2 のインストール
再起動後、Linux 本体のインストールを行います。

1.  **PowerShell** を「管理者として実行」で開く。
2.  以下のコマンドを入力して実行：
    ```powershell
    wsl --install
    ```
3.  完了後、Ubuntu のウィンドウが自動起動します。
4.  画面の指示に従い、**ユーザー名** と **パスワード** を設定すれば完了です。
5.  最新化と必要パッケージインストール
    ```bash
    sudo apt update
    sudo apt upgrade
    sudo apt install zip emacs-nox
    ```
6.  dockerインストール
    ```bash
    sudo apt install ca-certificates curl
    sudo install -m 0755 -d /etc/apt/keyrings
    sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
    sudo chmod a+r /etc/apt/keyrings/docker.asc
    sudo tee /etc/apt/sources.list.d/docker.sources <<EOF
    Types: deb
    URIs: https://download.docker.com/linux/ubuntu
    Suites: $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}")
    Components: stable
    Architectures: $(dpkg --print-architecture)
    Signed-By: /etc/apt/keyrings/docker.asc
    EOF
    sudo apt update
    sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
    sudo systemctl status docker
    curl -fsSL https://nvidia.github.io/libnvidia-container/gpgkey | sudo gpg --dearmor -o /usr/share/keyrings/nvidia-container-toolkit-keyring.gpg
    curl -s -L https://nvidia.github.io/libnvidia-container/stable/deb/nvidia-container-toolkit.list | \
    sed 's#deb https://#deb [signed-by=/usr/share/keyrings/nvidia-container-toolkit-keyring.gpg] https://#g' | \
    sudo tee /etc/apt/sources.list.d/nvidia-container-toolkit.list
    sudo nvidia-ctk runtime configure --runtime=docker
    sudo service docker restart
    ```
    
---

## II. wslttyのインストールとおすすめ設定

### 1. wsltty の導入
標準のコンソールよりも軽量でカスタマイズ性の高いターミナルを導入します。

#### 📥 ダウンロード
* [wsltty リリースページ](https://github.com/mintty/wsltty/releases) から、最新の `wsltty-x.x.x-install.exe` を入手。

#### ⚙️ インストール
1.  インストーラーを起動し、すべてデフォルト設定で進めます。
2.  完了後、スタートメニューに **[WSL Terminal]** が追加されます。

### 2. おすすめの初期設定
wsltty 上で右クリック > **[Options...]** から変更可能です。

| カテゴリ | 項目 | おすすめ設定 |
| :--- | :--- | :--- |
| **Look** | Cursor | 好みの形状に変更（Block / Line 等） |
| **Text** | Font | `Cascadia Code` や `MS Gothic` 等 |
| **Window** | Transparency | `Medium` 前後にすると背景が美しく透過します |

### 3. トラブルシューティング
* **仮想化エラー (`0x80370102` 等) が出る**
    * PC 起動時の BIOS/UEFI 設定で **Virtualization Technology (VTx / AMD-V)** が `Enabled` か確認。
* **日本語表示でカーソルがズレる**
    * ターミナルで以下を実行して設定ファイルを更新します：
    ```bash
    echo "Charwidth=ambig-wide" >> `wslpath $APPDATA`/wsltty/config
    ```

---

## III. 削除編（アンインストール）

環境を完全にクリーンアップする場合の手順です。

### 1. wsltty のアンインストール
1.  **[設定]** > **[アプリ]** > **[インストールされているアプリ]** を開く。
2.  リストから **wsltty** を探し、[アンインストール] を実行。

### 2. Linux ディストリビューションの削除
1.  PowerShell を開き、以下のコマンドでインストールされている名前を確認。
    ```powershell
    wsl --list
    ```
2.  登録を解除（データも削除されます）:
    ```powershell
    wsl --unregister Ubuntu
    ```
    *(※Ubuntu 以外をインストールした場合は、その名前に置き換えてください)*

### 3. WSL 機能の無効化
1.  導入時と同様に **[Windows の機能の有効化または無効化]** を開く。
2.  **[Linux 用 Windows サブシステム]** と **[仮想マシン プラットフォーム]** のチェックを外す。
3.  **Windows を再起動** する。

---

## 🔗 関連リンク
* [Microsoft 公式: WSL のインストール](https://learn.microsoft.com/ja-jp/windows/wsl/install)
* [GitHub: mintty/wsltty](https://github.com/mintty/wsltty)
