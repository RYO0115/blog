# Jupyter を使ってみたよ
## Jupyter Notebookって何？

プログラムと聞くと、何行、何十行とコードを書いて、初めて動作確認をするイメージを持つ方もいるかと思います。

特に、CやC++など作ったプログラムを実行する前にコンパイルという作業が必要な言語に慣れ親しんでいる人ほど、そういうイメージを持っているのではないでしょうか

(Jupyterを知る前のわたしも同じでした。)

だけど、ご存知の通り、一気に複数行に渡るコードを追加しても想定どおりに動いてくれないことが多々あります。

逆に一発で想定通りに動いてくれたときもとてつもない不安に襲われていると思います。

[Jupyter](https://jupyter.org/)はそういう手間を省いてくれるツールになります。

Jupyterはブラウザ上で様々な言語を対話形式で動かすことができる開発環境です。

本家Wikiの言葉を借りると

>Project Jupyter is a set of open source software projects that form the building blocks for interactive and exploratory computing that is reproducible and multi-language.
>The main application offered by Jupyter is the Jupyter Notebook, a web-based interactive computing platform that allows users to author documents that combine live code,
>equations, narrative text, interactive dashboard and other rich media. ...

>Jupyterは多数に渡る言語をブロックで組み合わせることで対話的にそして探索的に計算処理をすることができるOpen Sourceソフトウェアプロジェクトの集合体です。
>Jupyterの主な機能はJupyter Notebookと呼ばれるブラウザ上で対話的に計算処理を行うことができる環境で、ユーザにその場で実行可能なコード、数式、処理の流れなどの情報を含んだドキュメントを作成することができます。

ここでいう対話的というのは、一行実行したら、すぐに結果が出力されることをいいます。

つまり、Jupyterを使うことで得られる大きなメリットは,

* 位置行ごとに実行確認をすることができる

* 自分が書いたコードの意図を数式や図を使って簡単にまとめることができる

ことにあります。

もちろんその他にもいろいろなOpen Sourceを使うことなどのメリットもあります。