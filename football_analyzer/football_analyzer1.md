
# Football Analyzer開発日記_その1

- [Football Analyzer開発日記_その1](#football-analyzer%E9%96%8B%E7%99%BA%E6%97%A5%E8%A8%98%E3%81%9D%E3%81%AE1)
- [概要](#%E6%A6%82%E8%A6%81)
- [matplotlibとは](#matplotlib%E3%81%A8%E3%81%AF)
  - [インストール方法](#%E3%82%A4%E3%83%B3%E3%82%B9%E3%83%88%E3%83%BC%E3%83%AB%E6%96%B9%E6%B3%95)
- [matplotlibを使ってフットボールコートを描いてみる](#matplotlib%E3%82%92%E4%BD%BF%E3%81%A3%E3%81%A6%E3%83%95%E3%83%83%E3%83%88%E3%83%9C%E3%83%BC%E3%83%AB%E3%82%B3%E3%83%BC%E3%83%88%E3%82%92%E6%8F%8F%E3%81%84%E3%81%A6%E3%81%BF%E3%82%8B)
  - [コードを実行](#%E3%82%B3%E3%83%BC%E3%83%89%E3%82%92%E5%AE%9F%E8%A1%8C)
  - [コードの中身を解説](#%E3%82%B3%E3%83%BC%E3%83%89%E3%81%AE%E4%B8%AD%E8%BA%AB%E3%82%92%E8%A7%A3%E8%AA%AC)
    - [描画領域の宣言](#%E6%8F%8F%E7%94%BB%E9%A0%98%E5%9F%9F%E3%81%AE%E5%AE%A3%E8%A8%80)
    - [描画時の初期設定](#%E6%8F%8F%E7%94%BB%E6%99%82%E3%81%AE%E5%88%9D%E6%9C%9F%E8%A8%AD%E5%AE%9A)
    - [センターサークルを描く](#%E3%82%BB%E3%83%B3%E3%82%BF%E3%83%BC%E3%82%B5%E3%83%BC%E3%82%AF%E3%83%AB%E3%82%92%E6%8F%8F%E3%81%8F)
    - [ラインを引いてみる](#%E3%83%A9%E3%82%A4%E3%83%B3%E3%82%92%E5%BC%95%E3%81%84%E3%81%A6%E3%81%BF%E3%82%8B)
  - [ここまでを実行してみる](#%E3%81%93%E3%81%93%E3%81%BE%E3%81%A7%E3%82%92%E5%AE%9F%E8%A1%8C%E3%81%97%E3%81%A6%E3%81%BF%E3%82%8B)
- [コートにプレイヤーを配置してみる](#%E3%82%B3%E3%83%BC%E3%83%88%E3%81%AB%E3%83%97%E3%83%AC%E3%82%A4%E3%83%A4%E3%83%BC%E3%82%92%E9%85%8D%E7%BD%AE%E3%81%97%E3%81%A6%E3%81%BF%E3%82%8B)
# 概要
自分でサッカーを分析できるツールを作ろうという見切り発車な企画第一弾。
まずはpythonの中でも大人気なグラフ作成ツール、matplotlibを使って、
簡単にサッカーのコートとその中にホーム、アウェイ両チームのポジションを描けるプログラムを
作ってみましたので、紹介したいと思います。

# matplotlibとは
[matplotlib](https://matplotlib.org/gallery.html)とはpythonで
グラフを描画して画像を作成したり、簡単なアニメーションを作成することが可能なライブラリであり、
pythonが人気言語に数えられる大きな要因の一つとなっています。
基本的なライブラリの使い方は上のリンク先に書いてあるのですが、いざ自分で使おうと思うと
いろいろとつまずくことがあるので、その点を踏まえて説明していきたいと思います。

## インストール方法
ここではすでにpythonがインストールされていることを前提にお話します。
お使いのパソコンでターミナル(windowsならコマンドプロンプト)を開いていただき、

    pip install matplotlib

と入力して貰えればインストールは完了です。
基本的にpythonをインストールした際に自動的にpipもインストールされるはずですが、
もし、pipがないと言われた場合は[こちら](https://pip.pypa.io/en/stable/installing/)を参考に
インストールしてください。

# matplotlibを使ってフットボールコートを描いてみる
## コードを実行
[**drawFootballCourt.py**](https://github.com/RYO0115/Football_Analyzer/blob/master/src/drawFootballCourt.py)
を参照

``` python : drawFootballCourt.py
import matplotlib.pyplot as plt
import numpy as np

COURT_SIZE = [72.5, 45]

class DRAW_COURT():
    def __init__(self, fig, ax):
        self.fig = fig
        self.ax = ax

    def DrawCourt(self):
        self.ax.set_xlim( -COURT_SIZE[0], COURT_SIZE[0])
        self.ax.set_ylim( -COURT_SIZE[1], COURT_SIZE[1])
        self.ax.set_aspect("equal")
        self.fig.set_facecolor("white")
        self.ax.set_facecolor("green")
        self.ax.set_alpha(0.7)
        plt.tick_params(labelbottom=False,
                        labelleft=False,
                        labelright=False,
                        labeltop=False)
        self.DrawCourtCircle()
        self.DrawCourtLine()

    def DrawCourtCircle(self):
        #センターサークル
        circle = plt.Circle( xy=(0,0), radius=9.150, ec='w', fc='w', fill=False, zorder=1)
        self.ax.add_patch(circle)

        #ペナルティスポット
        circle = plt.Circle( xy=( 61.5, 0), radius=0.5, ec="k", fc="k", fill=True, zorder=1)
        self.ax.add_patch(circle)
        circle = plt.Circle( xy=(-61.5, 0), radius=0.5, ec="k", fc="k", fill=True, zorder=1)
        self.ax.add_patch(circle)

    def DrawPlayerCircle(self, x, y, color):
        circle = plt.Circle( xy=(x,y), radius=2, ec="w", fc=color, fill=True, zorder=2)
        self.ax.add_patch(circle)


    def DrawCourtLine(self):
        #センターライン
        self.DrawLine( 0, 0,-COURT_SIZE[1], COURT_SIZE[1], zo=1)

        #ゴールエリア
        self.DrawLine( 67.0, 67.0,-9.16, 9.16, zo=1)
        self.DrawLine(-67.0,-67.0,-9.16, 9.16, zo=1)
        self.DrawLine( 67.0, COURT_SIZE[0], 9.16, 9.16, zo=1)
        self.DrawLine( 67.0, COURT_SIZE[0],-9.16,-9.16, zo=1)
        self.DrawLine(-67.0,-COURT_SIZE[0], 9.16, 9.16, zo=1)
        self.DrawLine(-67.0,-COURT_SIZE[0],-9.16,-9.16, zo=1)

        #ペナルティエリア
        self.DrawLine( 56.0, 56.0,-20.16, 20.16, zo=1)
        self.DrawLine(-56.0,-56.0,-20.16, 20.16, zo=1)
        self.DrawLine( 56.0, COURT_SIZE[0], 20.16, 20.16, zo=1)
        self.DrawLine( 56.0, COURT_SIZE[0],-20.16,-20.16, zo=1)
        self.DrawLine(-56.0,-COURT_SIZE[0], 20.16, 20.16, zo=1)
        self.DrawLine(-56.0,-COURT_SIZE[0],-20.16,-20.16, zo=1)


    def DrawLine(self, x1, x2, y1, y2, color="w-", zo=3):
        line = plt.plot([x1,x2],[y1,y2],color,lw=2, zorder=zo)


    def Show(self):
        plt.show()
```

実行部分が
[**main.py**](https://github.com/RYO0115/Football_Analyzer/blob/master/src/main.py)
を以下の状態にしてもらうとこちらのような結果が得られます。
``` python :main.py

import matplotlib.pyplot as plt
from setPlayer import *

# for drawing court
if __name__ == '__main__':
    fig, ax = plt.subplots()
    dc = DRAW_COURT(fig, ax)
    dc.DrawCourt()
    dc.Show()

# for drawing player formation
#if __name__ == '__main__':
#    fig, ax = plt.subplots()
#    ps = PLAYER_SERVER(fig, ax)
#    ps.SetFullMember()
#    ps.teams[0].SetPlayerPosition("4231")
#    ps.teams[1].SetPlayerPosition("343")
#    ps.DrawPlayers()
#    ps.Show()
```

## コードの中身を解説
### 描画領域の宣言
まず、matplotlibは以下のような仕組みで描画を行っています。
詳細は[こちら](https://matplotlib.org/tutorials/introductory/usage.html#sphx-glr-tutorials-introductory-usage-py
)のページに載っていますが、ここでは簡単に説明します。

まず、[**main.py**](https://github.com/RYO0115/Football_Analyzer/blob/master/src/main.py)の中から説明します。
``` python :main.py

import matplotlib.pyplot as plt
# for drawing court
if __name__ == '__main__':
    fig, ax = plt.subplots()
```

まず一行目の
``` python
import matplotlib.pyplot as plt
```
このpythonプログラムにmatplotlibの点や線などをプロットする
ライブラリをpltという名前で呼び出しています。
この

    import *** as ~~~

という呼び出し方はpythonでは良く使われるので覚えているといいと思います。
次に、
``` python
# for drawing court
if __name__ == '__main__':
    fig, ax = plt.subplots()
```
この部分で今回、グラフを描画するウィンドウ(Figure)とそのウィンドウの中でグラフを描くエリアを示す(Axes)を宣言しています。
ここではグラフを一つしか描かないため、引数は何も指定していませんが、
もし1つのウィンドウに例えば縦2列、横2列でグラフを描きたい場合は、

    fig, ax_lst = plt.subplots(2, 2)

と、宣言するとax_lstの中にaxesがリストで格納されます。

### 描画時の初期設定
描画の初期設定は
[**drawFootballCourt.py**](https://github.com/RYO0115/Football_Analyzer/blob/master/src/drawFootballCourt.py)
の中では

``` python
    def DrawCourt(self):
        self.ax.set_xlim( -COURT_SIZE[0], COURT_SIZE[0])
        self.ax.set_ylim( -COURT_SIZE[1], COURT_SIZE[1])
        self.ax.set_aspect("equal")
        self.fig.set_facecolor("white")
        self.ax.set_facecolor("green")
        self.ax.set_alpha(0.7)
        plt.tick_params(labelbottom=False,
                        labelleft=False,
                        labelright=False,
                        labeltop=False)
```

このDrawCourt()関数の中でいろいろと宣言しています。
まず、
``` python
self.ax.set_xlim( -COURT_SIZE[0], COURT_SIZE[0])
self.ax.set_ylim( -COURT_SIZE[1], COURT_SIZE[1])
```
では、グラフのx軸(横軸)、y軸(縦軸)の表示範囲を指定しています。
今回はグラフ全体を使ってフットボールコートを描きたいと思っているので、
 **COURT_SIZE** の中に縦、横のコートの半分のサイズを配列として格納しています。

次にこちらの4行についてです。
``` python
self.ax.set_aspect("equal")
self.fig.set_facecolor("white")
self.ax.set_facecolor("green")
self.ax.set_alpha(0.7)
```
**set_aspect("equal")** はx,y軸の分解能を実際のピクセルで同じ幅にするという宣言です。
matplotlibの場合、これを宣言しないとx,yそれぞれで分解能が違うグラフが描かれるようになっています。
次に、**ax.set_facecolor()**, **fig.set_facecolor()** についてです。
これについては字の通り、figure, axesそれぞれの色を設定しています。
今回はfigureを白、axesを緑に設定しています。
**set_alpha()**は透明度を設定する部分になるので、これはあくまで好みになります。

最後に、軸に目盛りをつけない設定を以下の通りにしています。
``` python
plt.tick_params(labelbottom=False,
                labelleft=False,
                labelright=False,
                labeltop=False)
```
これも字の通り、グラフの上下左右の軸には何も表示してほしくないので**False** を設定しています。

描画時の初期設定は以上になります。


### センターサークルを描く
センターサークルとペナルティスポットを描くのが以下の関数になります。
``` python
    def DrawCourtCircle(self):
        #センターサークル
        circle = plt.Circle( xy=(0,0), radius=9.150, ec='w', fc='w', fill=False, zorder=1)
        self.ax.add_patch(circle)

        #ペナルティスポット
        circle = plt.Circle( xy=( 61.5, 0), radius=0.5, ec="k", fc="k", fill=True, zorder=1)
        self.ax.add_patch(circle)

        circle = plt.Circle( xy=(-61.5, 0), radius=0.5, ec="k", fc="k", fill=True, zorder=1)
        self.ax.add_patch(circle)
```
大まかに流れを説明するとmatplotlibのCircleを描く関数を呼び出し、
各種パラメータを設定した後、返り値として返ってきた構造体を
axesに加えています。
matplotlibで複数の図形を描く際、**add_patch()**　にて追加された図形の構造体が自動的に描画される仕様になっています。

今回はセンターサークルなどの円に加え、プレイヤーを示す円形もこの関数を利用しています。
**plt.Circle()** 内のパラメータを簡単に説明すると、

|     |      |
|----:|:-----|
|ec|外枠の色|
|fc|塗りつぶし部分の色|
|fill|塗りつぶすかどうか|
|zorder|描く優先順位|

ここで一番重要なのが**zorder**です。

**zorder**は描く優先順位を示しており、数字が大きいほど優先順位が高くなります。
なので、センターサークルやこの後に説明するラインはなるべく低い数字を設定しています。
これをしなければ、ラインと重なったプレイヤーの丸がラインの下に潜りこむというなんとも微妙な見栄えになってしまいます。
これは単純にグラフを描く際にも使うことが多いので覚えておいても損はないかと思います。

次にラインを引いて行きます
### ラインを引いてみる
``` python
    def DrawCourtLine(self):
        #センターライン
        self.DrawLine( 0, 0,-COURT_SIZE[1], COURT_SIZE[1], zo=1)

        #ゴールエリア
        self.DrawLine( 67.0, 67.0,-9.16, 9.16, zo=1)
        self.DrawLine(-67.0,-67.0,-9.16, 9.16, zo=1)
        self.DrawLine( 67.0, COURT_SIZE[0], 9.16, 9.16, zo=1)
        self.DrawLine( 67.0, COURT_SIZE[0],-9.16,-9.16, zo=1)
        self.DrawLine(-67.0,-COURT_SIZE[0], 9.16, 9.16, zo=1)
        self.DrawLine(-67.0,-COURT_SIZE[0],-9.16,-9.16, zo=1)

        #ペナルティエリア
        self.DrawLine( 56.0, 56.0,-20.16, 20.16, zo=1)
        self.DrawLine(-56.0,-56.0,-20.16, 20.16, zo=1)
        self.DrawLine( 56.0, COURT_SIZE[0], 20.16, 20.16, zo=1)
        self.DrawLine( 56.0, COURT_SIZE[0],-20.16,-20.16, zo=1)
        self.DrawLine(-56.0,-COURT_SIZE[0], 20.16, 20.16, zo=1)
        self.DrawLine(-56.0,-COURT_SIZE[0],-20.16,-20.16, zo=1)
```

これまでいろいろと説明してきましたが、ここが一番簡単な箇所になります。
というのも、この関数のなかではネットで調べた寸法をもとに線を引く関数を呼び出しているだけです。
今回はlinewidth(線の幅)を固定にしたかったので、別途DrawLineという関数を設けています。

## ここまでを実行してみる
ここまで説明したソースコードを実行してみると以下の実行結果が得られます。


# コートにプレイヤーを配置してみる
少し長くなってしまったので、別の記事として後日投稿します。

