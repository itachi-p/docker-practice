# はぢめてのDockerfile
# DockerfileはDockerイメージを作成するための設定ファイル
# DockerfileをビルドすることでDockerイメージが作成される

# 公式のUbuntuイメージをベースに、行番号が表示されるvimをインストール
# その後、コンテナが起動した際にデフォルト命令として現在時刻を指定のフォーマットで表示する

# ベースイメージの指定 (ubuntuのLTSバージョンを指定)
FROM ubuntu:24.10

# DockerHubにある公式イメージは軽量さのためにパッケージが最小限しか入っていない
# そのため、追加でパッケージをインストールする必要がある
RUN apt update
RUN apt install -y vim

# インストールしたVimの設定ファイルをローカルからコンテナ内部へコピー
COPY docker/date/.vimrc /root/.vimrc

# コンテナが起動した際に実行されるデフォルト命令を指定
# 以下の書き方は非推奨 (実行されるが、warningが出る)
# CMD date +"%Y/%m/%d %H:%M:%S ( UTC )"

# JSON形式で書くのが推奨されている
CMD ["date", "+%Y/%m/%d %H:%M:%S ( UTC )"]
