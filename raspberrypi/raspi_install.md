# Raspberry Pi 4 ModelBを買ってみた

## 概要
すでに2 B plusを持っていますが、最近さらに性能が上がっているRaspberry Piを購入してみました。

これを使ってWebスクレイピングのBotなどを常時稼働させたりとかしたいと思っています。

今回はその中でRaspberry Piの立ち上げと画像処理の勉強用にROS2のインストールまでを目標にやりたいと思います。

## Raspberry Pi とは？

そもそもRaspberry Piとはなにかご存知ではない方も多いでしょう。

Raspberry Piはなにかというと、

**安価かつコンパクト**なパソコンです。

まず値段についてですが、お得感を感じるためにはその性能から語る必要あるでしょう。

現在最新モデルであるRapberry Pi 4 Model Bの性能は以下のとおりです。

CPU: ARM Quad-core 64bit @ 1.5GHz
RAM(メモリ): 4GB/8GB
インターフェイス: 2.4GHz/5.0GHz Wireless LAN, Bluetooth 5.0, USB3.0 x 2, USB2.0 x 2
ビデオ/オーディオ出力: Micro HDMI x 2(4K60fps), ステレオ音声出力など
電力: 15W程度(マニュアルに5V3A程度と書かれているのでおそらくこのくらい)

この他GPIOピンなども入っているので、かんたんな電子工作からWebブラウジング程度のライトな作業は何でもこなせます。

ここで値段ですが、
最新モデルであるRaspbery Pi 4Bは少し過去のモデルから値段が上がって、

メモリ4GBモデルであれば6500円程度、
8GB(今回はこちらを購入しました)であれば9000円となっています。

これだけの機能がありながら1万円を切るコスパの高さは本当に魅力的です。

というわけで今回は様々なボットを動かすためのサーバとして構築していきたいと思っています。


## セットアップ

### セットアップに必要なもの

セットアップに必要なものはいかになります。

* Raspberry Pi 本体
* USB Type-Cケーブル(給電用)
* micro SDカード
* micro SDカード　<-> USB 変換機
* キーボード/マウス(USB)

ここから下はお使いのディスプレイの端子に合わせて準備してください。

自分の場合は以下を用意しました。

* HDMIケーブル
* micro HDMI <-> HDMI 変換ケーブル

なお、ここで注意ですが、HDMIには大きく分けて三種類規格があります。
1. HDMI
2. mini HDMI
3. micro HDMI

上から下に向けて端子が小さくなります。
自分もやってしまったのですが、間違ってmini HDMIを買わないように気をつけてください。

不安でしたら、下にリンクを貼りますのでこちらを使ってみてください。



### OSインストール

