---
title: wikijs
description: wikijsの各種手順
published: true
date: 2026-06-07T08:09:22.756Z
tags: 
editor: markdown
dateCreated: 2026-06-07T08:09:22.756Z
---

# wikijs
## インストール
1. 起動用ディレクトリを作成して移動
```bash
mkdir -p docker/wikijs/data/wiki docker/wikijs/data/db
cd docker/wikijs
```
2. docker-compose.yml作成
```docker-compose.yml
version: "3.8"

services:
  db:
    image: postgres:15
    container_name: wikijs-db
    restart: unless-stopped
    environment:
      POSTGRES_DB: wiki
      POSTGRES_USER: wikijs
      POSTGRES_PASSWORD: wikijs
    volumes:
      - ./data/db:/var/lib/postgresql/data

  wiki:
    image: requarks/wiki:latest
    container_name: wikijs
    restart: unless-stopped
    depends_on:
      - db
    ports:
      - "3000:3000"
    environment:
      DB_TYPE: postgres
      DB_HOST: db
      DB_PORT: 5432
      DB_USER: wikijs
      DB_PASS: wikijs
      DB_NAME: wiki
    volumes:
      - ./data/wiki:/wiki/data
```
3. GitHub の Personal Access Token（classic）を取得  
   - GitHub → Settings → Developer settings → Personal access tokens → Tokens (classic)  
   - 「Generate new token」  
   - 権限：`repo`（Private repo を使う場合は必須）  
   - 生成された Token を控えておく

4. GitHub で Wiki.js 用リポジトリを作成  
   - 例：`wikijs-content`  
   - Public / Private はどちらでも可  
   - 空の状態で OK（README なし推奨）

5. Wiki.js 管理画面にアクセス  
   - ブラウザで `http://localhost:3000`  
   - 初回セットアップウィザードで管理者アカウントを作成

6. GitHub 同期設定  
   - 管理画面 → **Administration** → **Storage**  
   - 「Git」を選択  
   - Provider：**GitHub**  
   - Repository：`ユーザー名/リポジトリ名`  
   - Branch：`main`  
   - Authentication：**Personal Access Token**  
   - Token：手順 3 で取得した Token  
   - Sync Direction：  
     - `Push + Pull`（双方向同期）  
   - 「Apply」→「Start Sync」

7. 同期確認  
   - Wiki.js のページを 1 つ作成  
   - GitHub リポジトリに `.md` ファイルが生成されていれば成功  
   - GitHub 側で編集 → Wiki.js 側に反映されることも確認
