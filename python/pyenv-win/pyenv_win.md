# pyenv-win をWindows10にインストールしてPython開発環境を整えたよ

## 概要

最近C++やhtmlなどばかりやっていたせいか、windows環境にPythonを入れていないことに先ほど気づいたので入れてみました。

個人的にやっぱりこのあたりの環境構築はLinux, Unix系のほうがやりやすいなという印象。

何ならこの間WSLを入れたので、最初からそっちで開発すれば良かったのではないか説もあります。。。

それはさておき、pyenv-winのREADMEにインストール方法がまとまってはいるのですが、意外と落とし穴もあったのでそこを備忘録がてらまとめたいと思います。


## そもそもpyenvってなに？
pyenvとは大量にあるPythonの環境を簡単に切り替えてくれる便利ツールです。

Pythonは現在リリースバージョンで3.9.6まで出ています。

Pythonを初めて使う人は何も考えずに最新のリリースバージョンをインストールすればいいのですが、

長年Pythonを使って開発していると昔のバージョンなら使えた機能が無くなるや形式が変わるなどといった問題が出てくることがあります。

もちろんライブラリやシステム側でそういったバージョンの違いを吸収できるように作るべきなのですが、それも中々に大変な作業です。

そこで、このシステムを使う時はPythonのこのバージョンを、という形でバージョンを切り替える機能が求められているわけです。

そこで活躍するのがpyenvです。

pyenvは複数のpythonバージョンを仮想環境として保持することができ、その環境間を簡単に切り替えることができます。

というわけで、Pythonで開発する上で欠かせないpyenvをインストールしたのでその手順を説明していきます。

## (余談)Anaconda

実はpyenvの他にAnacondaと呼ばれる、pythonバージョンを管理できるとても便利なツールがあったのですが、

つい最近商業利用が有償になってしまいました。

今回、個人利用しか考えていませんが、念のためAnacondaは使わずpyenvのみで運用していきます。


## pyenv-winインストール手順

pyenvはもともとLinux, Unix(Mac)向けのツールになっていて、そのままではWindowsでは使えません。

ですが、最近ではそれをWindowsでも使えるようにしてくれた[pyenv-win](https://github.com/pyenv-win/pyenv-win)がGithubで公開されています。


Install方法についてもREADMEに簡潔にまとめられており、かなり簡単にインストールできるようになっています。

開発者の方には頭が上がりません。

### 1. pyenv-win ダウンロード

まず初めにpyenv-winのパッケージをインストールします。

Githubから取ってくる形になるので、インストールは主に以下の二つの方法があります。


* zipファイルをダウンロードする
* git cloneしてくる

まず最初に、最終的にインストールするとどういうディレクトリ構成になるかを紹介します。


    C:\Users\<User>\.pyenv
    ├───pyenv-win
        ├───bin
        ├───libexec
    ...

#### zipファイルをダウンロードする場合

1. [リンク先](https://github.com/pyenv-win/pyenv-win/archive/master.zip)からpyenv-winをダウンロードします。
2. 『.pyenv』ディレクトリ(フォルダ)がない場合、『C:\Users\(自分のユーザ名)\』に.pyenvディレクトリを作成します。
3. ダウンロードした.zipを解凍し、中に入っているpyenv-winを2.で作った.pyenvの中に移動します

#### git cloneする場合
git clone する場合は以下のコマンドをたたくだけです。

    git clone https://github.com/pyenv-win/pyenv-win.git "$HOME/.pyenv"

### 2. Pythonアプリインストーラの無効化
ここから先に進む前に自分のwindowsのバージョンを確認してください。

Windows10 1905よりも新しいバージョンの場合、Python App Installerがデフォルトで有効になっています。

その場合、ここから先の作業をするまえに無効化する必要があります。

やり方は簡単です。

下の画像のように左下に『アプリ実行エイリアスの管理』と打ち込んで検索をかけて、実行してください。




すると次のようにアプリの一覧が出てきますので、この中からPython, Python3のアプリインストーラをオフにしてください。




### 3. pyenv-winの環境変数を設定する

次に.pyenvに移動したpyenv-winを実行できるようにpyenvの環境変数の設定をしていきます。

まず、左下のスタートメニューを右クリックし、Powershellを起動します。

その後、以下のコマンドを実行します。

    [System.Environment]::SetEnvironmentVariable('PYENV',$env:USERPROFILE + "\.pyenv\pyenv-win\","User")
    [System.Environment]::SetEnvironmentVariable('PYENV_HOME',$env:USERPROFILE + "\.pyenv\pyenv-win\","User")

これでpyenvの環境変数は設定完了です。

### 4. Pathを設定する

最後にパソコンがpyenvを見つけられるようにPathを設定していきます。

同じくPowershellを開き、以下を実行します。

    [System.Environment]::SetEnvironmentVariable('path', $env:USERPROFILE + "\.pyenv\pyenv-win\bin;" + $env:USERPROFILE + "\.pyenv\pyenv-win\shims;" + [System.Environment]::GetEnvironmentVariable('path', "User"),"User")

## pyenvが使えるかを確かめる

ここまで出来たら動くかを確認していきます。

ターミナルやpowershellを起動してもらい、以下を入力してください。

    pyenv --version

これでダウンロードしたpyenvと同じバージョンが表示されればインストールの完了です。


また

    pyenv

と打つとpyenvのコマンド一覧を確認できます。


## pyenvの使い方

### pyenvに様々なversionのPythonをインストールする (pyenv install)

このコマンドは様々なPythonのバージョンをインストールするコマンドです。

どのPythonがインストールできるかは

    pyenv install -l

で確認ができます。

これを実行すると以下のようにインストール可能なバージョンが大量にリストになって表示されます。

この中からインストールしたいバージョンを選び次のコマンドを実行するとインストールが完了します。

    pyenv install 3.9.6

### インストール済みのPythonバージョン一覧を確認する( pyenv versions)

今、どのバージョンがインストール済みかを確認したい場合、こちらのコマンドを実行すると、

インストール済みのバージョンの一覧が表示されます。

また、pyenvの仮想環境が実行済の(現在すでに動いている)場合、動いているバージョンの頭に*がついて、どれが実行されているかを確認できます。

### インストールした環境を実行する( pyenv exec )

pyenvの仮想環境を立ち上げる場合は以下のコマンドを実行します。

    pyenv exec [バージョン]

なお、実行前にそのバージョンがインストールされている必要があります。


### 指定した環境を自動的に実行するようにする( pyenv global)

例えばバージョン3.9.6を常に自動的に実行されるようにしたい場合は以下のようにコマンドを設定します。

    pyenv global 3.9.6



## 終わりに

というわけで、今回はwindowsにPython環境を構築する方法について説明しました。