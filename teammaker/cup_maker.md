# TeamMakerボット作成日記　第二弾
- [TeamMakerボット作成日記　第二弾](#teammaker%e3%83%9c%e3%83%83%e3%83%88%e4%bd%9c%e6%88%90%e6%97%a5%e8%a8%98-%e7%ac%ac%e4%ba%8c%e5%bc%be)
- [概要](#%e6%a6%82%e8%a6%81)
  - [追加機能の概要](#%e8%bf%bd%e5%8a%a0%e6%a9%9f%e8%83%bd%e3%81%ae%e6%a6%82%e8%a6%81)
    - [!cup](#cup)
    - [!cupw](#cupw)
  - [今回追加したものの構成について](#%e4%bb%8a%e5%9b%9e%e8%bf%bd%e5%8a%a0%e3%81%97%e3%81%9f%e3%82%82%e3%81%ae%e3%81%ae%e6%a7%8b%e6%88%90%e3%81%ab%e3%81%a4%e3%81%84%e3%81%a6)
  - [CUP_MAKER](#cupmaker)
    - [**ROUND()**:](#round)
    - [**TOURNAMENT_MAKER()**](#tournamentmaker)
    - [DRAW_SHAPE()](#drawshape)
    - [**CUP_MAKER()**](#cupmaker)
    - [画像の送信](#%e7%94%bb%e5%83%8f%e3%81%ae%e9%80%81%e4%bf%a1)
  - [まとめ](#%e3%81%be%e3%81%a8%e3%82%81)
# 概要
最近は便利になりました。
私の子供のでは、チーム分けをするにもじゃんけんで延々とアイコを繰り返したり、時間をかけてくじを作ったりととりあえず時間がかかりました。
それこそ人数が10人を超えてくるとそれはもう大変です。じゃんけんなんか早々決まるわけがないので(私はグッパージャス派でした)、結局チームわけで時間がかかって肝心の遊びの時間がなくなってしまう。そんな悲しい経験を何度もしました。

それに比べて今はどうでしょう?
１人一台スマホやパソコンを持つ時代になり、スマホやパソコンにはチーム分けのアプリがあり、劇的にその時間は短縮されました。

だけど、ちょっとまってください。
皆さんは本当にそれで満足していますか？
チームを作るたびにアプリにプレイヤーの名前を打ち込んで、あるいは参加する人の名前を膨大なリストから選んで。
(リストの人数が少ないって? それならそのままでいいと思いますよ。にっこり)
意外と時間かかっていないですか？

特に大人数で協力プレイをするゲームをやる方ならなおさらこれを感じているのではないでしょうか?
かくいう私もその１人です。
そんな私が作ったのがこちら。

自動チーム分けBot その名も**TeamMaker**!!

今回はそんなTeamMakerに新機能を追加したので第二弾としてこのサイトでも公開したいと思います。

## 追加機能の概要
今回追加した機能は、**トーナメント表の自動作成機能**になります。
メンバーを登録すると、その人数に合わせいい感じにシードなども作ってくれるトーナメント表作成機能です。

今回、この機能を実行するに当たり、新たなライブラリが必要となりますので、そのインストールを行います。

    sudo pip install pillow

これは作成したトーナメント表を画像として作成するために使用しています。
使い方自体は簡単。以下の２つのコマンドを打つだけ。

* **!cup**
* **!cupw**

それぞれのコマンドが何をするのかを説明します。

### !cup
友人たちと熱いトーナメント戦をしたい!! だけどいちいちトーナメント表を作るのは面倒くさい!
そんなあなたに魔法のコマンド!
以下の画像のように **!cup**と打ってみてください。
するとGeneralに参加したメンバを参加者としたトーナメント表を自動で作成して貼り付けてくれます。
![cup](image/cup.png)

### !cupw
ちなみにこのトーナメント表、勝ち負けの判定もできます。
コマンドで **!cupw [トーナメント番号] [ラウンド番号] [買った人のID] [4-2などのスコア]**を打ってもらうと、
下のようになります。
![cupw](image/cupw.png)
これで面倒な記録も必要なし! いつでもどこでも手軽にトーナメント戦ができるようになります!


## 今回追加したものの構成について
今回は以前作成したTeamMakerに付け加える形で作成しましたが、TeamMaker bot専用の機能にする気はなく、
他のものにも使い回せるようにというのを意識して作成しました。
そこで作成したのが以下になります。

-----------
ここにはいずれクラス図を勉強して追加します。
(正しい表記の仕方がわからない)

-----------

主に追加したものは
* トーナメント表を作る
* トーナメント表を描く
* 何個のトーナメント表を作るかを判断する
* トーナメントを管理する
の4つです。
これらの機能を合計で4つのクラスで表現しています(つもりです)
また、これに合わせてTeamMakerクラスのスリム化を行いました。

構成としてはBotの根幹をなす**BOT_BASE**クラスの中にチーム分けをする**TeamMaker**クラスとトーナメント表の作成などを行う今回新たに作成した**CUP_MAKER**クラスが内包されているという構成になっています。

BOT_BASEクラスはただそれぞれのクラスを管理しているだけですので、このページではCUP_MAKERのみ説明していきます。

## CUP_MAKER
CUP_MAKERクラスを構成する上で重要なクラスが２つあります。

### **ROUND()**:
このクラスがトーナメント作成での参加者及び、参加者が誰と対戦するのかを管理するこのCUP_MAKERに格納される最小のクラスになります。
トーナメント表での一番左端の参加者名しか書かれていないところも参加者が１人しかいないラウンドとして管理していきます。
このクラスはGraph構造をイメージしながら、色々と手抜きをしたものになります。


``` python @ TournamentMaker.py

# @brief トーナメント内の各ラウンドの構造体。全てプレイヤーはラウンドの中に格納されて管理される
class ROUND():
    def __init__(self,stageNum, id, members):
        self.roundID = id
        self.stageNum = stageNum
        self.parentID = 0
        self.childID = []

        # それぞれのラウンドの座標を保存
        # ここではしたの*の座標を格納
        #
        # ---|
        #    *---|
        # ---|   |
        #
        self.x = 0
        self.y = 0

        self.hasChildFlag = 0
        self.PositionSetFlag = 0
        self.SetMember(members)
        self.winner = -1
        self.player_id = -1
        self.score = ""


    def SetMember(self, members):
        self.memberList = members
        if len(self.memberList) == 1:
            self.hasChildFlag = 1

    def CreateChildRound(self,roundID):
        if self.hasChildFlag == 0:
            self.hasChildFlag = 1
            halfLine = int(len(self.memberList) / 2)
            child1List = self.memberList[:halfLine]
            child2List = self.memberList[halfLine:]
            newStage = self.stageNum + 1
            child1Round = ROUND(newStage, roundID+1,child1List)
            child2Round = ROUND(newStage, roundID+2,child2List)
            self.childID = [roundID+1, roundID+2]
            child1Round.SetParentID(self.parentID)
            child2Round.SetParentID(self.parentID)
            return [child1Round, child2Round], roundID+2
        return [0], roundID

    def SetParentID(self, parentID):
        self.parentID = parentID

    def SetPosition(self, x, y):
        self.x = x
        self.y = y
        self.PositionSetFlag = 1

    def SetWinnerID(self, id, score):
        self.winner = id
        self.score = score

    def SetPlayerID(self, id):
        self.player_id = id

    def GetWinnerID(self):
        return(self.winner)

```

各ラウンドには区別するためのIDが割り振られており、ラウンド同士の繋がりを保持するために、親と子のIDをもたせています。

    aaaa ---┐ ①
            ├---┐
    bbbb ---┘   |②
                ├---
                |

この図を例に説明すると、**aaaa**, **bbbb**, **①**, **②**はどれも同じROUNDクラスで表現されています。

①の親は②であり、子はaaaa,bbbbの２つになります。

なので、各ラウンドのメンバの数を確認することでそれが参加者を示すラウンドかどうかを確認することができます。

基本的に勝敗、スコア、参加者名などの情報は全てこのクラスの中に格納されます。

本当はprivate変数などにして外部から直接編集できないように管理すべきところではありますが、ここではかなり手抜きをしています。

### **TOURNAMENT_MAKER()**
次にこのクラスで先程のROUNDを取りまとめてトーナメントの形に整えて上げるクラスになります。

``` python @TournamentMaker.py
# @brief トーナメント作成用の構造体.
# ここではあくまで登録されたメンバー全員を入れたトーナメント表を作成する機能と勝ち負けの登録のみにしている。
# なので、人数が多い場合や、トーナメントを分けてあげたい場合は複数個のクラスを呼んで上げる必要がある
class TOURNAMENT_MAKER():
    def __init__(self, members):
        self.members = members
        self.tournament = []
        self.start_pos = [ 120, 30]
        # 線を描く際のオフセット値
        self.y_space = 40
        self.x_space = 50

    def CreateNormalTournament(self):
        roundID = 0
        FinalRound = ROUND(0,roundID,self.members)

        self.tournament.append(FinalRound)
        self.largestStageNum = 0

        num = 0
        while len(self.tournament) > num :
            newRounds = [0]
            newRounds, roundID = self.tournament[num].CreateChildRound(roundID)
            if len(newRounds) == 2:
                self.tournament.extend(newRounds)
            if  self.largestStageNum <  self.tournament[num].roundID:
                self.largestStageNum = self.tournament[num].roundID
            num += 1

        self.SetPosition()

    def GetLargestStageNum(self):
        return(self.largestStageNum)

    def SetFirstRoundPosition(self):
        dst_firstRound = []
        for round in self.tournament:
            if len(round.memberList) == 1:
                dst_firstRound.append(round.roundID)
        self.firstRound = []

        for name in self.members:
            for round_id in dst_firstRound:
                if name == self.tournament[round_id].memberList[0]:
                    self.firstRound.append(round_id)
                    break

        for i in range(len(self.firstRound)):
            self.tournament[self.firstRound[i]].SetPosition(self.start_pos[0], self.start_pos[1] + i * self.y_space)
            self.tournament[self.firstRound[i]].SetPlayerID(i)

    def CalcNewCenterPosition(self, roundID):
        childID = self.tournament[roundID].childID
        x = max([self.tournament[childID[0]].x, self.tournament[childID[1]].x]) + self.x_space
        y = 0
        for id in childID:
            y += self.tournament[id].y
        self.tournament[roundID].SetPosition(x, int(y / 2))

    def SetPosition(self):
        self.SetFirstRoundPosition()
        stageNum = self.largestStageNum
        roundList = range(len(self.tournament))
        while stageNum >= 0:
            for round in roundList:
                if self.tournament[round].stageNum == stageNum:
                    if self.tournament[round].PositionSetFlag == 0:
                        self.CalcNewCenterPosition(round)
            stageNum -= 1

    def SetMatchScore(self, roundID, winnerID, score):
        self.tournament[roundID].SetWinnerID(self.firstRound[winnerID], score)

```

このクラスがやっていることは、
1. 参加者のリストから必要な数のROUNDクラスを呼び出して、トーナメントの形になるように親、子の関係を作ってあげること
2. 全てのラウンドの設置位置を計算してあげること
3. ROUNDクラスに勝ち負けをつける

の3つになります。

そのため、関数は複数個ありますが、実質外部から呼び出して上げる必要があるのは、

    * CreateNormalTournament()
    * SetMatchScore()

の２つ。

CreateNormalTournament()が1,2番の役割を、SetMatchScore()が3番の役割を果たしています。

1. については、トーナメントに参加するメンバをどんどん2で割ってゆき、割れなくなったら、トーナメントの追加を終えるという方法にしています。
2. については、参加者それぞれを格納するラウンドに最初に座標を割り当ててあげ、各ラウンドの中心をそのラウンドの子２つのちょうど真ん中になるようにy座標だけ計算してあげています。(**CalcNewCenterPosition()**)

### DRAW_SHAPE()
このクラスでは今回はpillowを使って絵を描きましたが、今後、他のライブラリを使いたいと思うことがあるかと思い、CUP_MAKER()の中でpillowの描画する関数を直接呼び出すのではなく、DRAW_SHAPE()を間に挟んであげています。
これによっていざ変えるときは、DRAW_SHAPE()の中のみを変更すればCUP_MAKER()やこのクラスを使っている他のクラスを全く変更する必要がありません。

``` python @DrawImage.py
import os, sys
from PIL import Image, ImageDraw, ImageFont

class DRAW_SHAPE():
    def __init__(self, image_name):
        self.size_x = 512
        self.size_y = 512
        self.image_name = image_name
        self.SetConfigure()

    def SetConfigure(self):
        self.SetRectangleSize()
        self.SetRectangleFillColor()
        self.SetEllipseFillColor()
        self.SetLineFillColor()
        self.SetEdgeColor()
        self.SetTextSize()
        #self.GetNewImage()

    def GetCenterPoint(self):
        return([self.size_x/2, self.size_y/2])

    def SetCenterPoint(self, x, y):
        self.size_x = x * 2
        self.size_y = y * 2


    def GetNewImage(self):
        self.im = Image.new("RGB",(int(self.size_x), int(self.size_y )),(255,255,255))
        self.draw = ImageDraw.Draw(self.im)


    def SetRectangleSize(self, dx=100, dy=20):
        self.rectangleXSize = dx
        self.rectangleYSize = dy

    def SetRectangleFillColor(self, color="white"):
        self.recColor = color

    def SetLineFillColor(self, color="black"):
        self.lineColor = color

    def SetEllipseFillColor(self, color="white"):
        self.ellipseColor = color

    def SetEdgeColor(self, color="Black"):
        self.edgeColor = color

    def DrawRectangle(self, x, y):
        self.draw.rectangle([(x,y),( x+self.rectangleXSize, y+self.rectangleYSize)], fill=self.recColor, outline=self.edgeColor, width=2)

    def DrawLine(self, x1,y1, x2, y2, width):
        self.draw.line((x1,y1,x2,y2), fill=self.lineColor, width=width)

    def DrawZigZagXtoY(self, x1, y1, x2, y2, width=10):
        self.DrawLine(x1,y1,x2,y1, width)
        self.DrawLine(x2,y1,x2,y2, width)

    def DrawZigZagYtoX(self, x1, y1, x2, y2, width=10):
        self.DrawLine(x1,y1,x1,y2, width)
        self.DrawLine(x1,y2,x2,y2, width)


    def DrawEllipse(self, x1,y1, x2,y2):
        self.draw.ellipse(( x1, y1, x2, y2), fill=self.ellipseColor, outline=self.edgeColor)

    def SetTextSize(self, textSize=10):
        self.textSize = textSize

    def SetModestTextSize(self, char, targetWidth):
        width = self.textSize * len(char)
        if width < targetWidth:
            self.textSize = targetWidth / len(char)

    def SetFontSize(self):
        self.font = ImageFont.truetype(size=self.textSize)

    def SetImageSize(self, x, y):
        self.size_x = x
        self.size_y = y

    def DrawText(self, x1, y1, text):
        self.draw.multiline_text((x1,y1), text, font=self.font, fill=(0,0,0,255))

    def SetFontFile(self, fontFileName, size=10):
        fileDir = os.path.dirname(os.path.abspath(__file__)) + "/../font/" + fontFileName
        self.font = ImageFont.truetype(fileDir, size)


    def ShawImage(self):
        self.im.show()

    def SaveImage(self):
        img_dir = os.path.dirname(os.path.abspath(__file__)) + "/../../image/"
        if os.path.exists(img_dir):
            ret = 1
        img = img_dir + self.image_name + ".jpg"
        self.im.save(img)
        return img
```
### **CUP_MAKER()**
ここではこれまで説明したクラスを統括し、何個のトーナメントを作ればいいかを判断するクラスであるCUP_MAKER()を説明します。

``` python @CupMaker.py
# @brief TOURNAMENT_MAKER()にメンバーを登録する前の下処理と画像作成をしてあげるクラス
# TOURNAMENT_MAKERを内包しているので、ラッパーとして勝ち負けを登録する関数などを加えている
class CUP_MAKER():
    def __init__(self, member, tournament_name):
        self.tournament = []
        self.start_pos = [-100,0]
        self.line_width = 3
        self.tournament_name = tournament_name
        self.member = member
        self.member_size_array = self.CalcTournamentNum(member)


    def CreateCupTournament(self):
        for member_size in self.member_size_array:
            self.tournament.append(TOURNAMENT_MAKER(self.member[:member_size]))
            if len(self.member) > member_size:
                self.member = self.member[member_size:]
        for i in range(len(self.tournament)):
            self.tournament[i].CreateNormalTournament()

    def CalcTournamentNum(self, member):
        list_size_array = []
        divide_num = len(member)/12
        min = 100
        divide_size = len(member)
        if divide_num >= 1 and len(member)%12 != 0:
            for i in range(6, 13):
                remain = len(member) % i
                if min > remain:
                    min = remain
                    divide_size = i
            divide_num=len(member)//divide_size
            list_size_array = [divide_size for i in range(divide_num)]
            for i in range(min):
                list_size_array[i]+=1
        else:
            list_size_array.append(len(member))
        return(list_size_array)

    def SetRoundWinner(self, tournament_num, round_id, winner_id, score):
        self.tournament[tournament_num].SetMatchScore(int(round_id), int(winner_id), score)

    def DrawTournamentImage(self):
        tournament_num = 0
        image_list = []
        for tournament in self.tournament:
            image_name = self.tournament_name + str(tournament_num)
            ds = DRAW_SHAPE(image_name)
            ds.SetFontFile("font_1_honokamarugo_1.1.ttf", size=12)
            ds.GetNewImage()
            ds.DrawText( 0, 0,self.tournament_name+str(tournament_num))
            for t in tournament.tournament:
                width = self.line_width
                if t.stageNum == 0:
                    # 決勝戦は先にラウンドがないけど横線を引きたい
                    if t.winner != -1:
                        # 勝者のいる場合は太線で描画
                        width *= 2
                    ds.DrawLine(t.x, t.y, 50 + t.x, t.y, width=width)

                if len(t.childID) > 1:
                    for child in t.childID:
                        width = self.line_width
                        if t.winner != -1:
                            # もしこのラウンドの勝者がいる場合,太さを二倍とスコアを表記
                            for member in tournament.tournament[child].memberList:
                                if member == tournament.tournament[t.winner].memberList[0]:
                                    width *= 2
                                    ds.DrawText(t.x + 20, t.y - 20, t.score)
                        # ジグザグの線を描画
                        ds.DrawZigZagYtoX(t.x, t.y, 5+tournament.tournament[child].x, tournament.tournament[child].y, width=width)
                        ds.DrawText(t.x + 5, t.y - 20, "[" + str(t.roundID) + "]")
                if len(t.memberList)==1:
                    ds.DrawRectangle(self.start_pos[0]+t.x,self.start_pos[1]+t.y - 10)
                    member_name_text = " " + str(t.player_id) + "." + t.memberList[0]
                    ds.DrawText(self.start_pos[0]+t.x,self.start_pos[1]+t.y-5,member_name_text)
            image_dir = ds.SaveImage()
            image_list.append(image_dir)
            tournament_num += 1
        return(image_list)
```

このクラスがやっているのは、TOURNAMENT_MAKER()を何個呼び出せばいいのかという判断と、それぞれのトーナメント表をDRAW_SHAPE()クラスを呼び出して上げて画像に変換してあげるところです。
そのため実はこのクラスではあまり説明することがありません。
もしトーナメント表のデザインなどが好みでないなどがあれば変更するのがこのクラスになると思います。

###  画像の送信
今回、トーナメント表をjpgファイルで作成し、作成した画像のパスを渡してDiscordの各サーバーに送信しています。
その部分について簡単に説明します。
``` python @ BotBase.py
if image_list != "null":
    for img in image_list:
            await client.send_file(message.channel, img)
```
私が作成したBOT_BASE()クラスは送信するメッセージ(String型)のみか、
メッセージと画像のパスを格納したリスト型のimage_listを返してきます。
そこで、そのリストに格納されたパスの先にある画像を送ってくれるのが、
send_file()関数です。この関数では送信する先のChannelと送信するファイルを指定するだけで簡単に送信することができます。


## まとめ
今回は以前作成したチーム分けのbotにトーナメント表を作成する機能を追加しみました。
かなり手を抜いてしまいましたが、構成としてはそこそこのものができたと思っています。
ただ、今後、これを拡張していく上で、Discord側から受け取るコマンドに対し、どの関数を呼び出すのかというif文が増えていってしまうという問題を抱えているので、今後はそれをまずは対処しつつ、ROUND()クラスを純粋なGraph構造に近づけるように改造したいと思います。
次に加える新機能としては、まずリーグ戦を作れるようにすること。
次にリーグ戦の後にトーナメントを行うような場合に対応させることです。


以上、TeamMaker Botの話でした!!
