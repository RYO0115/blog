# 自作のMatrixクラスを作ってみたよ

## 概要

最近、仕事の関係でMATLAB/Simulinkを使うことが多くなったのですが、色々とイライラが募っています。

特に、最近S-Functionを使ったC++のソースコードをSimulinkに移植する作業をしているのですが、

もうイライラがとまりません。

関数の使い方はよくわからないし、何より、行列が使えない!! MATLABなのに!!

(本当は使い方があるのかもしれませんが、すいません。僕にはレベルが高すぎました。。。)


というわけで、ひとまずオレオレMatrixライブラリをC++で作ろうと思い立ったわけです。

そこで、とりあえず逆行列や行列式を求めたりするので、そのロジックがあっているのかを確認するため、

今回はMatrixクラスをPythonで組んでみたいと思います。


## 具体的に今回やったこと

今回やったことは主に以下の４つです。

1. 二次元行列を格納できる
2. 行列式を計算できる
3. 逆行列を計算できる
4. 加減などの計算も符号演算できるようにする

それぞれ、コードとともに具体的に説明していきます。

### コードの中身

まず、今回のソースコードはJupyterにて作成しました。

もし、実際のPythonコードに使いたいという方は、Jupyterからクラス部分のみをコピーアンドペーストしていただければ

大丈夫です。

importするのも、pythonインストール時にデフォルトでインストールされている,math, copyのみになります。


#### 二次元行列の格納

``` Python

import math
import copy
class Matrix:
    def __init__(self, row, col):
        self.row = row
        self.col = col
        self.data = [ 0 for i in range(row * col)]
        self.error = False

    def ResetMatrix(self, row, col):
        self.row = row
        self.col = col
        self.error = False

    def InitializeWithIdentity(self):
        for row in range(self.row):
            for col in range(self.col):
                if row == col:
                    self.data[ row, col] = 1
                    #self.data[row * self.col + col] = 1
                else:
                    self.data[ row, col] = 0
                    #self.data[row * self.col + col] = 0
```

```Python
class Matrix:
    def __init__(self, row, col):
        self.row = row
        self.col = col
        self.data = [ 0 for i in range(row * col)]
        self.error = False
```

まず、データの格納部分です。
今回は色々と考えたのですが、とりあえずデータの格納は一次元の配列にし、このクラスを呼び出した際に、行、列を宣言するようにしました。

ここについては[ 1, 2, 3, 4] ではなく[[ 1, 2],[ 3, 4]]することも考えたのですが、ひとまずはこの形でも問題ないことが確認できたので、

簡単なこちらにしています。

なので、行数などを変えたいときは**ResetMatrix()**関数を、単位行列にしたいときは**InitializeWithIdentity()**関数を呼び出すようにしています。


ちなみに、InitializeWithIdentity()内のここの部分、

``` Python
                if row == col:
                    self.data[ row, col] = 1
                    #self.data[row * self.col + col] = 1
                else:
                    self.data[ row, col] = 0
                    #self.data[row * self.col + col] = 0

```

あえてコメントアウトしていますが、本来、コメントアウトされた書き方でないとエラーになります。

しかし、今回、メソッドのオーバーライドをしているので、この記法ができるようになっています。

この方法についてはまた後ほど説明します。(実はこれが今回このライブラリを作ってみようと思ったモチベーションの一つだったりします。)


### 行列式の計算

``` Python
    def Det(self):
        temp = copy.deepcopy(self)
        det = 1.0
        for i in range(self.row):
            if self[i,i] == 0:
                for j in range(i,self.row):
                    for k in range(self.row):
                        buf = self[i,k]
                        self[i,k] = self[j,k]
                        self[j,k] = buf
                    det *= -1.0
                    break


        for i in range(self.row):
            for j in range(self.row):
                if i < j:
                    buf = self[j,i] / self[i,i]
                    for k in range(self.row):
                        self[j,k] = self[j,k] - self[i,k] * buf

        for i in range(self.row):
            det *= self[i,i]
        self = copy.deepcopy(temp)
        return(det)
```

次に行列式の計算です。

