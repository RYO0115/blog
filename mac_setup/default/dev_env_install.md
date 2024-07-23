# MacBook Air 開発環境構築


## 概要
今回はPythonとTypescriptの開発環境を入れていきます。

また追々C＋＋環境もApple clangのものではなくgccのg++を使えるようにしたいと思っています。

## Homebrew Install

Mac環境に数々のツールを入れるための必須ツールであるパッケージ管理ツール[Homebrew](https://brew.sh/index_ja)をインストールしていきます。

インストールは簡単です。

まずはターミナルを開き、以下のコマンドを順番に実行していきます。

    /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"


    (echo; echo 'eval "$(/opt/homebrew/bin/brew shellenv)"') >> /Users/ryota_amano/.zprofile

    eval "$(/opt/homebrew/bin/brew shellenv)"

これでインストール完了です。

## Git

### インストール

次にコード管理のためにGitをinstallしていきます。

ここではHomebrewからインストールします。

    brew install git

Gitのインストールはこれで完了です。

### ユーザ名、アドレスの設定

次にGitでコミットするために必要なユーザ名とメールアドレスを設定します。

    git config --global user.name "ユーザ名"
    git config --global user.email "メールアドレス"
    
これでコミットなどのGit機能が使えるようになります。


## Python3.9以降 環境構築 (pyenv + pipenv環境構築)

以前WSLで構築したpyenv + pipenv環境を構築していきます。

M2 Macbook AirなのでPython3.9以降を想定した環境を構築していきます。

### pyenvインストール

pyenvもHomebrewでインストールしていきます。

    brew install pyenv

これでpyenvがインストールされたので、次にPythonのバージョンを指定してインストールしていきます。

まずはインストール可能なバージョンを確認します。

    pyenv install -l

これでインストール可能なバージョンがリストで表示されます。

今回は一番最新の3.11.4をインストールしていきます。

    pyenv install 3.11.4

しかしここで問題が、

下のようにlzmaというライブラリがないですよ、とwarningがでます。

一度pyenvでインストールしたpython 3.11.4をuninstallします。

    pyenv uninstall 3.11.4

綺麗な状態で足りないライブラリと設定をしていきます。

    brew install openssl readline sqlite3 xz zlib tcl-tk

これで再度pyenv installを試していきます。

    pyenv install 3.11.4

これで無事にインストールできました。

### pyenv 適用

自分はどのディレクトリでもこの3.11.4をベースに動いて欲しいので以下のコマンドを実行しました。

    pyenv global 3.11.4

もし特定のディレクトリでのみ動いて欲しいのであれば、そのディレクトリに移動した後に以下のコマンドを実行してください。

    pyenv local 3.11.4

次にルートディレクトリにある ~/.zshrcファイルに以下の設定を追加します。

    export PYENV_ROOT="$HOME/.pyenv"
    command -v pyenv >/dev/null || export PATH="$PYENV_ROOT/bin:$PATH"
    eval "$(pyenv init -)"

その後この設定ファイルを再度読み込むためにターミナルを再起動する or 以下のコマンドを叩いてください

    source ~/.zshrc

これでpyenvのインストールは完了です。

### pipenvインストール

Pythonの仮想環境を作成するためにpipenvをインストールしていきます。

pipenvのインストールは先ほどpyenvでインストールした中に入っているpipを使ってインストールします。

    pip install pipenv

### pipenvを使った環境構築

pipenvの使い方についてはこちらの記事を参考にして下さい。

## TypeScript/JavaScript環境構築

ここではTypeScript/JavaScriptの開発環境を構築するために以下の各種ライブラリ等をインストールしていきます。

- nodebrew
  - Node.jsのバージョン管理ツール
- Node.js
  - JavaScriptアプリを作る強い味方. さまざまな機能がライブラリ化されてます。
- 


### nodebrew/Node.jsのインストール

[公式のインストーラ](https://nodejs.org/ja)もありますが、ここもHomebrewでインストールしていきます。

    brew install nodebrew

次にnodebrewの設定をします。

    nodebrew setup

これで必要な設定が$HOME/.nodebrew/に生成されます。

これを.zshrcにもパスを書いていきます

下の一文を~/.zshrcに追加してください。

    export PATH=$HOME/.nodebrew/current/bin:$PATH

またまた設定を反映するためにターミナルを再起動 or 以下のコマンドで反映してください。

    source ~/.zshrc

その後最後にnodebrewインストール後に最新版のNode.jsをインストールします。

    nodebrew install-binary latest

念の為、インストールしたNode.jsのバージョンを確認しましょう。

    node -v

これでバージョン情報が表示されたらインストール成功です。


### TypeScriptのコンパイラインストール

Node.jsをインストールするとnpmというJavaScriptのライブラリ管理ツールもインストールされます。

こちらを使ってコンパイラをインストールしていきます。

    npm install -g ts-node
    npm install -g typescript

最後にコンパイラのバージョンを確認してインストールできているかを確認します。

    tsc -v

これでバージョンが表示されればコンパイラのインストール完了です。

### Prettier(コード整形ツール)インストール

まずはパッケージ管理ツールのyarnをインストールします。

    npm install -g yarn

このyarnを使ってPrettierをインストールします。

    yarn add -D 'prettier@^2'

### ESLint(コーディング規約チェックツール)インストール

コーディング規約に準拠しているかを自動で確認してくれるESLintをインストールします。

    yarn add -D 'eslint@^8'

