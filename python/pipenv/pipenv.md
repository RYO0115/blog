# WSL + VSCode + pyenv + pipenv 環境を自分なりに構築してみた(しなおしてみた)

久しぶりに余裕ができたので自分のパソコンで最近はやりのOpenAIのAPIでも叩いてみようかと思っていたら、

「あれ？Python動かねえ。。。？」

結論から言うとWSLで起動するUbuntuバージョンが違うだけだったのですが、せっかくなのでWSLのUbuntuを22.04にして、python環境を構築しなおしてみました。

また、これまで避けてきたpipenv環境を構築し、vscodeで起動時にPIPENV_VENV_IN_PROJECTを自動的に設定するところまでやってみました。

では、やっていきましょう。

## WSL環境の構築, pyenvのインストールについて

WSL環境の構築, pyenvのインストールについては、すでに過去に幾つか記事を書いていますので、そちらを参照ください。

WSL環境の構築はこちらの記事で詳細に説明しています。

[https://ryo-udon.hatenadiary.jp/entry/2021/07/23/124046:embed:cite]

pyenvのインストールは以下の記事の後半で説明しています。

[https://ryo-udon.hatenadiary.jp/entry/2022/06/04/155108:embed:cite]

こちらの記事ではwindowsにすでにpython環境を作成済みの方のお悩み解決になるかと思います。

## 今回実際に構築した環境+プロジェクト

[こちら](https://github.com/RYO0115/chatgpt_prompt_engineering)のリポジトリになります。

pipenvが関係するのは、このルートディレクトリにいるPipfile, Pipfile.lockになります。

## 注意書き

ここから先はpyenvでpython 3.11.3を起動した、という前提で説明していきます。

なので、コマンドとしては以下が行われた状態で実行していきます。

    pyenv install 3.11.3
    pyenv global 3.11.3

## Pipenv

### pipenvのインストール

installはpipを使います。

    pip install pipenv

これだけでpipenvがインストールされます。


### pipenvの初期化

まずpythonのバージョンに併せてpipenvを初期化します。

私は今回Python3.11.3で環境を作ったので、以下のコマンドを実行します。

    pipenv --python 3.11

このコマンドを実行するとPipfileというファイルが自動作成されます。

Pipfileは、その開発環境にどんなパッケージがどのバージョンでインストールされているかを管理しています。

例えば上のレポジトリのPipfileを見ると

```
[[source]]
url = "https://pypi.org/simple"
verify_ssl = true
name = "pypi"

[packages]
numpy = "*"
matplotlib = "*"
openai = "*"
ipykernel = "*"
python-dotenv = "*"

[dev-packages]
ipykernel = "*"

[requires]
python_version = "3.11"
python_full_version = "3.11.3"

```

基本的にこのファイルを手動で操作することは**ありません**。

なので、コミットの際に忘れずこちらのPipfile, Pipfile.lockを含めれば、この次に紹介するコマンドで簡単に開発環境を再構築することができます。

### pipenvでのパッケージ(ライブラリ)インストール

#### 新しくパッケージを追加する場合

新しくパッケージをインストールする場合、pip installの代わりにpipenv installを使用します。

    pipenv install numpy

このコマンドを実行すると、指定したパッケージがインストールされるだけではなく、このPipfileの[packages]の部分に追記されます。

開発用パッケージとしてinstallしたい場合は

    pipenv install --dev ipykernel

のようにして区別してインストールできます。

#### 新しくパッケージをバージョンを指定して追加する場合

パッケージのバージョンを指定してインストールすることもできます。

    pipenv install "pandas==0.15.0"

ただし[公式サイト](https://pipenv-ja.readthedocs.io/ja/translate-ja/basics.html#specifying-versions-of-a-package)を参照すると、この指定方法だとpipenvがアップデートしなくなる、ということから

    pipenv install "pandas~=0.15.0"

を推奨しているようです。

またこのバージョン以上、という指定であれば、

    pipenv install "pandas>=0.15.0"

バージョン以下であれば、

    pipenv install "pandas<=0.15.0"

このバージョンより上、であれば、

    pipenv install "pandas>0.15.0"

となります。


#### Pipfileから環境再現(指定されたパッケージを最新バージョンでインストール)

こちらのPipfileから環境再現ももちろんできます。

    pipenv install

もし開発用パッケージもインストールする場合

    pipenv install --dev

上の例として上げたレポジトリの場合は後者のコマンドを実行することになります。

これらのコマンドを実行することで、必要なパッケージの**最新バージョン**がインストールされます。

#### Pipfileから環境再現(指定されたパッケージを実際に使用していたバージョンでインストール)

まったく同じ開発環境を構築したいと思うと、それぞれのパッケージのバージョンまで合わせる必要があります。

その場合はPipfileではなく**Pipfile.lock**が使用されます。

Pipfile.lockは各パッケージのバージョン情報を保持しているので、こちらを使うとまったく同じ環境を再現することができます。

    pipenv sync

pipenv installと同じく、開発用パッケージもある場合は

    pipenv sync --dev

でまったく同じ環境が再現できます。

#### requirements.txtからインストール

pipを使い慣れている方ならお馴染みのrequirements.txtを使ってまとめてインストールすることも可能です。

まずはコマンド

    pipenv install -r requirements.txt


次にrequirements.txtの書き方ですが、こちらも従来と同じです。

```
numpy
matplotlib
pandas

```

## VSCodeでのkernel選択

ここから正攻法でのkernel設定方法を説明します。

手元のpath設定の状況によって、こちらでも簡単に設定できる方がいるかもしれません。

ただできない方も多いかと思うので、そのような方はこの章を読み飛ばして、次の[VSCodeでのkernel選択(裏技)](#vscodeでのkernel選択裏技)を参照ください。

### Pythonスクリプト

Pythonスクリプトを実行する場合は、画面右下から設定します。


画像の部分をクリックすると下のようなkernelのリストが追加されます。

右側にそれがpyenvなのか、pipenvなのか、など書いてくれていますので、pipenvのものを選びましょう。

この表で一番上に表示されているRecommendedについては、今回の記事の一番の工夫ポイントになりますので、後の章で説明します。

### Jupyter Notebook

Jupyter Notebookの場合は画面右上から設定します。

画像の部分をクリックすると下のようなkernelのリストが追加されますので、この中から探してください。

### もしkernelが見つからない場合

ここに指定したいkernelが出てくればいいですが、もし出てこない場合は、以下の手順でパスを通してください。

#### Pythonスクリプトの場合

こちらの表の一番上のEnter interpreter path -> Findから使いたい仮想(pipenv)環境のpython.exeを選択してください。

#### Jupyter Notebookの場合

一番下のSelect Another Kernel -> Python Environments

と進みます。

その後、Python環境のリストが出てきますが、この中でも出てこない場合は一番上のCreate Python Environmentから

Create Python Environment -> Venv -> Enter interpreter path

から、実行したい仮想環境のbinに入っているpython.exeを指定してください。

## VSCodeでのkernel選択(裏技)

裏技と書いていますが、実際は以下のことをしているだけです。

- pipenvの仮想環境をプロジェクトのローカルに保存するように変更 (./.venv に移動)
- パッケージをローカルの.venv内に保存
- VSCode setting.jsonで以下の二つを設定する
    - ローカルに仮想環境を保存するコマンドを自動実行
    - 使用するpython環境をローカルの仮想環境に設定

ただし、これだと新しく環境を構築する度に環境変数をいじいじする必要があるため、ヒューマンエラーが出てしまいます。

そこで、最初の環境変数をvscodeのsetting.jsonに埋め込むことで、ほぼ設定なしで環境を再構築できるようにします。

順番に説明していきます。

### pipenvの仮想環境保存場所をプロジェクトのローカルに変更

pipenvの仮想環境をローカルに保存するのは実は簡単です。

*PIPENV_VENV_IN_PROJECT* という環境変数があり、これを*true*にするだけで、ローカルに.venvディレクトリを作成し、そこに各種プロジェクトを保存するようになります。

(もちろんpython.exeなども保存されます)

今回はコマンドを叩くことはありませんが、

    export PIPENV_VENV_IN_PROJECT=true

を実行した後にpipenv installすればローカルに保存されるようになります。

### pipenvのプロジェクトをローカルに保存

こちらは上にも説明したinstallコマンドで実行可能です

    pipenv install

または

    pipenv install --dev

これでPipfileをもとにインストールしてくれます。

### VSCodeのsetting.jsonを設定する

次にVSCodeの設定を行います。

下で二つ設定をしますが、設定が完了したら念のためVSCodeを一度再起動してください。

#### VSCodeでPIPENV_VENV_IN_PROJECTを自動設定する

VSCodeユーザーの方であればご存じかと思いますが、VSCodeにはsettings.jsonというファイルが存在します。

このファイルは多種多様に渡るVSCodeの機能、その設定ファイルになります。

そしてこのファイルはdefault, user, そしてworkspaceで分けて設定することが可能です。

今回はworkspaceの中に生成されるsettings.jsonを使って、上のpipenv環境を自動で設定できるようにしていきます。

Ctrl + Shift + P のショートカットで機能一覧を開き、"settings json"で検索すると以下のように何項目かでてきます。

ここでWorkspace Settingsを開きます。

最初は恐らく下のような何も書かれていない状態だと思いますが、ここに以下の二つを追加します。

``` json : settings.json
{

}
```

``` json : settings.json
{
    "terminal.integrated.env.windows": {
        "PIPENV_VENV_IN_PROJECT": "true"
    },
    
    "terminal.integrated.env.linux": {
        "PIPENV_VENV_IN_PROJECT": "true"
    },
}
```

ここではWindows, Linux両方で設定しています。

上を追記したら保存してください。

#### Python Kernelの自動設定

Python Kernelも同じくsettings.jsonで設定します。

追加する内容はシンプルです。

ローカルで保存するようにしたpipenv仮想環境へのパスを記述するだけです。

なので以下のように三行追記します。

``` json : settings.json
{
    "terminal.integrated.env.windows": {
        "PATH": "${workspaceFolder}/.venv/bin;${env:PATH}",
        "PIPENV_VENV_IN_PROJECT": "true"
    },
    
    "terminal.integrated.env.linux": {
        "PATH": "${workspaceFolder}/.venv/bin;${env:PATH}",
        "PIPENV_VENV_IN_PROJECT": "true"
    },

    "python.defaultInterpreterPath": "${workspaceFolder}/.venv/bin/python.exe",
}
```

これで設定は完了です。

次に実際に使い方を説明します。

### サンプルレポジトリを例とした使い方

サンプルレポジトリをもとに説明していきます。

ちなみに**前半はgit, pipenvが使えるターミナルであればなんでも大丈夫です。**

もしVSCodeのターミナルで実行する場合は、後で一度VSCodeを開き直す必要がありますのでご注意ください。

#### リポジトリをクローン

まず[こちら](https://github.com/RYO0115/chatgpt_prompt_engineering.git)のレポジトリをクローンしてきます。

適当なディレクトリで

    git clone https://github.com/RYO0115/chatgpt_prompt_engineering.git

#### クローンしたディレクトリに仮想環境を保存

クローンできたら、chatgpt_prompt_engineeringのディレクトリへ移動

    pipenv install --dev

今回 ipykernelも入れるので--devコマンドをつけています。

#### (VSCodeで実行している場合)VSCode 再起動

ここで一度VSCodeを再起動します。

再起動と言っても、そのプロジェクトを開いているウィンドウを一度閉じて開きなおすだけです。

#### 再度プロジェクトをVSCodeで開き、Jupyter Notebookを開く

VSCodeでプロジェクトを開き、guidelines.ipynbを開いて見てください。

すると右上のkernelの部分がdetectingとなった後に.venvに設定されます。

これでpipenv環境との紐づけは完了です。

## 終わりに

というわけで、今回はWSL2 + Windows11 + pyenv + pipenv + VSCode環境構築とそれを簡単に管理する方法について紹介しました。

まだpipenvについては研究する余地があるかと思いますので、今後も更新していきたいと思います。

また、何かほかにこうした方がいいなどのアドバイス、コメントあればお願いします。

ではでは
