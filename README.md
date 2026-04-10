# Ollama起動用Docker Compose
## 目次

- [Ollama起動用Docker Compose](#Ollama起動用Docker Compose)
  - [目次](#目次)
- [概要](#概要)
  - [コンテナ](#コンテナ)
  - [機能](#機能)
- [前提](#前提)
  - [Linux、Docker](#linuxdocker)
- [準## 現在のUID、GIDを調べる](#準-現在のuidgidを調べる)
  - [.envファイルを作る](#envファイルを作る)
    - [基本情報（実行ユーザー情報、バージョン等）](#基本情報実行ユーザー情報バージョン等)
- [使い方](#使い方)
  - [初回](#初回)
    - [docker-compose.ymlの作成](#docker-composeymlの作成)
    - [Dockerイメージの取得](#dockerイメージの取得)
    - [Dockerコンテナイメージのビルド](#dockerコンテナイメージのビルド)
  - [初回、あるいは、Ollamaのモデルをダウンロード・更新するとき](#初回あるいはollamaのモデルをダウンロード更新するとき)
    - [Ollamaのモデルをダウンロード・更新](#ollamaのモデルをダウンロード更新)
  - [毎回](#毎回)
    - [コンテナの起動](#コンテナの起動)
    - [コンテナの終了](#コンテナの終了)
    - [Ollamaの起動](#ollamaの起動)

# 概要
## コンテナ

- llm-rag-examples-ollama: ローカルでLLMを起動します。

## 機能

- Ollama
  - ローカルのLLMとして使用します。

# 前提
## Linux、Docker

LinuxにDocker、Docker Compose（v2以降）が入っている前提とします。

（`docker-compose`でなく、`docker compose`のコマンドを使います。）

また、Linuxの実行ユーザーが`docker`グループに所属しているか、していない場合は`root`ユーザーで実行していることとします。

なお、作者はWindows 11 homeにWSL2をインストールし、Ubuntu 24.04上で作成しました。

Ubuntu上でDocker、Docker Composeをインストールする方法が分からない方は、作者がQiitaに書いた、[Ubuntu 22.04にDocker Engine、Docker Composeをインストールする手順](https://qiita.com/murakami77/items/98ef607dc4ff0ae9a497)をご参照ください。

# 準## 現在のUID、GIDを調べる

```bash
id
```

コマンドで、uidとgidを調べます。

## .envファイルを作る
### 基本情報（実行ユーザー情報、バージョン等）

まず、.env.example ファイルをコピーして .env ファイルを作成します。

次に、その内容を編集します。

まず、

- APPUSER_UID・・・先程調べたUID
- APPUSER_GID=・・・先程調べたUID

を変更します。

次に、

- APPUSER_NAME

については、コンテナ内のユーザーを作成する際のユーザー名を設定しており、変更不要ですが、自分で好きなユーザー名を使いたい場合は変更頂いても結構です。

なお、下記の項目は、基本的に変更しないでください。

- PYTHON_VERSION
- PIP_VERSION

※最新のバージョンを使いたい場合は変更できますが、自己責任でお願いします。

# 使い方
## 初回
### docker-compose.ymlの作成

GPUがあり、CUDA Toolkit、NVIDIAドライバがインストールされている環境の場合：

```bash
ln -s docker-compose.gpu.yml docker-compose.yml
```

GPUなし、上記ドライバ等のインストールがない場合：

```bash
ln -s docker-compose.cpu.yml docker-compose.yml
```

### Dockerイメージの取得

```bash
docker compose pull ollama
```

### Dockerコンテナイメージのビルド

```bash
./docker-compose-build.sh
```

## 初回、あるいは、Ollamaのモデルをダウンロード・更新するとき
### Ollamaのモデルをダウンロード・更新

【注意】
GPUなしの環境では、起動および実行を全てCPUで行うため、非常に時間がかかることがあります。
GPUありの環境を推奨します。

```bash
cd ./ollama/
```

中に入っているシェルスクリプトのうち、使用したいモデルの `********.init.sh` を実行します。
※今後、使えるモデルを増やしていきたいと思っています。

## 毎回
### コンテナの起動

```bash
./compose-reup.sh
```

【注意】

最初に起動する際、venv仮想環境作成、pipパッケージのインストールのため、非常に時間がかかります。

※下記の作者の環境では30分ほどかかりました。

- CPU : Intel(R) Core(TM) i5-10300H CPU @ 2.50GHZ 4コア・8論理プロセッサ
- GPU：NVIDIA GeForce RTX 2060
- RAM：16.0GB
- OS：Windows 11 Home
- Linux：WSL 2 / Ubuntu 24.04

### コンテナの終了

コンテナを使い終わったら、

```bash
./compose-down.sh
```

で終了します。


### Ollamaの起動

【注意】
GPUなしの環境では、起動および実行を全てCPUで行うため、非常に時間がかかることがあります。
GPUありの環境を推奨します。

```bash
cd ./ollama/
```

中に入っているシェルスクリプトのうち、使用したいモデルの `********.run.sh` を実行します。
※今後、使えるモデルを増やしていきたいと思っています。
