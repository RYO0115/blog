# Docker + WSL2 をインストールしてみた + ROSも動くようにしてみた。

## 概要

最近開発環境を何度も複製する機会があり、そろそろいい加減開発環境を簡単に複製できるようにすべきだなーと思い、重い腰をあげてDockerをインストールしてみましたのでまとめてみます。

## WSL2インストール

こちらは過去のこちらの記事を参照ください。

## Docker Engine インストール

今回、Docker Desktopは無しでインストールしたいと思います。

理由はDocker Decktopは商業利用が有償なため、個人利用でこれに慣れすぎるといざ仕事で使いたいと思ったときに悪影響がでそうだなと思ったからです。

では、早速インストールしていきます。

今回は[こちら](https://qiita.com/blueskyarea/items/ef262768c8f8f2ebe990)と[こちら](https://zenn.dev/sprout2000/articles/95b125e3359694)を参考にしました。


### 必要なライブラリのインストール

以下のコマンドを入力

    sudo apt install curl apt-transport-https ca-certificates gnupg lsb-release

### Docker公式GPG鍵を追加

    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

### Docker安定版のレポジトリを追加

    echo "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

### レポジトリのアップデートとDocker Engineのインストール

    sudo apt install docker-ce docker-ce-cli containerd.iosudo docker-compose

### ユーザをDockerに追加

    sudo gpasswd -a (ユーザ名) docker

Adding user (ユーザ名) to group docker　と出れば成功

## Docker デーモンの起動

    sudo service docker start


## (余談) 下のテストでdocker run コマンドをたたいてpermission deniedが出る場合

これは、/var/run/docker.sockがユーザでも編集可能になっていないことが原因です。

なので、chmodします。

    sudo chmod 666 /var/run/docker.sock

これで問題なくdocker コマンドを実行可能になります。

## Dockerが動くかのテスト

こちらの公式にあるHello Worldコンテナで試してみます。

    docker run hello-world


これでエラーなく起動できればDockerのインストール成功です。


## さて本題。WSL2 上にROSをインストールしてみる

今回のモチベーション。DockerでWSL2上でGUIを使えるROSをインストールしてみました。

参考にしたのは[こちら](https://qiita.com/Tiryoh/items/ed7eecfac2ca9c7bf09d)

以下のコマンドを起動するとROSが入ったUbuntuが起動します。

    docker run -p 6080:80 --shm-size=512m tiryoh/ros2-desktop-vnc:dashing

後は、好きなブラウザで http://127.0.0.1:6080/ にアクセスするとGUI画面がブラウザ上に表示され、確認できます。

![gazebo]()

ここではGazeboなどが動くのを確認しています。

## 終わりに

というわけでWSL2でROS環境を構築してみました。

今後はDOckerの勉強をしつつ、自動で静的解析をしてくれるような環境の構築などもやっていきたいなとおもっています。

ではでは。

