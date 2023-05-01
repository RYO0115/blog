
- [概要](#%E6%A6%82%E8%A6%81)
- [Systemd](#systemd)
- [serviceファイルの作成](#service%E3%83%95%E3%82%A1%E3%82%A4%E3%83%AB%E3%81%AE%E4%BD%9C%E6%88%90)
- [Subprocess](#subprocess)
- [終わりに](#%E7%B5%82%E3%82%8F%E3%82%8A%E3%81%AB)

# 概要
先日投稿したTeamMakerですが、現在私はRaspberry Piにて運用しています。

ただ最近、OSの問題か、電源の問題か、Raspberry Piが何もしていないのに落ちていることがあります。
なので、今回はその対策として、Raspberry Pi起動時にPythonを自動的に実行する方法と
Pythonの処理が通信の途絶などで誤って終了した際に自動的に再起動してくれるスクリプトを勉強したので紹介したいと思います。

# Systemd
今回はLinuxの起動処理やシステムを管理するSystemdという機能を利用します。
こちらを参考にさせていただきました。


そもそもSystemdとは何か。[DevelopersIO](https://dev.classmethod.jp/cloud/aws/systemd-getting-started/)さんで調べたところ、以下のようにありました。


>Systemdは、サービスを1つのシェルスクリプトではなくUnitという単位で管理し、設定ファイルとして持ちます。
>このため処理を細分化でき個別に実効することが可能です。また処理ごとの依存関係を明確にすることが出来ます。
>さらに処理が細分化されているということは、並列での実行も可能になります。例えばAの処理の後にBとCを並列実行する、
>というようなきめ細やかな設定が可能になります。

つまりは処理ごとに **.service**というファイルを **/lib/systemd/system/** 以下に作成してあげれば起動時や
デバイスを差し込んだ際にプログラムを設定することができます。

# serviceファイルの作成
まずは **/lib/systemd/system/**の中に.serviceファイルを作成します。
ここでserviceファイルの名前は任意なので、この記事の中では **TeamMaker.service**と記述する **TeamMaker**の部分を任意の名前にしてください。

    sudo vim /lib/systemd/system/TeamMaker.service

```
[Unit]
Description = TeamMaker

[Service]
ExecStart=/usr/local/bin/python3 /home/pi/workspace/Discord_Python/discord_python/src/main.py
Restart=always
Type=simple

[Install]
WantedBy=multi-user.target
```
[Unit]の **Description**ではその処理の詳細を書きます。
今回は指定していませんが、この[Unit]の中では**after**や**before**などを

    after=test.service

のように記述することで、他の処理の後に実行という設定ができます。
この他にも同じ使い方で**Require**や**Wants**などを使うことで、依存関係を表現することができます。

[Service]の中では主に実行コマンドを記述します。
**ExecStart**に実行コマンドを記入します。
今回はpython3でTeamMaker.pyを実行します。
なので実行するpythonを指定し、実行するpythonプログラムのフルパスを指定しています。
**Restart**はプロセスの再起動です。
一応、エラーが発報されても再実行するスクリプトを後で紹介しますが、念の為常に再実行するように設定しておきます。

[Install]ではシンボリックリンクの設定をしています。
**Wantedby**は.watsディレクトリにシンボリックリンクを貼る


詳しくはこちらの[メモ書き](https://qiita.com/a_yasui/items/f2d8b57aa616e523ede4)などを見てください。

# Subprocess

今回は[こちら](https://teratail.com/questions/112500)の質問への回答を参考にしました。

``` python
import os,sys
import time
import subprocess

COMMAND  = "python3"
SRC_NAME = "TeamMaker.py"

dir = os.path.abspath(__file__)
proc = subprocess.Popen([COMMAND, dir[:-len("main.py")] + SRC_NAME])
while True:
    ecode = proc.poll()
    if ecode is None:
        time.sleep(2)
    elif ecode == 0:
        print('NORMAL END')
        print('NEXT' + '-'*10)
        proc = subprocess.Popen([ COMMAND, SRC_NAME])
    else:
        print('ERROR')
        print('RESTARTING' + '='*10)
        proc = subprocess.Popen([ COMMAND, SRC_NAME])
```
subprocessモジュールはターミナル上で実行するコマンドを実行することができます。また、proc.poll()の返り値をみることでエラーも確認できますので、今回はこれを利用します

このスクリプトの内容は凄くシンプルです。
``` Python
proc = subprocess.Popen([COMMAND, dir[:-len("main.py")] + SRC_NAME])
```
この部分で何度も再起動させるpythonプログラムと実行方法を指定します。
今回は実行方法には"python3"を、実行対象には絶対パスでTeamMaker.pyを指定しています。
その後、
``` python
ecode = proc.poll()
```
にてエラーコードを取得します。
ecode == Noneが実行中
ecode == 0というのは正常終了、
それ意外がエラーになります。

これによって処理が突然落ちてしまっても再度起動されるようになります。

# 終わりに
これでTeamMakerの運用環境はほぼほぼ作り終えたので、今後はサッカー分析ツールを作成していきたいと思います。
