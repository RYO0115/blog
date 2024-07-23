# C++でマルチスレッドのクラスを作ってみたよ

## 概要

以前、C++のマルチスレッドについて説明し、サンプルを書きました。

それ以降、業務内でマルチスレッドのアプリを作ることが頻繁にあったので、今回はマルチスレッドのみにフォーカスしたクラスを作ってみたので紹介したいと思います。


## リポジトリ

リポジトリは[こちら](https://github.com/RYO0115/cpp_tester/tree/master/SampleThread)になります。

cpp_test/SampleThreadが今回のサンプルプロジェクトになります。

## 使用方法

こちらのリポジトリをクローンしていただき、SampleThreadディレクトリ内にbuildディレクトリを作ってください。

その後、以下のコマンドを実行します。

    cmake ..

    make

これでサンプルがbinディレクトリの中に生成されるので、動かすことができます。

## サンプルの内容

サンプルの内容はシンプルです。

SampleThreadクラスがメインスレッドとは別に100ms周期で動いていて、常にカウンタ(_counter)を更新し、更新したカウンタを適当な変数(_value)に加算し続けます。

メインスレッドも同じく100msで動いており、1秒に1回(10回に1回)この_valueを適当な数字に書き換えます。

マイループ、メインスレッドはSampleThreadクラスから_counterと_valueの値を取り出し、画面に表示すると言うプログラムです。

具体的に中身を見ていきましょう。


### メイン

メインは今回testディレクトリ内に作っています。(今回作ったSampleThreadクラスの動作テストなのでtest内にしました.srcの中でも問題ありません)

main関数では頭でSampleThreadを宣言し、SampleThreadクラスのスレッドをスタート(GenerateThread())し、ループを回し始めます。

SampleThreadクラスから値を取り出すのは各種Get関数を使っています。

またSampleThreadクラスに値をセット(リセット)する際もResetValue()関数を使用します。

では次にSampleThreadクラスの中を見ていきます。

``` cpp: test/thread_test.cpp
#include "thread_sample.hpp"

int main(void)
{
    SampleThread st;
    const int sleep_msec = 100;

    st.GenerateThread();
    int counter = 0;
    int value = 0;

    while(1)
    {
        counter = st.GetCounter();
        value = st.GetValue();

        printf("Counter: %d Value:%d", counter, value);

        if(counter % 10 == 0)
        {
            printf(" --> Value Reset");
            st.ResetValue(10);
        }
        printf("\n");
        
        std::this_thread::sleep_for(std::chrono::microseconds(sleep_msec));
    }

    st.CloseThread();
    return(0);
}
```

### SampleThreadクラス

### sample_thread.hpp

まずはHeaderです。

ここで必要な変数、変数の値を取り出す、セットするための関数、別スレッドで回すmain関数、mutex, threadなどを宣言しています。

``` cpp : include/sample_thread.hpp
#include <thread>
#include <chrono>
#include <mutex>

class SampleThread
{
private:
    std::thread _main_thread;
    std::mutex _main_mutex;

    int _counter;
    int _value;
    bool _stop_loop;

    void _StopLoop(void);
    void _Main(void);
    void _UpdateData(void);

public:
    SampleThread() : _counter(0), _value(0), _stop_loop(false)
    {

    }
    ~SampleThread() {}

    void GenerateThread(void);
    void CloseThread(void);

    int GetCounter(void);
    int GetValue(void);
    void ResetValue(const int &value);
};
```

#### sample_thread.cpp

ここでの肝はスレッドの開始、終了を行うGenerateThread(), CloseThread()関数とメモリのアクセス違反に気を遣いながら値を更新している_UpdateData(), ResetValue()関数です。

``` cpp: src/sample_thread.cpp

#include "sample_thread.hpp"


void SampleThread::_Main(void)
{
    const int sleep_msec = 100;
    while(!_stop_loop)
    {
        _UpdateData();
        std::this_thread::sleep_for(std::chrono::microseconds(sleep_msec));
    }
}

void SampleThread::_UpdateData(void)
{
    std::lock_guard<std::mutex> lock(_main_mutex);
    _counter++;
    _value += _counter;

    if(_counter > 100) _counter = 0;
}

void SampleThread::_StopLoop(void)
{
    _stop_loop = true;
}

void SampleThread::GenerateThread(void)
{
    _main_thread = std::thread(&SampleThread::_Main, this);
}

void SampleThread::CloseThread(void)
{
    _StopLoop();
    _main_thread.join();
}

int  SampleThread::GetCounter(void)
{
    return(_counter);
}

int SampleThread::GetValue(void)
{
    return(_value);
}

void SampleThread::ResetValue(const int &value)
{
    std::lock_guard<std::mutex> lock(_main_mutex);
    _value = value;
}

```

#### スレッドの開始/終了

まずGenerateThread()では、std::thread()を使ってこのクラスの_Main関数を別スレッドで動かすよ、と宣言しています。

逆にCloseThread()では_Main関数ないのループを終了するフラグを立てた後にjoin()関数を使って別スレッドが正常に終了するのを待っています。

join()関数で勘違いしやすいのは、この関数はあくまで"そのスレッドが終了したかを確認する"のであって"スレッドを終了させる"関数ではありません。

なのでjoinの前に止めたいスレッドの停止処理を用意してあげる必要があります。

またここでスレッドを止めないとプログラムを止めても裏でこのスレッドが回り続けてしまうので必ず呼びましょう。

``` cpp: src/sample_thread.cpp
void SampleThread::GenerateThread(void)
{
    _main_thread = std::thread(&SampleThread::_Main, this);
}

void SampleThread::CloseThread(void)
{
    _StopLoop();
    _main_thread.join();
}
```

#### 別スレッド同士のデータの読み書き

次にデータの読み書きについてです。

以前こちらの記事で説明したように、マルチスレッドのプログラムで一番気を付けるべきはメモリのアクセス違反(segmentation fault)です。

他のスレッドが編集中のメモリに対してさらに別のスレッドが同じメモリを書き換えようとするとこのエラーが発生し、プログラムが強制的に終了してしまいます。

なのでそれを防ぐためにstd::threadと合わせて使われるのがstd::mutexです。

mutexとはメモリを書き換える際に既に他のスレッドが書き換えている場合は他のスレッドの処理が終わるのを待ちましょうと言う機能です。

このサンプルでは以下の二箇所でそれを使っています。

``` cpp: src/sample_thread.cpp

void SampleThread::_UpdateData(void)
{
    std::lock_guard<std::mutex> lock(_main_mutex);
    _counter++;
    _value += _counter;

    if(_counter > 100) _counter = 0;
}

void SampleThread::ResetValue(const int &value)
{
    std::lock_guard<std::mutex> lock(_main_mutex);
    _value = value;
}
```

上の二つの関数で共通するlock_guardが今説明したメモリへのアクセスを順番待ちする関数になります。

ただしここで気をつけたいのが、ずっとこのlockをし続けることができてしまう、と言うことです。

つまり一つのスレッドが常時メモリへのアクセスをし続けていることになり、他のスレッドがアクセスできず順番待ち=処理が止まってしまう、と言うことが起こります。

なのでmutexを使うときの鉄則として、lock_guardを呼び出す範囲は値を書き換える部分のみにしましょう。

``` cpp: src/sample_thread.cpp
    std::lock_guard<std::mutex> lock(_main_mutex);
```

## 最後に

と言うわけで今回マルチスレッドのクラスを作ったので紹介してみました。

今後はシングルトンのクラスや複数箇所でこのスレッドにアクセスする方法などもまとめていきたいと思います。

ではでは。
