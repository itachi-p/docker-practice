# 『実践Docker』の実践

## 方針

- プログラミングで食ってくことは諦めるが、学習習慣は生涯無くさない
- その為、当面はDocker基礎の反復学習を続ける
- 但し何か他に（当面それは英語で確定だろうが）継続的に学習したい対象が見つかれば置き換える
- 兎に角全て（運命と言ってもいい）は【習慣】次第であり、習慣は練習・訓練であり、訓練は反復である。

### 学習記録

202410040800開始

- [x] 01１部: はじめに
- [x] 02１部: 仮想化とは
- [x] 03１部: Docker とは
- [x] 04２部: Docker を理解するためのポイント
- [x] 05２部: コンテナの基礎操作
- [x] 06２部: コンテナ起動時の基本の指定
- [x] 07２部: コンテナの状態遷移
- [x] 08２部: コンテナの状態保持
  - `Dockerfile`, `volume`
- [x] 09２部: コンテナに接続する
  - `docker (container) exec`
- [ ] 10２部: イメージの基礎
- [ ] 11２部: Dockerfile の基礎


#### TIPS（覚え書き）

- 新旧コマンドについて
  - 新コマンドの方が対象が明確だが、要はimageかcontainerかのどちらか
  - 一度覚えてしまえば敢えてタイプ数の多い新コマンドを使う必要性は低い
  - `docker (container) run` = `docker image pull & docker container create & docker container start`
  - `docker images` = `docker image ls`
  - `docker ps` = `docker container ls`
- dockerコンテナーとはつまり1プロセスとして分離されたnamespaceである
- dockerイメージはレイヤー(UbuntuやMySQLなど)というメタ情報の集積である
  - 既存のイメージをpullするのがDockerfileの`FROM`コマンド
  - 既存のイメージに更にレイヤーを重ねるのが`docker (image) build`コマンド
  - Dockerfileは基本的に既存イメージにレイヤーを重ねる命令を集めたテキストファイル
- `docker compose`は複数のコンテナをまとめて起動し、連携させるオーケストレーションツール
- Kubernetes(K8S)はロードバランサーによるコンテナの自動スケールや自動再起動などを運用するツール。
  - 大規模サービス向けで、個人の開発環境構築レベルではあまり用はなさげ。

##### 起動中のコンテナ内でコマンドを実行

`docker (container) exec [option] <container> command`

例）

```bash
\$ docker exec -it nginx bash
```

- execサプコマンドのオプションは-iと-tのみ
  - `-i or --interactive` コンテナの標準入力に接続
  - `-t or --tty` 元々はTeleTypewriterの略（の名残）。
    - 現在はCLIやターミナルを指す用語
  - 常時この両方を付けていても特に害はない
