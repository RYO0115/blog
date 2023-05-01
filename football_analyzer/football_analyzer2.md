
# Football Analyzer開発日記_その2

- [Football Analyzer開発日記_その2](#football-analyzer%E9%96%8B%E7%99%BA%E6%97%A5%E8%A8%98%E3%81%9D%E3%81%AE2)
- [概要](#%E6%A6%82%E8%A6%81)
- [プレイヤーを設置してみよう](#%E3%83%97%E3%83%AC%E3%82%A4%E3%83%A4%E3%83%BC%E3%82%92%E8%A8%AD%E7%BD%AE%E3%81%97%E3%81%A6%E3%81%BF%E3%82%88%E3%81%86)
  - [まずは実行してみよう](#%E3%81%BE%E3%81%9A%E3%81%AF%E5%AE%9F%E8%A1%8C%E3%81%97%E3%81%A6%E3%81%BF%E3%82%88%E3%81%86)
  - [コードの中身を解説](#%E3%82%B3%E3%83%BC%E3%83%89%E3%81%AE%E4%B8%AD%E8%BA%AB%E3%82%92%E8%A7%A3%E8%AA%AC)
- [色々なフォーメーションで試してみよう](#%E8%89%B2%E3%81%AA%E3%83%95%E3%82%A9%E3%83%BC%E3%83%A1%E3%83%BC%E3%82%B7%E3%83%A7%E3%83%B3%E3%81%A7%E8%A9%A6%E3%81%97%E3%81%A6%E3%81%BF%E3%82%88%E3%81%86)
- [終わりに](#%E7%B5%82%E3%82%8F%E3%82%8A%E3%81%AB)
# 概要
自分でサッカーを分析できるツールを作ろうという見切り発車な企画第二弾。

今回は第一弾にてmatplotlibを使ってフットボールコートを描く方法を説明した続き、そのコートに指定したフォーメーションでプレイヤーを描く部分の紹介になります。

# プレイヤーを設置してみよう
## まずは実行してみよう
まずは実行してみます。
今回は前回紹介した[drawFootballCourt.py](), [main.py]()に加え、
以下の[setPlayer.py]()を使用します</br>

``` python
import sys,os
import numpy as np
from drawFootballCourt import *


COURT_SIZE = [72.5, 45]
#POSITION_LIST = "positionName.json"

class PLAYER():
    def __init__(self):
        self.name = ""
        self.uniNum = 0
        self.playerPos = ""
        self.playerID = 0
        self.pos = [0,0]

class TEAM():
    def __init__(self, homeAway):
        self.homeAway = homeAway
        self.member = [PLAYER() for i in range(11)]
        dir = os.path.abspath(__file__)
        #self.positionList = json.loads(dir[:-len("setPlayer.py")] + "positionName.json")
        self.SetPlayerPosition("4231")
        self.teamColor = ""

    def SetPlayerPosition(self, formation):
        lineMemberNum = []
        lineMemberNum.append(1)
        for i in range(len(formation)):
            lineMemberNum.append(int(formation[i]))

        #コートの端からペナルティスポットまでを引いたもの
        verticalLength  = COURT_SIZE[0] - 6.0
        verticalRes     = (verticalLength-5) / (len(lineMemberNum)-1)

        horizontalLength = COURT_SIZE[1] * 2

        i = 0
        memberIDNum = 0
        for memberNum in lineMemberNum:
            horizontalRes = horizontalLength / (memberNum+1)
            x = verticalLength - verticalRes * i
            for j in range(memberNum):
                y = COURT_SIZE[1] - horizontalRes * (j+1)
                self.member[memberIDNum].pos = [x,y]
                memberIDNum += 1
            i += 1


class PLAYER_SERVER():
    def __init__(self, fig, ax):
        self.fig = fig
        self.ax = ax
        self.dc = DRAW_COURT(fig, ax)
        self.dc.DrawCourt()

    def SetFullMember(self):
        self.teams = []
        self.teams.append(TEAM("home"))
        self.teams.append(TEAM("away"))

        self.teams[0].teamColor="red"
        self.teams[1].teamColor="blue"

    def DrawPlayers(self):
        for team in self.teams:
            for member in team.member:
                x = member.pos[0]
                y = member.pos[1]
                if(team.homeAway=="home"):
                    x *= -1
                    y *= -1

                self.dc.DrawPlayerCircle(x,y,team.teamColor)

    def Show(self):
        self.dc.Show()
```

実行する[main.py]()は以下のように変更します。
``` python

import matplotlib.pyplot as plt
from setPlayer import *

home = 0
away = 1

# for drawing court
#if __name__ == '__main__':
#    fig, ax = plt.subplots()
#    dc = DRAW_COURT(fig, ax)
#    dc.DrawCourt()
#    dc.Show()

# for drawing player formation
if __name__ == '__main__':
    fig, ax = plt.subplots()
    ps = PLAYER_SERVER(fig, ax)
    ps.SetFullMember()

    ps.teams[home].SetPlayerPosition("4231")
    ps.teams[away].SetPlayerPosition("343")
    ps.DrawPlayers()
    ps.Show()

```
実際に実行してみると以下のような結果が得られます。


## コードの中身を解説
全体の構成としては、第一弾で説明したフットボールを描く**DRAW_COURT**クラスを持つ**PLAYER_SERVER**がホーム、アウェイのチーム全体のプレイヤーの位置を管理しています。
``` python
class PLAYER_SERVER():
    def __init__(self, fig, ax):
        self.fig = fig
        self.ax = ax
        self.dc = DRAW_COURT(fig, ax)
        self.dc.DrawCourt()

    def SetFullMember(self):
        self.teams = []
        self.teams.append(TEAM("home"))
        self.teams.append(TEAM("away"))

        self.teams[0].teamColor="red"
        self.teams[1].teamColor="blue"

    def DrawPlayers(self):
        for team in self.teams:
            for member in team.member:
                x = member.pos[0]
                y = member.pos[1]
                if(team.homeAway=="home"):
                    x *= -1
                    y *= -1

                self.dc.DrawPlayerCircle(x,y,team.teamColor)

    def Show(self):
        self.dc.Show()

```
チームは別途**TEAM**というクラスを作成していて、11人のプレイヤーの格納と一括でポジションを設定する**SetPlayerPosition()**を持っています。</br>
この関数はフィールドのサイズを入力されたフォーメーションの数字列に合わせてプレイヤーを配置します。</br>
例えば4-2-3-1の場合はキーパーも加えた5列でペナルティエリアからハーフウェイラインまでの距離を分割し、</br>
横方向はフィールドの横幅を各ラインの人数で分割して設置するようになっています。</br>
これによって、3-4-3や4-4-2などのキーパーを入れて4列のフォーメーションでも、
一時期話題になった1-1-7(3-4)-1などのネタフォーメーションも表現できるようになっています。

``` python
class TEAM():
    def __init__(self, homeAway):
        self.homeAway = homeAway
        self.member = [PLAYER() for i in range(11)]
        dir = os.path.abspath(__file__)
        self.SetPlayerPosition("4231")
        self.teamColor = ""

    def SetPlayerPosition(self, formation):
        lineMemberNum = []
        lineMemberNum.append(1)
        for i in range(len(formation)):
            lineMemberNum.append(int(formation[i]))

        #コートの端からペナルティスポットまでを引いたもの
        verticalLength  = COURT_SIZE[0] - 6.0
        verticalRes     = (verticalLength-5) / (len(lineMemberNum)-1)

        horizontalLength = COURT_SIZE[1] * 2

        i = 0
        memberIDNum = 0
        for memberNum in lineMemberNum:
            horizontalRes = horizontalLength / (memberNum+1)
            x = verticalLength - verticalRes * i
            for j in range(memberNum):
                y = COURT_SIZE[1] - horizontalRes * (j+1)
                self.member[memberIDNum].pos = [x,y]
                memberIDNum += 1
            i += 1

```
プレイヤーは**PLAYER**というクラスを別途作って管理しています。</br>
(現在はposしか使っていませんが、追々、プレイするポジションや選手名、背番号なども表示できるようにしたいと考えています。
後、できれば採点などをした際にはその選手のレーティングとMoMには星マークを描く、などの機能を実装していく予定です。)
``` python
class PLAYER():
    def __init__(self):
        self.name = ""
        self.uniNum = 0
        self.playerPos = ""
        self.playerID = 0
        self.pos = [0,0]
```
最後にコート上にプレイヤーを描く関数を紹介します。
まず、丸そのものを描く関数は[drawFootballCourt.py]()にあります。
``` python
    def DrawPlayerCircle(self, x, y, color):
        circle = plt.Circle( xy=(x,y), radius=2, ec="w", fc=color, fill=True, zorder=2)
        self.ax.add_patch(circle)
```
中身はただ、matplotlibの円を描く関数を呼び出しているだけです。</br>

今回、プレイヤーを描くために工夫したのは次のPLAYER_SERVE内の
**DrawPlayers()** 関数です。
``` python
    def DrawPlayers(self):
        for team in self.teams:
            for member in team.member:
                x = member.pos[0]
                y = member.pos[1]
                if(team.homeAway=="home"):
                    x *= -1
                    y *= -1

                self.dc.DrawPlayerCircle(x,y,team.teamColor)

```
ここで、チームがhomeかawayかで正負を反転させることで、
プレイヤーの座標を同じ座標系で扱えるようにしました。

このようにした理由は、home, awayで同じサイドを扱おうとしたとき、それぞれでグラフでいうy座標系の条件が反転してしまうのが嫌だったからです。(これは追々、変えるかもしれません)

では最後に、以上のことを考えながら作ってみたものを色々なフォーメーションで試してみたいと思います。

# 色々なフォーメーションで試してみよう
4-2-3-1 vs 3-4-3

4-3-3 vs 5-3-2

4-4-2 vs 3-5-2

ちょっと5の部分をもっとジグザグにしたいなー


# 終わりに
今回は、まずいプログラムでフィールドとフォーメーションを描いてみました。
次はこの選手をマウスで移動させたり、一人が動くと他の11人も合わせて移動するような機能を作りたいと思います。

(後、もう少しフォーメーションにジグザグ感をプラスしたい)

以上!!

何かアドバイスやコメントなどもお待ちしています。