行列式の計算については、[こちらのサイト](https://hikalium.com/lectures/c/0004.html)を参考に、上三角行列への変形 -> 対角成分の積を取る

方法にしています。

ここは、ただ数学の方程式をプログラムに書き起こしただけなので、深くは説明しません。


### 逆行列の計算

``` Python

    def Inverse(self, inv_type="GaussJordan"):

        if inv_type == "GaussJordan":
            return(self.GaussJordanInverse())



    def GaussJordanInverse(self):
        det     = self.Det()
        ans     = Matrix(self.row, self.col)
        temp    = copy.deepcopy(self)
        ans.InitializeWithIdentity()
        if self.row != self.col or det == 0:
            ans.error = False
            return(ans)

        for i in range(self.row):
            buf = 1.0 / self[i,i]
            for j in range(self.row):
                self[i,j] = self[i,j] * buf
                ans[i,j] = ans[i,j] * buf

            for j in range(self.row):
                if i != j:
                    buf = self[j,i]
                    for k in range(self.row):
                        self[j,k] = self[j,k] - self[i,k] * buf
                        ans[j,k] = ans[j,k] - ans[i,k] * buf

        self = copy.deepcopy(temp)

        return(ans)

```

続いて逆行列の計算です。

本当はLU法もやるつもりだったのですが、とりあえず[GaussJordan法](
https://ja.wikipedia.org/wiki/%E3%82%AC%E3%82%A6%E3%82%B9%E3%81%AE%E6%B6%88%E5%8E%BB%E6%B3%95
)が形になったので、このブログにはここまでしか掲載しません。

[Git](https://github.com/RYO0115/python_tester.git)には追々追加予定です。

こちらも同じくこのサイトの数式を書き起こしただけになります。


### 加減などの計算も符号演算できるようにする

最後に今回、演算子をオーバーロードしてみたので、その説明をしたいと思います。

(正直、この記事はこれを書きたいがためにやったと言っても過言ではないです。)

そもそも演算子のオーバーロードとは何か、わからないですよね。

簡単な例を説明すると、例えば以下のような行列があったとします。

    A=
    |	1.000	2.000	3.000	|
    |	4.000	5.000	6.000	|
    |	7.000	8.000	9.000	|
    B=
    |	1.000	2.000	3.000	|
    |	4.000	5.000	6.000	|
    |	0.000	0.000	0.000	|

例えばこの２つの行列を足し合わせたい。そう思ったときに、どうするか。

もし、数式で書くなら次のようになります。

    ANS = A + B

ANSが3行3列のA,B,２つの行列の和の答えです。

しかし、実際にプログラムを書かれた方ならわかるかと思いますが、自作のクラスではこの書き方では計算ができません。

なぜか。

それは、計算の方法がわからないからです。

なので、だいたいの場合、Add関数なるものを作成し、

``` Python
ANS = Add(A,B)
```

このような形で、Add関数内で各要素にアクセスし、その結果を返り値としてANSに格納するという方法をとっていました。

しかし、不思議には思いませんか？　ではなぜ、numpyなどのライブラリは行列で+などの演算子が使えるのか。

それは、この+などの演算子を使った場合の処理(特殊メソッド)をオーバーロード、つまり書き換えられるからです。


Pythonの公式ドキュメントには[こちら](https://docs.python.org/ja/3/reference/datamodel.html#special-method-names)
のように、様々な特殊メソッドが公開しており、これと同じ関数名でクラス内で宣言するだけで、簡単にオーバーロードをすることができます。

では、簡単な例を見ていきましょう。

``` python
    def Add(self, other):
        scalar = self.CheckScalar(other)
        ans = Matrix(self.row, self.col)

        if scalar==False:
            if self.row != other.row or self.col != other.col:
                ans.error = True
                return(ans)

            for row in range(self.row):
                for col in range(self.col):
                    ans[row, col] = self[row, col] + other[row, col]

        else:
            for row in range(self.row):
                for col in range(self.col):
                    ans[row, col] = self[ row, col] + other
        return(ans)

    def __add__(self, other):
        return(self.Add(other))
```

今回のMatrixクラスに入っている足し算のメソッドを紹介します。

まずは先に実行例を示します。
``` python
    a.data = [1,2,3,4,5,6,7,8,9]
    b.data = [1,2,3,4,5,6,0,0,0]
    print("a=\n",a)
    print("b=\n",b)
    ans = a+b
    print("ans=\n",ans)
```

この実行結果がこちらになります。

    a=
    |	1.000	2.000	3.000	|
    |	4.000	5.000	6.000	|
    |	7.000	8.000	9.000	|

    b=
     |	1.000	2.000	3.000	|
    |	4.000	5.000	6.000	|
    |	0.000	0.000	0.000	|

    c=
     |	2.000	4.000	6.000	|
    |	8.000	10.000	12.000	|
    |	7.000	8.000	9.000	|

と、このように、def __add__(self, other):　という形で関数を再度宣言して上げることで、+演算子でも計算ができるようになりました。

### その他

今回、いくつか特殊メソッドをオーバーロードしてみたので、その紹介もしてみたいとおもいます。
オーバーロードしたのは、以下の３つです。

1. \__str__(self)
2. \__getitem__(self, key)
3. \__setitem__(self, key, value)


#### 1. \__str__(self)

これはstr()という文字列に変換する関数でこのクラスが呼ばれたときに何を返すのかを決める特殊メソッドです。

なので、簡単な例でいうと、print()関数がいい例でしょう。

例えば、
``` python
a = 1
print(a)
```

としたときに、出力はどうなるかというと、もちろん下のようになります。

    1

ですが、今回のように複数のデータがあるクラス、しかも今回は行列のクラスなので、

できれば行ごとに開業してきれいに表示してほしい。

というわけで、オーバーロードしてみました。

``` python
    def __str__(self):
        if self.error == True:
            return("No Matrix")

        s = ""
        for row in range(self.row):
            s += "|"
            for col in range(self.col):
                s += "\t" + "{:.3f}".format(self[row,col])
            s += "\t|\n"
        return(s)
```

この関数を加えたことで、行列が下のようにprint()できるようになります。


    |	1.000	2.000	3.000	|
    |	4.000	5.000	6.000	|
    |	7.000	8.000	9.000	|

    |	1.000	2.000	3.000	|
    |	4.000	5.000	6.000	|
    |	0.000	0.000	0.000	|

    |	2.000	4.000	6.000	|
    |	8.000	10.000	12.000	|
    |	7.000	8.000	9.000	|



#### 2. \__getitem__(self, key), 3. \__setitem__(self, key, value)

この２つについてはほぼ役割は同じなのでまとめて説明します。

この２つの特殊メソッドの役割は、以下のような場面です。

``` python
a       = [1,2,3]
b       = a[0]
a[1]    = 4
```

このコードの2行目が\__getitem__, 3行目が\__setitem__を使っています。

つまり、

要素を指定して値を取り出すときにどの要素を取り出すかを決めるのが**\__getitem__**,

要素を指定して値を書き込むときにどの要素に書き込むかを決めるのが**\__setitem__**

になります。

``` python
    def __setitem__(self, key, value):
        if len(key) == 2:
            row = key[0]
            col = key[1]
        elif len(key) == 1:
            row = 0
            col = key[0]
        else:
            return(None)
        if row>=self.row or row < 0 or col >= self.col or col < 0:
            return(None)

        self.data[ row * self.col + col ] = value


    def __getitem__(self, key):
        if len(key) == 2:
            row = key[0]
            col = key[1]
        elif len(key) == 1:
            row = 0
            col = key[0]
        else:
            return(None)
        if row>=self.row or row < 0 or col >= self.col or col < 0:
            return(None)

        #print("row:",row," col:",col)
        return(self.data[ row * self.col + col])
```

この２つを宣言することで、クラス内やクラス外で行列の要素にアクセスする際、

    A[0,0]

のような形でアクセスすることができるようになります。

### 最後に

というわけで、今回は行列のクラスを試しにつくってみました。

今まで、手を出してなかった特殊メソッドのオーバーロードを試すことができたので、かなりいい勉強になりました。

今後もクラスを作る際には積極的に作って行きたいとおもいます。

ではでは。