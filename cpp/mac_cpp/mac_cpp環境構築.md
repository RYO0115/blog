# MacでC++環境を構築してみた。

## 概要

Macbook Airを購入したのでC++環境を構築してみました。

ここではC++のcmakeでのコンパイル開発環境整備までを書いていきます。

## g++を使えるようにする。

まずはXCodeをインストールしてg++によるコンパイルができるようにします。

これはただAppStoreから引っ張ってくるだけなので簡単です。

[こちらのリンク](https://apps.apple.com/jp/app/xcode/id497799835?mt=12)からか、あるいはAppStoreでXCodeを検索してください。

あとはインストールするのみです。

その後新しくターミナルを立ち上げg++コマンドが呼び出せるようになっているか確認してください。

合わせてmakeコマンドも入っているはずです。

## cmakeを使えるようにする。

次にcmakeを使えるようにします。

cmakeとは何かについては[こちら](https://rinatz.github.io/cpp-book/make-cmake/)のサイトなどがわかりやすいので参考にしてください。

平たく言うとコンパイル時にどのような手順でコンパイルしていくか(依存関係)やそのコンパイルの各種設定を自動でしてくれるビルド自動化ツールです。

なのでソースコード1つだけの時はほとんど恩恵を受けることはありませんが、ソフトの規模が大きくなればなるほどその恩恵は大きくなります。

特に個人的に嬉しいのがCMakeLists.txtで色々なところへパスを簡単に通せるので、作成した機能の役割ごとにディレクトリを分けるなどよりコードの可読性をあげ、他のプロジェクトに移植しやすかったりすることです。

なので自然とライブラリチックに単一機能に絞って書くことが習慣付くので自分は気に入っています。

### cmakeのインストール


#### homebrewインストール

インストールにはhomebrewを使います。

homebrewのインストール方法はこちらの記事をご確認ください。

#### cmakeをインストールする

特に難しいことはありません。

    brew install cmake

これだけです。

あとはターミナルを開き直してもらえればcmakeコマンドが出ていることがわかるかと思います。

### cmakeの使い方

#### CMakeLists.txt

下に自分がよく使うCMakeLists.txtを掲載します。

このCMakeListsが設定しているのは以下の項目です。

- C++コンパイル時設定
- 実行ファイルの出力先(存在しない場合は自動的に生成)
- ライブラリの出力先(存在しない場合は自動的に生成)
- includeで参照するパスの指定
- 使用するライブラリがあるパスの指定
- コンパイルしたい実行ファイルに必要なソースコードを指定
- コンパイルしたい実行ファイルに必要なライブラリを指定

```
# Cmake version
cmake_minimum_required(VERSION 3.5)

# Project Name and Language
project(thread_sample CXX)

set(CMAKE_CXX_FLAGS "-g -O0 -Wall -pthread -std=c++11")
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${PROJECT_SOURCE_DIR}/bin)
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${PROJECT_SOURCE_DIR}/lib)
set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${PROJECT_SOURCE_DIR}/lib)

# Add Subdirectory
#add_subdirectory(src)

# Add Include dir
#target_include_directories(
#	new_object_detection
#	${PROJECT_SOURCE_DIR}/include
#)

include_directories(
	${PROJECT_SOURCE_DIR}/include
	)

link_directories(
	${PROJECT_SOURCE_DIR}/lib
)

add_library(thread_sample STATIC src/thread_sample.cpp)
target_link_libraries(thread_sample)

add_executable(thread_test test/thread_test.cpp
)
target_link_libraries( thread_test
		thread_sample

)

```


#### cmakeの実行場所

個人的にcmake, makeはコンパイルしたいプロジェクト内にbuildディレクトリを作成し、その中で実行することをオススメします。

この理由はコンパイル時に生成される途中経過ファイルが全てbuildファイルの中に保存されるため、他のディレクトリを汚さないことなどが挙げられます。

なのでまずbuildディレクトリを作成し、ターミナルでbuild内に移動した後、

    cmake ..

を実行してください。

するとbuildの中にMakefileが生成されます。これでmakeができるようになります。

    make

同じくbuildの中でmakeを実行してもらえれば、自分で指定した実行ファイルやライブラリがbinやlibの中に生成されます。

## 実践

ここでは自分がサンプルで作ったマルチスレッドクラスのSampleThreadを参考に実際に使ってみます。

今回使用するリポジトリは[こちら](https://github.com/RYO0115/cpp_tester)です。

今回はこのcpp_testerの中にあるSampleThreadを例にcmake, makeを使っていきます。

### git clone

まずはこちらのレポジトリをローカル(自分の手元のパソコン)にclone(ダウンロード)してきます。

保存したいディレクトリにターミナルで移動して以下のコマンドを実行いただくか、下の画像にあるDownload zipからzipファイルでダウンロードしてください。

    git clone https://github.com/RYO0115/cpp_tester.git

[画像]

### buildディレクトリを作る

次にcpp_tester/SampleThreadの中にbuildディレクトリを作成し、ターミナルでその中に移動します。

### cmake

ここで実際にcmakeを実行します。

次のコマンドを実行してください。

    cmake ..

ここでcmakeの後の".."はCMakeLists.txtの場所になります。

もしbuildディレクトリの中にCMakeLists.txtがある場合は、このコマンドは"cmake ."になります。

ただ基本的にCMakeLists.txtとcmakeを実行する場所は分けることをオススメします。

それは上にもちらっと書きましたが、cmake, makeの中間ファイルが全てコマンドを実行した場所に保存されるからです。(CMakeLists.txtで保存場所を変更すればその限りではありませんが)

なので基本的には分けておいた方が無難でしょう。

[画像]

### make

cmakeを実行したことでbuildの中を覗くとMakefileが生成されているかと思います。

[がぞう]

これでコンパイルの準備が完了です。

実際にmakeしてみましょう。build内で以下のコマンドを実行してください。

    make

これでsrc,test内のソースコードがコンパイルされます。

### ファイルの確認

最後に今回のcmake, makeで出来上がったものを確認します。

[画像]

thread_testが実行ファイル、libthread_sample.aが今回静的ライブラリ化したファイルになります。

他のソースコードでSampleThreadクラスを使いたい場合は、includeの中にあるthread_sample.hppとこのlibthread_sample.aへパスを通せば使えるようになります。


## 最後に

と言うわけでC++のコンパイルができる環境構築と実行方法をまとめました。

CMakeLists.txtは上で書いたものの数倍数十倍の機能があるので、必要に応じてどんどんいじってください。

ではでは。
