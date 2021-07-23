# Windows10にWindows Subsystem for Linux(Ubuntu 20.04)をインストールしてみた & VSCodeで環境を整えてみたよ


## 概要

C++のモジュールテストの勉強をしようとGoogle先生とお話していたところ、そういえばUbuntu環境を整えていないなと思い、前からやってみたかったWSL2, Ubuntu20.04のインストールとその開発環境を整えたので備忘録として残します。


## WSLとは？

そもそもWSLとは何か。[マイクロソフト公式サイト](https://docs.microsoft.com/en-us/windows/wsl/about)を見てみると以下のようにありました。

> The Windows Subsystem for Linux lets developers run a GNU/Linux environment -- including most command-line tools, utilities, and applications -- directly on Windows, unmodified, without the overhead of a traditional virtual machine or dualboot setup.

>"WSL(Windows Subsytem for Linux)とは、開発者に仮想マシンやデュアルブートのインストールを必要とせずに、Windows上で直接GNU/Linux環境(例えばコマンドライン、アプリケーションなど)を実行できるツールです。"

かつて、プログラマ=Macという時代がありました。

(OSの歴史は簡単にまとまっている[こちらのサイト](https://qiita.com/tatsuya4150/items/09a45b71d7a6fb58efd0)が参考になると思います)

その最大の要因は、MacはUnix系のOSのため、Linuxチックにソフトを開発できるということがメリットとしてありました。
(コマンドもLinuxと同じものが多い)

対してWindowsはIBMと共同開発していたMS-DOSの系譜なので、コマンドラインなどが上のMac, Linuxとは違っていました。

世間的にはWindowsのほうがシェアが大きいですが(90%以上)、意外と組み込み系はLinuxベースの物も多く、特に研究用途などではUnixベースで書かれることが多くありました。

あと、個人的な意見ですが、コマンドラインで環境を構築しずらかったのもWindowsが避けられていた原因の1つだと思います。

というような背景もあり、しばらくプログラマ=Mac (or Linux)という状態が続いていたのですが、数年前(2016年)ついにWindowsが動きました。

そう、WSL(Windows Subsystem for Linux)のリリースです。

これによって、WindowsでもLinux, Unixベースのソフト開発ができるようになったためよりWindowsが優位になったのです。


(個人的にはいまだにWindowsはイライラする点が多いですが。。。)


## WSLは何ができるの？

じゃあ、WSLは何ができるのか。

基本的にWSLはコマンドラインで動かす形になりますが、Ubuntuなどを入れることで簡単なLinuxのコマンドからGUIアプリケーションまで幅広く実行できます。[参考](https://docs.microsoft.com/en-us/windows/wsl/tutorials/gui-apps)

自分みたいにUbuntuのためにRaspberry PiやUSBメモリを用意するといった作業が必要なくなります。


## どうやってWSLをインストールするの？

基本的には[公式サイト](https://docs.microsoft.com/ja-jp/windows/wsl/install-win10#step-4---download-the-linux-kernel-update-package)に沿ってコマンドを打ち込んでいくだけです。

## Windowsのバージョン確認

まず、Windowsのバージョンを確認します。

以前は[Windows Insider Program](https://insider.windows.com/en-us/getting-started)に参加する必要があったはずですが、なぜか自分は参加せずとも動かすことができました。

(もしかしたら手動インストールをする分にはInsider Programへの参加は必要がないのかもしれません)

ちなみに自分は以下のバージョンで実施しました。

    エディション	Windows 10 Pro
    バージョン	21H1
    OS ビルド	19043.1110

上よりも新しいバージョンになっていれば問題ないと思います。

公式には以下のような記述があります。

>WSL 2 に更新するには、Windows 10 を実行している必要があります。
>
>x64 システムの場合:バージョン 1903 以降、ビルド 18362 以上。
>
>ARM64 システムの場合:バージョン 2004 以降、ビルド 19041 以上。
>
>18362 より前のビルドは WSL 2 をサポートしていません。 Windows 更新アシスタントを使用して、お使いのバージョンの Windows を更新します。

もし更新が必要な場合は以下の通りです。

### 更新方法

1. まず左下のWindowsマークから設定を開きます。




2. 設定の中から『更新とセキュリティ』を選択します。



3. その後、赤丸で囲った更新プログラムのチェックを押してください。


4. もしアップデートがあれば自動でアップデートしてくれます。



ただし、再起動が必要なアップデートがあれば可否が聞かれますので、再起動してもいいように他のアプリは落としておきましょう。



## WSLのインストール

WSLには二つバージョンがあります。WSLとWSL2です。

違いは[こちらの公式サイト](https://docs.microsoft.com/en-us/windows/wsl/compare-versions)に載っています。

基本的にはWSL2の方がパフォーマンスが上がること、制限なしのLinuxカーネルを利用できることがあげられてます。

唯一WSL2ができない(非推奨な)ことが、Windows <-> Linux間でファイルを同時に操作することですが、正直そのようなソフトを作ることはかなりレアケースだと思いますので、基本的には何も考えずにWSL2をインストールすればいいと思います。

(新しい物好きの自分は何も考えずにWSL2をインストールしました)

### Powershellを管理者権限で開く

これから3つのコマンド(オプション機能を2個有効、WSLのバージョン変更)をPowershellで実行しますが、管理者権限が必要になります。

もし開き方が分からない方は以下の通りに実行してください。

1. まず左下のWindowsマークを**右クリック**します

2. Windows Powershell (管理者)から管理者権限を付与したPowerShellを実行できます


### Linux用Windowsサブシステムを有効にする

まずLinux用Windowsサブシステムというオプション機能を有効にする必要があります。

先ほど開いたPowerShellで以下のコマンドを実行してください。

    dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart

注意:ちなみにWSLをインストールする方はここで再起動をして[Linuxディストリビューションをインストールする](#linuxディストリビューションをインストールする)に進んでください。


### 仮想マシンの機能を有効にする

次に二つ目のオプション機能、仮想マシンプラットフォームを有効にします。

先ほどのPowerShellで以下のコマンドを実行してください。

    dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart

ここで再起動を求められますので「Y」を押してください。
(Yを押した瞬間に再起動しますので、びっくりするかもしれません)

Windowsが再度立ち上がったら次に進んでください。

### Linuxカーネル更新プログラムパッケージをダウンロードする

ここではLinuxカーネル更新プログラムパッケージをインストールしていきます。

[公式サイト](https://docs.microsoft.com/ja-jp/windows/wsl/install-win10#step-2---check-requirements-for-running-wsl-2)のこちらのリンクからパッケージをダウンロードし、実行してください。

管理者権限を求めるウィンドウやインストールしますか、などを聞かれるのですべて「はい」を押してください。

### WSL2を既定のバージョンとして設定する

再度PowerShellを開き(自分は念のため管理者権限で開きました)、WSLの既定バージョンをWSL2とするために以下のコマンドを実行します。

    wsl --set-default-version 2

これで基本的な設定は終わりです。

### Linuxディストリビューションをインストールする

さて、やっとLinuxをインストールしていきます。

上のパッケージをインストールすると、[Microsoft Store](https://aka.ms/wslstore)から様々なLinuxをダウンロードできるようになります。

ここでは例として[Ubuntu20.04 LTS](https://www.microsoft.com/store/apps/9n6svws3rx71)を入れていきます

上のリンクに飛んでいただくと入手ボタンがあるので、それをクリックします。

するとインストールするというボタンが上に現れますので、それもまたポチリます。

これでインストールは終わりです。

### Linuxディストリビューションのアカウントとパスワードを作成する

スタートメニューなどからLinuxディストリビューションを起動すると、最初の一回のみ以下のような画面がでてきます。


(撮り忘れたので公式より引用)

ここでユーザアカウント名とパスワードを設定すれば、WSL2 + Ubuntu20.04のインストールは完了です！！


## WSL2 + VSCodeによる開発環境構築

ここからはWSL2とVSCodeを使った開発環境構築の方法を説明していきます。

こちらの方法は[公式サイト](https://code.visualstudio.com/docs/remote/wsl)にも載っています。

### 構築前に確認すべきこと

確認すべきことは主に以下の二つです。

* [WSLのインストール](#wslのインストール)が完了していること
* Windows側にVSCodeをインストールしていること

VSCodeがインストールできていない方は[こちらのサイト](https://code.visualstudio.com/download)からダウンロードしてインストールしてください。


### VSCodeでリモート開発拡張パックをインストールする

VSCodeには様々な拡張機能を追加することができます。

例えば、プログラミング言語ごとのintellisense(補完機能)やデバッグ機能、自動でCMakeListを作ってくれる機能だったり、markdownのプレビューができるなど様々です。

自分がどういう拡張機能を使ってるかはまた今度紹介したいと思います。

さて、リモート開発拡張パックをインストールするのですが、ここで選択肢が二つあります。

公式サイトでは、[Remote Development](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.vscode-remote-extensionpack)というWSLに限らず、他のパソコンや仮想マシンにSSHでアクセスしてリモート開発できる拡張パックをインストールすることを推奨しています。

ですが、ただWSLでのみ開発することを考えている方にはこのパックはToo Muchな気がします。

また設定もいくつか必要となります。

なのでここでは、[Remote WSL](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-wsl)を紹介します。

どちらもMicrosoftが提供している公式拡張パックですが、後者はWSLに限定しています。

なのでほぼ何も考える必要もなくWSL上のUbuntuにリモートアクセスすることができます。

インストール方法は、リンク先のインストールを押すか、VSCodeの拡張機能から検索してインストールすることができます。

### Remote WSLの使い方

1. まずVSCodeウィンドウの左下にある緑のボタンをクリックします。

2. すると下のようなコマンド一覧がVScodeウィンドウの真ん中上部に表示されます。

3. この中から『New WSL Window』を選択します。するとUbuntu20.04のディストリビューションに接続した状態のウィンドウが開かれます。

あとはいつものVScodeの感覚でファイル編集や操作ができます。

### (余談)Linuxディストリビューションの切り替え方

ちなみに複数のLinuxを入れている場合(例えば18.04と20.04など)、簡単に切り替えができます。

1. 先ほどと同じく左下の緑色のボタンをクリック
   
2. コマンド一覧の中から『New WSL Window using Distro ...』を選択
   
3. 使いたいディストリビューションを選択

で開く切り替えることができます。


## 終わりに

というわけで、今回はWSL2+Ubuntu20.04のインストールからVSCodeの設定までやってみました。

過去に山ほどLinuxインストールで苦しんできた身としてはこれほど簡単にインストールできるのは感動ものでした。

ぜひ皆さんも試してみてください。

ではでは!!