[こちらの公式サイト](https://ubuntu.com/tutorials/how-to-install-ubuntu-desktop-on-raspberry-pi-4#1-overview)を参考にしました。

今まではSDカードにimgを書き込むソフトを使っていましたが、最近になった今更ながら[Raspberry Pi Imager](
https://ubuntu.com/tutorials/how-to-install-ubuntu-desktop-on-raspberry-pi-4#2-prepare-the-sd-card
)
というソフトが提供されていることを知ったので、今回はこちらを説明します。

使い方は簡単です。

Raspberry Piに差す予定のmicro SDカードをメインPC(Mac, Windows, Linux)につないだ状態でソフトを起動した後、
下のような画面がでてきます。

後は左から順番に自分に合わせてOS, 書き込むSDカードを選択していきます。

まず、CHOOSE OS ボタンをクリックし、OSを選択します。


ここではRasbianを始め、Ubuntuのデスクトップイメージなどが選択できます。

今回はUbuntu20.10をインストールしたので、Ubuntu -> Ubuntu 20.10 Desktop (Raspberry Pi 4)　を選択します。

次にSDカードを選択します。
CHOOSE SD CARDをクリックすると今パソコンに接続されている外部ストレージの一覧が出てきます。
この中からRaspberry Pi用のSDカードを選択してWRITEボタンを押せば完了です。

### OS起動

こちらについては画面に従って操作するだけなので説明は省きます。
もし分からなければコメントをいただければお応えします。

なおここからはディスプレイに接続する必要がありますのでケーブルの準備などご注意ください。

### SSH

ネットワークを介して、他のパソコンを操作できるSSHを使う設定をしていきます。
基本は[こちら](https://tool-lab.com/raspi-key-authentication-over-ssh/)のサイトを参考にしました。

今回はpassphrase(パスフレーズ)を使った公開鍵を作っていきます。


#### パスフレーズ?

そもそもパスフレーズとはなにか。
[こちらのサイト](https://it-trend.jp/encryption/article/64-0064#:~:text=%E3%83%91%E3%82%B9%E3%83%95%E3%83%AC%E3%83%BC%E3%82%BA%E3%81%A8%E3%81%AF%E3%80%8110,%E3%81%9F%E3%83%91%E3%82%B9%E3%83%AF%E3%83%BC%E3%83%89%E3%81%AE%E4%B8%80%E7%A8%AE%E3%81%A7%E3%81%99%E3%80%82)
を参照すると以下のように説明されています。

> パスフレーズとは、10文字以上の単語（フレーズ）を組み合わせて作られたパスワードの一種です。
> パスフレーズとは、本人認証で用いるパスワードの一種です。通常のパスワードが８文字前後のところ、パスフレーズは10文字以上の単語を組み合わせた文字列で構成されているため、セキュリティが強固になります

要は、一般的なパスワードと違い、

**文字列が長いため不正にパスワードを暴く「総当り攻撃」に対して有効**

ということです。


#### 設定
以下の手順で実施していきます。

1. メインPC上で鍵ペアファイル作成
2. 1.で作成した鍵ファイルのうち、公開かぎファイルをRaspberry Piに送信
3. Raspberry Piのsshサーバ設定ファイルを編集
4. Mac or Windows PC側でのRaspberry Piの登録


##### 鍵ペアファイルの作成
まず以下のコマンドを実行します。

    ssh-keygen -t rsa

ここで-tのオプションで指定しているの公開鍵の形式です。
作成できるキーペアは以下の4つのようです。

* dsa
* ecdsa
* ed25519
* rsa

それぞれの形式の良し悪しについてはまた別に話すとして、今回は最後のrsaで作成しました。

##### Raspberry Piへの公開鍵の登録

    scp id_rsa.pub [raspiのユーザ名]@[raspiのIPアドレス]:

    (最後の:を忘れがちなので注意)

これをするためにはIPアドレスの固定化が必要なので、[以下のサイト](https://mugeek.hatenablog.com/entry/2019/05/27/230256)を参考に設定。

raspberry pi内のターミナルで、

    ifconfig


次に同じネットワーク上に存在するIPアドレスを確認。

Mac or Windows PCでターミナルを立ち上げ、以下のコマンドを実行

    ipconfig /all

ここで使われていないIPアドレスを確認し、合わせてルータのIPアドレスを確認

(ルータについては各社ごとにルータのIPアドレスを確認するソフトが公式に配布されているので、そちらで確認するのがベスト)


ここで調べたIPアドレスをもとに、Raspberry Piのアドレスを固定化していきます。

またRaspberryPiのターミナルに移動し、以下のコマンドを実行。

    sudo vim /etc/dhcpcd.conf

ここではエディタでvimを使っていますが、他のエディタでも可です。
ただsshで接続している場合、geditなどは開けませんのでご注意ください。

重要なのは管理者権限(sudo)で編集するということ。

以下の四行を追加

    interface wlan0
    static ip_address=[固定化したいIPアドレス]
    static routers=[ipconfig /allで調べたルータのIPアドレス]
    static domain_name_servers=[上のルータのIPアドレス]

上記の設定が終わったところでRaspberry Pi を再起動する。

    reboot


再度Raspberry Piが立ちあがったらifconfig でアドレスが固定化されているかを確認して設定は終了。

もし不安だったら、Mac or Windows PCからsshでアクセスできるか確認する。

    ssh [raspiのユーザ名]@[固定化したraspiのIPアドレス]

これでパスワードを要求されるので入力して、ログインできれば設定完了です。

この後のサーバ設定ではssh接続しておくと作業が捗るので試しておくことをおすすめします。

##### sshサーバ設定

sshで　Raspberry Piに接続しているターミナルから以下のコマンドを実行。

    mkdir .ssh

ここがsshを実行する際に公開鍵を探しに行くディレクトリになります。

次に先程scpでRaspberry Piのホームディレクトリにコピーした"id_rsa.pub"ファイルを.sshに移動します。

    cat id_rsa.pub >> .ssh/authorized_keys

次に.sshディレクトリとauthorized_keysの権限を変更していきます。

(グループ権限、他のユーザの権限を無効にします。)

    chmod 700 .ssh
    chmod 600 .ssh/authorized_keys

その後、もう必要はないのでid_rsa.pubを削除します。

    rm id_rsa.pub

次にsshサーバ設定ファイル(/etc/ssh/sshd_config)を編集していきます。

    sudo vim /etc/ssh/sshd_config

まずPortを設定します。

sshだとデフォルトは22番ポートになっています。

ただ、このままだと外部から簡単に不正アクセスされてしまう恐れがありますので、

[こちらのページ](
http://www.vwnet.jp/mura/PortNumbers/tcpip-port.asp
)
を参考に使われていないポートを設定しましょう。


予約済みのポートは同時に使えないので、予約済みになっていないポートを使いましょう。

```profile:sshd_config
#Port 22
Port 49151
```

次にリモートでのRootログインを禁止します。

PermitRootLoginという項目をsshd_configの中から探しだし、yes -> noに変更します。

```profile:sshd_config
#PermitRootLogin yes
PermitRootLogin no
```

最後に公開鍵ファイル認証を有効にします。

```profile:sshd_config
RSAAuthentication yes
PubkeyAuthentication yes
AuthorizedKeysFile ~/.ssh/authorized_keys
```

もしパスワードの入力を省略したい場合はこれに加えて以下の設定を変更します。

```profile:sshd_config
#PasswordAuthentication yes
PasswordAuthentication no
```

これで編集は完了になります。
最後に保存して、編集画面を閉じてください。

その後、設定を反映させるためにMac or Windows PC 内でRasberry piにsshでつないでいるターミナルで以下のコマンドを実行します。


sudo /etc/init.d/ssh restart

これで設定は完了になります。


正常に設定ができているかを確認したい場合はMac or Windows PCで新しくターミナルを開き以下のコマンドを実行してsshできるか確認してください。


    ssh -i [秘密鍵ファイル　このページだと~/.ssh/id_rsa] -p [設定したポート番号 このページだと49151] [raspberrypiのユーザ名]@[raspberry Pi のIPアドレス]


##### Mac or Windows PC側でのRaspberry Piの登録

最後にsshする際に一々上の長いコマンドを打つのは面倒なので、以下のようにユーザ名を設定していきます。

ここでもターミナルを開いてもらって、以下のファイルを編集していきます。

    vim .ssh/config

開いたら、以下の5行を追加します。

```profile:config
Host raspi4b
    HostName [固定化したIPアドレス]
    User [Raspberry Piのユーザ名]
    Port 49151
    IdentityFile ~/.ssh/id_rsa
```

編集終了したら保存して、ファイルを閉じると、以下のコマンドで接続ができるようになります。

    ssh raspi4b

これで接続できれば成功です。

##### 余談

ちなみに私はこれに加えて、mac内の.bashrcを変種し、以下のalias(ショートカット)を足しています。

```profile:.bashrc
alias raspi4b='ssh raspi4b'
```

これで保存すると、ターミナル上で

    raspi4b

と打つだけでssh接続ができるようになります。


### VSCodeの設定

#### Remote Developmentとは？ / インストール方法

私は普段からVSCodeを利用していますが、最近MicroSoft公式でリモートアクセス機能が使えるExtensionが公開されたと聞いたのでその設定をしてみました。

そのExtension の名前が"**Remote Development**"になります。

インストール方法はその他のExtensionと同じく左側のメニューバーからExtemsionを選択し、


検索タブでRemote Developmentを検索します。

似たような名前のExtensionが上位で出てきますが、それを無視して名前が一致するRemote Developmentをインストールしてください。


#### Remote DevelopmentでのSSH接続方法

使い方は簡単です。

1. 左下にある緑のボタンをクリック
2. Remote-SSH Connect Current Window to Host ... または Remote-SSH Coneect to Host ...を選択(後者は新しくwindowを立ち上げる)
3. 接続先を指定(.ssh/configを設定しているとクリックのみで接続可)
4. パスワードを入力(パソワード入力を必要なしにしていた場合3.で接続完了)

上の4ステップでRemote接続ができます。


#### どうやって使えばいいの？
基本的にはメインPCと同じ感覚で作業ができます。

また、VSCode内でターミナルを開くと自動的にSSHした状態のターミナルが開けるので、ディレクトリの作成などの操作も簡単に行えます。


#### Raspberry Pi用 Python環境構築

色々なサイトでRaspberry PiでのPython環境構築についての記事が上がっていますが、
色々と試した結果以下の方法でインストールができました。

##### pyenv

今回はAnacondaのインストールをしましたが、動作の確認などができていないのでひとまず確認できたpyenvのインストールを行います。

**pyenv**はPythonのバージョンを簡単に切り替えられるツールになります。

なので、例えば基本は3.8で開発したいが、一部のアプリは2.7でといったアプリごとに使い分けることができるようになります。

なお、ここでは何もPython関連のライブラリがインストールされていない状況を想定して書いていきます。

#### pyenvのインストール

まず、パッケージリストの更新とインストール済みのパッケージを更新します。

    sudo apt-get update
    sudo apt-get upgrade
    sudo reboot

最後に再起動を行い、改てpyenvをインストールしていきます。

    git clone https://github.com/pyenv/pyenv.git ~/.pyenv

これで必要なパッケージはインストールしました。

次にpyenvを使うためにパスの設定を行っていきます。

    sudo vim ~/.bashrc

.bashrcに以下のようにパスを設定していきます。

```profile:.bashrc
export PYENV_ROOT="$HOME/.pyenv"
export PATH="$PYENV_ROOT/bin:$PATH"
eval "$(pyenv init -)"
```

上の設定が終わったら、保存してファイルを閉じた後、

    source ~/.bashrc

を実行すればパスの設定は完了です。
ちなみにターミナルを立ち上げ直しても反映されます。


#### Python3のインストール

Python3.7 のインストールにはgcc, makeなどのライブラリが必要になります。
なのでまずはそれをインストールしていきます。

    sudo apt install gcc make zlib1g-dev libffi-dev libbz2-dev libssl-dev libreadline-dev libsqlite3-dev python3-tk tk-dev

次にpyenvでインストール可能なバージョンを確認していきます。

    pyenv install --list

こちらのコマンドでインストール可能なバージョンのリストが出てきます。
ここで、anacondaがリストの中に出てきますが、Raspberry PiではCPU(ARM)の関係でインストールできませんのでご注意ください。

今回は3.9.1をインストールしたのでそれを例にしていきます。

    pyenv install 3.9.1
    pyenv global 3.9.1

これでターミナルでpythonを起動する際にpyenvでインストールされたpython3.9.1が起動します。
起動するpythonのバージョンは以下で確認ができます。

    python --version


## 終わりに

というわけでRaspberry Pi 4Bのインストール方法について書いてみました。
以前にもサラッと説明したのですが、自分の備忘録も兼ねて今回、整理しております。

なにかうまくいかないやここがおかしいなどがあればご連絡ください。

ではでは。