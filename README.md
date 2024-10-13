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
- [x] 10２部: イメージの基礎
- [x] 11２部: Dockerfile の基礎
  - `$ docker (image) build [option] <path>`
    - -f Dockerfileを指定する（複数のDockerfileを使い分ける）
    - -t ビルド結果にタグ付けし、把握しやすくする
  - `$ docker (image) history [option] <image>`

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

---

- [x] 12３部: 構成の全体図
- [x] 13３部: イメージのビルド
  - [x] Appイメージのビルド
  - ベースイメージの指定(ubuntu20.04)
  - PHP のインストール
  - PHP の設定ファイルを追加
  - msmtp のインストール
  - msmtp の設定ファイルを追加
  - (確認用コマンド群のインストール)
    - `docker build -t docker-practice:app -f docker/app/Dockerfile .`
  - [x] DBイメージのビルド
  - ベースイメージの指定(Appleシリコン製MacでMySQL5.7を使う場合)
    - MySQL5.7系にはlinux/arm64/v8アーキテクチャが存在しない
    - `FROM --platform=linux/amd64 mysql:5.7`は非推奨(環境依存性が高まる為)
    - Dockerfileは`FROM mysql:5.7`とした上で、ビルド時に`--platform=linux/amd64`とオプションを付ける
  - MySQL の設定ファイルを追加
    - 内容はDockerというよりMySQLについて調べる
    > [参考:基礎MySQL my.cnf (設定ファイル)](https://qiita.com/yoheiW@github/items/bcbcd11e89bfc7d7f3ff)
  - M1系MacでMySQL5.7系を使う場合のビルド(8.0系ならアーキテクチャはarm64/v8を選ぶ)
    - `docker build --platform=linux/amd64 -t docker-practice:db -f docker/db/Dockerfile .`
  - `docker-compose.yml`の中でplatformの指定もできる（毎回手動でオプションを付ける手間が省ける）
- [x] 14３部: コンテナの起動
  - MySQLコンテナ起動には環境変数の指定が必要(docker-compose.ymlに書ける)
    - `MYSQL_ROOT_PASSWORD`,`MYSQL_USER`,`MYSQL_PASSWORD`,`MYSQL_DATABASE`
- [x] 15３部: ボリューム
  - コンテナの終了と共に全てが消滅せず、データをホスト側に永続化させられるようにする
  - `$ docker volume create [option]`
  - `--volume`と`--mount`2つのオプションはどちらも同じような結果
    - volumeの方が短く書けるが、mountの方を推奨
    - mountの方が`docker-compose`と互いに読み替え易いメリットがある
    - volumeの実体の詳細確認
      - `$ docker volume inspect docker-practice-db-volume`
- [x] 16３部: バインドマウント
  - [x] AppコンテナにPHPソースコードをマウントする
    - 既存のPHPディレクトリをそのままマウント→変更が即時反映されるようにできる
    - この場合も`--volume`と`--mount`どちらも使えるが、mountの方が後々望ましい
    - さらにPHPのビルトインサーバのドキュメントルートをソースコードのある/srcに変更
  - [x] DBコンテナに初期化クエリ(テーブル作成)をマウントする
    - ただし初期化クエリは既にデータが存在する場合は実行されない
- [x] 17３部: ポート
  - コンテナのポートをホストマシンに公開し、ブラウザからアクセスできるようにする
- [x] 18３部: ネットワーク
  - コンテナ間で通信できるようにする（ポートでは解決できなかった問題）
  - `$ docker network create [option] name`
  - `$ docker network ls`
  - execサブコマンドでpingを使って疎通の確認
    - `docker container exec -it app ping db -c 3`
  - [x] App コンテナから DB コンテナへの接続設定
    - src/history.php & src/mail.php
  - [x] AppコンテナからMailコンテナへの接続設定
    - msmtp(設定ファイル`docker/app/mailrc`の修正→再ビルド)
      - **イメージのビルド時にCOPYしたファイルなので、要再ビルド**
      - イメージの再ビルド→既存コンテナの終了→コンテナ再起動
- [ ] 19３部: Docker Compose
- [ ] 20３部: デバッグノウハウ ( 番外編 )
- [ ] 21おわりに

#### 第3部のTIPS

- **通常はDockerfileをいきなりゼロから書かない**
- まずはベースイメージをただ起動して、内部のbashでいろいろ試した上で、通ったコマンドをペーストする、というサイクルになる
- FROM を書くときは、Docker Hub で探す
- RUN を書くときは、DockerというよりもLinuxの知識が必要
- COPY を書くときは、その製品の知識(設定ファイルの書き方)が必要
- 手順が定かでない場合、 まずはコンテナを起動し内部で手作業してみるのが有効
- 上記の手順を経てレイヤーを重ねる操作が確定してから最後にDockerfileを書く

##### バインドマウント

###### AppコンテナにPHPソースコードをマウントする

コンテナ内部とホスト側と双方向で変更に関心がある場合。
PHPのソースコード変更に対し、転送などの作業不要で即時反映されるようになる。
コンテナ内部及びホスト側での変更（ファイルやディレクトリ削除も含む）が完全に一致する。

```bash
docker run --name app --rm --detach -it \
      --mount type=bind,src=$(pwd)/src,dst=/src \
      docker-practice:app php -S 0.0.0.0:8000 -t /src
```

***＊＊要注意事項＊＊***
ボリュームの一方向性（たとえコンテナ側で全削除してもホスト側に影響がない）に比べて、バインドマウントは使い捨てのコンテナだからと気軽に`rm -rf *`などやるとホスト側の同ディレクトリに波及して**えらいこと**になりかねない。

###### SQL5.7イメージの拡張機能

コンテナ起動時に`/docker-entrypoint-initdb.d`に存在する .sql を実行してくれる拡張がされている。
これを利用し、コンテナ起動時にvolumeをマウントすると共に、テーブル初期化も行える。

```bash
$ docker run --name db --rm --detach --platform linux/amd64 \
       --env MYSQL_ROOT_PASSWORD=root --env MYSQL_USER=user \
       --env MYSQL_PASSWORD=pass --env MYSQL_DATABASE=event \
       --mount type=volume,src=docker-practice-db-volume,dst=/var/lib/mysql \
       --mount type=bind,src=$(pwd)/docker/db/init.sql,dst=/docker-entrypoint-initdb.d/init.sql \
       docker-practice:db
```

上記の後、dbコンテナ内部に接続してテーブルの存在確認ができればOK

```bash
$ docker exec -it db mysql -h localhost -u user -ppass event

>mysql show tables;
+-----------------+
| Tables_in_event |
+-----------------+
| mail            |
+-----------------+
```

###### ポートの公開

`$ docker container run -p` (or --publish) host-machine:container
ホストマシン側のポートからコンテナ内のサーバのポートにアクセスする
**ホストマシン側のポート番号は任意だが、1024番以下は予約番号のため避ける**
*8000番台も使われていることが多い。競合した場合、正常な動作は期待できない*
