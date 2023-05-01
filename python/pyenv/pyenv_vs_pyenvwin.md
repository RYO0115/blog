# pyenv-winとWSL内のpyenvが干渉したので直してみた

## 概要

先日以下の記事で書いたように、Windowsにpythonを入れていなかったのでpyenv-winをインストールしました。

[https://ryo-udon.hatenadiary.jp/entry/2021/08/01/144310:embed:cite]


ただ正直Windowsでの動作確認程度の用途しかなく、そろそろ20.04のWSL環境にもpyenvを入れて運用しようとしてみたら、


### あれ？動かなくない？

そう。動かないのです。

正確には~/.bashrcにpyenvのパスを設定するのですが、その際に以下のようなエラーがでます。

    bash: /mnt/c/Users/pastm/.pyenv/pyenv-win/bin/pyenv: /bin/sh^M: bad interpreter: No such file or directory
    HOME: command not found
    bash: /mnt/c/Users/pastm/.pyenv/pyenv-win/bin/pyenv: /bin/sh^M: bad interpreter: No such file or directory

ん？pyenv-win？なぜpyenv-winを見に行くの？

どうやらWindows側の環境変数を参照しているらしく、pyenv-winの際に設定したPYENV_ROOTとPATHに引っ張られてwindows側のpyenvにアクセスしてしまっているようです。

というわけで今回はその解決方法を見つけたので備忘録がてら書いていきたいと思います。


## 直し方

先に直し方を説明します。

ちなみにこれは先にpyenvのインストール手順を踏んだ後に実行しても問題ありません。(あくまでPATHの問題なため)

直し方はこちらの[redditの記事](https://www.reddit.com/r/learnpython/comments/siqhqf/wsl_python_refers_to_windows_pyenv/)を参考にしました

直し方はシンプル。

/etc/profile に以下の一文を追加するだけ。

    PATH="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/usr/lib/wsl/lib:/snap/bin"

何をしているかというと、単純にPATHをここで上書きしています。

後は

    source /etc/profile
    source ~/.bashrc

でこのPATH問題は解決します。


## ついでにpyenvのWSL2 Ubuntu20.04へのインストール手順

こちらは何度も焼き増しされていますが、[こちらの記事](https://qiita.com/neruoneru/items/1107bcdca7fa43de673d)を参考にしました。

まずは各update, upgrade(久々に起動したせいか、かなりの量があってびっくり)

    sudo apt update
    sudo apt upgrade

次に各種ライブラリのインストール

    sudo apt install -y build-essential libffi-dev libssl-dev zlib1g-dev liblzma-dev libbz2-dev libreadline-dev libsqlite3-dev libopencv-dev tk-dev git

次はpyenvの本体のインストール。UbuntuなどのLinuxではgitからリポジトリをクローンしてきます。

ここでは丁度一週間前にv2.3.1がリリースされていたのでこちらにしました。

    git clone https://github.com/pyenv/pyenv.git ~/.pyenv

    cd ~/.pynev

    git checkout v2.3.1

最後に問題になった各種PATHの設定

    echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.bashrc

    echo 'export PATH="$PYENV_ROOT/bin:$PATH"' >> ~/.bashrc

    echo 'eval "$(pyenv init --path)"' >> ~/.bashrc

    source ~/.bashrc


これでインストールは完了です。


## 最後に

というわけで最初はpyenvが使えなくて焦りましたが、結論から言えば割と簡単に直すことができました。

また何か不具合などがあれば随時更新したいと思います。

ではでは。