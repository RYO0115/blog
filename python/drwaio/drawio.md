# Draw.io を使ってみたよ

## Draw.ioとは？
[Draw.io](https://app.diagrams.net/)は高性能な作画ツールです。

G Suite Markerplaceの説明文をお借りすると以下のように説明しています。

>draw.io is completely free online diagram editor built around Google Drive(TM), that enables you to create flowcharts, UML, entity relation, network diagrams, mockups and more.

>draw.ioはGoogle Driveを利用した完全フリーのダイアグラムエディタです。
>このエディタでは、フローチャート、UML、ネットワーク図などなどを描くことができます。

つまり、フローチャートやクラス図など、プログラムを書き始める前やあるいはあとに、プログラム全体の構成を書くのに便利なツールなわけです。

では、なぜこれをここで紹介したのかというと、最近紹介しました、[Jupyter Notebook を使ってみたよ](https://ryo-udon.hatenadiary.jp/entry/2020/06/06/155906)
でJupyter Notebookにフロー図を差し込みたいと思ったからです。

最近、Deep Learningの勉強を今更ながらはじめたのですが、Notebookにレイヤやパーセプトロンなどの図を差し込まないとなかなか後で見て分かりづらかったので、
それに適したツールを探したところ見つかったのがこちらというわけです。




## どういうところで使えるの？


プログラムを書きなれた方であれば、このツールを起動すればこのツールの良さがすぐに分かるかとは思いますが、

そもそも、あまりプログラムを書かない方だと、なかなかこのツールの良さは伝わらないかとおもいます。

そこで、今回はあまりクラス図やフロー図など、事前知識が必要な用途については言及せず、あくまでJupyterに差し込む図を簡単に作れるよという紹介をしたいとおもいます。


今回は自分が学習用に分けていた[リポジトリ](https://github.com/RYO0115/python_dl.git)を参考に説明します。　

まずは下の画像を見てください。

![python_dl_sample](./image/python_dl_sample.png)

こちらに差し込んでいる見た目のいい図(自分でいうなよと思わないでください。。。)がすべてこのdraw.ioで作成したものになります。


また、こちらの図のように、図の中にTex形式で数式を記述することもできます。

![python_dl_sample2]()

このようにdraw.ioは、そこそこ見た目のいい図が簡単に作成でき、しかもjupyter notebookに簡単にコピーアンドペーストができる、

そういう超便利ツールなわけです。

## どうやって使うの？

使い方は至って簡単ですので順を追って説明していきます。

まずは[こちらのページ](https://app.diagrams.net/)に行きます。

すると、次のように新しくダイアグラム(図)を作るか、それとも既存のものを作るかを聞いてきます。

今回は新しく図を作っていきますので **"Create New Diagram"** を選択します。

するとどういう図を作りたいかというフォーマットの選択画面に移動します。

今回は特にフローチャートなどはつくらず、簡単なお絵かきだけしようと思うので、　**"Blank Diagram"**　を選択します。

-------

注意
ちなみに、おそらく起動した際にDesktop版のインストールを進められるかと思いますが、現状はインストールをおすすめしません。

理由は、上のdraw.ioの良さの一つに上げた、数式が書けないからです。

Q&Aを確認したところ、LaTex形式での数式記入はブラウザ版のみの対応となっており、デスクトップ版にはまだ追加されていないようです。

そのため、数式を書くたびにわざわざ別のウィンドウで開き直すという作業が必要になるデスクトップ版はあまりおすすめしません。

-------

### 図の描き方
図の描き方は至ってシンプルです。

左のサイドバーにある、豊富な図形の中から使いたいものをクリックするだけで、真ん中の編集エリアに図形が現れます。あとはそれをPowerPointの図形を編集するのと同じ用に、
上下左右などにある青い点をクリックし、ドラッグしてあげることでサイズを変更できるようになります。


もし図形同士を先で結びたいのなら、線を結びたい元の図形をクリックし、結びたい方向に表示された矢印をクリックしてあげれば、簡単に線をつないでくれます。

もちろん、クリックのあとにドラッグをしてあげれば任意のところに線を伸ばすことも可能です。


ここについては、自分で色々と弄っていくと操作方法もわかってくるでしょう。

### 数式の描き方

数式については、少し混乱するかもしれませんが、やり方を覚えればあとは、都度都度文法をGoogleで調べるだけになります。

例えばこちらの図の矢印の横に、

$$y = \frac{1}{x}$$

という数式を追加したいとします。

まず、メニューバーから"Extras"->"Mathmatical Setting" をクリックします。

もし、この設定がONになっていれば、再度"Extras"を開いたときに"Mathmatical Setting"の横にチェックがついているはずです。

その後、Latex形式で数式を書いていきます。

ちなみに余談ですが、draw.ioでは、3つの数式記述法に対応しています。[リンク](https://desk.draw.io/support/solutions/articles/16000032875)

* AsciiMath
* Latex $$
* Latex \

今回は、二番目の **"$$"**で前後を囲むLatexの記述法で数式を書いていきます。

まず、左にあるサイドバーから、Textという図をクリックし、編集エリアにテキストボックスを追加します。

その後、このテキストボックスに以下の文を追加します。

    $$ y = \frac{1}{x} $$

ここで、

**$$** がこの間に数式を書くよという宣言

**\frac** が分数を意味しています。

この記入が終わったら、あとはテキストボックス外の編集ページのどこかをクリックすると、勝手に数式に変換してくれるのです。


これを使うことで、自分のJupyter Notebookの完成度をより一層高めることができるようになります。

### 保存や出力について

保存は他のアプリケーションと同じく、

"File" -> "Save"　または　"Save as"

から保存ができます。

また画像やpdfで出力したいときには、

"File" -> "Export as"

から好きな形式で保存することができます。


### Jupyterへの添付

最後に、今回の目玉であるJupyter Notebook への挿入についてです。

今回作成した図を実際にJupyter Notebookへペーストする方法について順を追って説明します。

#### 図形の整理

まず、編集ページの中を貼り付けたい図形のみにします。

これは、このあとにコピーする際に、page単位でのコピーとなるためです。

なので、もし貼り付けたくない図形などがある場合は、新たにpageを作成して、そちらへ移動しましょう。

#### 埋め込み式(html形式)への変換

図形の整理が終わったら、下の図のように"File" -> "Embed" -> "Image"を選択していきます。

次に色々と設定項目があるウィンドウがでてきますが、影の追加などについてなので、特にイジる必要はありません。

そのまま、"Embed"をクリックします。

すると下の図のように、html形式に変換してくれます。

あとはこれを Ctrl + C　か、下のCopyをクリックしてコピーしたら、あとはJupyter Notebookに貼り付けるだけです。


#### Jupyter Notebookへの貼り付け

Jupyter Notebookへの貼り付けは何も難しいことはありません。

ただ、図を差し込むようの **Markdown**ブロックを作ってあげ、そこに先程コピーしたhtml形式のimageタグを貼り付けて上げれば、

あとはおなじみの Ctrl + Enter(Return)を押したらJupyter Notebookに図形が表示されます。

##　終わりに
というわけで、今回はdraw.ioという描画ツールについて紹介しました。

draw.ioは、これまでパソコンでノートを作成する際に障害になっていた"手書きの図を作るのに時間がかかる”という問題を解決してくれる超便利ツールです。

今回はJupyter に差し込むにはという部分しか説明していませんが、プログラムを書く際の簡単な構成図を書くのにもかなり使えます。

ぜひ、色々と活用してみてください。

