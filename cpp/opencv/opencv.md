# WSL + OpenCVを使って画像処理をしてみたよ #1

## 概要

## WSL(Ubuntu20.04) + OpenCV のセットアップ

### OpenCVインストール時に必要な各種ライブラリのインストール

基本的には[OpenCVのLinuxインストールページ](https://docs.opencv.org/4.1.2/d7/d9f/tutorial_linux_install.html)を参照しています。

ターミナルを開き、以下のコマンドを実行

コンパイラのインストール

    sudo apt-get install build-essential


cmkaeやコーデックなどのインストール(推奨)

    sudo apt-get install cmake git libgtk2.0-dev pkg-config libavcodec-dev libavformat-dev libswscale-dev

その他png, jpegなどのライブラリインストール(オプション)

    sudo apt-get install libtbb2 libtbb-dev libjpeg-dev libpng-dev libtiff-dev

最後のオプションについては、python-dev, python-numpy, libjasper-dev, libdc1394-22-devが僕はインストールできなかったので、

上記のもののみにしています。

また必要があれば随時追加してください。


### OpenCV, OpenCV-ContribをGitからクローンしてくる

OpenCV, OpenCV-ContribはGitHub上に上がっています。

Linuxの場合はそこからクローン(ダウンロード)して、コンパイルする必要があります。

まずは作業場に移動します。

    cd ~/workspace

ここでworkspaceは僕のプログラミング用作業場です。
もし他の場所に別途作られているのであれば、その場所にしてください。

次にOpenCV, OpenCV-Contribをクローンしてきます。

    git clone https://github.com/opencv/opencv.git
    git clone https://github.com/opencv/opencv_contrib.git

ここでは特にバージョンを指定していません(=Masterブランチを選択しています)が、指定のバージョンがいいということでしたら、

    git clone https://github.com/opencv/opencv.git -b 2.4
    git clone https://github.com/opencv/opencv_contrib.git -b 2.4

のような形でブランチを指定してください。


### OpenCVをcmakeする

OpenCVをコンパイルする前に、コンパイルの設定をするためにcmakeを実行します。


まずコンパイル後の実行ファイルなどを格納するディレクトリをopencvディレクトリの中に先に作成し、その中に移動します。

    mkdir build
    cd build


コマンドは以下の通り

    cmake -D CMAKE_BUILD_TYPE=Release -D CMAKE_INSTALL_PREFIX=/usr/local -D WITH_TBB=ON -D WITH_OPENGL=ON -D OPENCV_EXTRA_MODULES_PATH=~/workspace/opencv_contrib/modules -D OPENCV_GENERATE_PKGCONFIG=ON ..

ここで重要なのが、**OPENCV_EXTRA_MODULES_PATH**です。

今回はOpenCV-Contribを使うのでOpenCV-Contribのmodulesのパスを教えてあげる必要があります。

なので、例えば、~/Documents/workspace/にOpenCV-Contribをクローンした際には

    OPENCV_EXTRA_MODULES_PATH=~/Documents/workspace/opencv_contrib/modules

という形になります。

上のcmakeを実行し、問題なく終了した場合、build内にMakeFileができているはずです。

基本的には問題なく進むのですが、たまにcmakeがエラーを出します。

その場合はcmakeを実行した最後にErrorLogが出ますので、何が足りないかはそこで確認できます。

そのログの最後の方にsys/videoio.hが足りないなどが出てくるかと思いますので、グーグル先生でそのヘッダが入ったライブラリを教えてもらい、適宜apt installしてください。



### OpenCVをmakeしてmake installする

ここまで出来たら最後にmakeしていきます。

build内で以下のコマンドを実行します。

    make -j7

-j7は並列に何個のジョブを実行するかというオプションです。

これでも数分以上時間がかかりますので、気長に待ってください。

最後にコンパイルが終ったら、他のディレクトリにあるプログラムからアクセスできるように、make installしていきます。

    sudo make install

これでOpenCVのインストールは完了です。

## 