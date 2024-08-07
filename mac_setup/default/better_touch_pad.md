# Better Touch Toolsをつかったトラックパッドのカスタム

## 概要

MacBookを買う理由の50%以上を占める(と勝手に思っている)トラックパッドを拡張していきます。

ここで使うのは[Better Touch Tool](https://folivora.ai/)です。

こちらはトラックパッド上の操作を指の本数からタップする位置までを区別して、それにさまざまな機能を割り当てることができるツールです。

今回はこのアプリを使って拡張していきます。

ちなみに10年ほど前は無料だったのですが、MacユーザーをBTT漬けにして逃れられなくなったところで有料化されました。(2年で$10, 買い切りで$22です)

今回の設定の多くはこちらを使うので、ここは諦めて課金しましょう。

画像

## Better Touch Toolsでの設定追加方法

Better Touch Toolへの設定追加は以下のようにします。

今回はトラックパッドの設定を例に説明します。

まずはウィンドウの上方真ん中にあるプルダウンをクリックして、"トラックパッド"を選択します。

するとこういう画面が出てきますので真ん中にあるプラスボタンを押します。

するとどういう入力をした際の設定をしますか、ということで最初にTriggerを聞かれます。

(下の画像ではデフォルトの状態を見せるためにあえてmagic mouseの画面を貼り付けています)

画像

試しに一本の指でタップを押すと次のような画像のようになります。

画像

今度は横に出てきたプラスボタンを押してもらうとそのtrigger(操作)した時、どういう操作をするのかを設定します。

プルダウンを見てもらうとキーボードショートカットを始めmacの機能、スペース/デスクトップの移動などさまざまな機能を割り当てられます。

では今回自分が割り当てた機能を便利度の高いものから順番に紹介していきます。

## BetterTouchToolsでの設定前に


#### トラックパッドのデフォルト設定の一部を無効化(一部デフォルトで無効にされている機能を有効化)


今回、デフォルトで設定されているジェスチャーに別の機能を割り当てるので先に無効化します。

こちらもシステム設定からトラックパッドを開きます。

その他のジェスチャに移動して以下のように設定してもらいます。

画像

## おすすめ設定

### タブ移動 (3本指左右スワイプ)

ブラウザで複数のタブを開いているとき別のタブに移動したい時がありますよね？皆さんはどうしていますか？

おそらくほとんどの人はマウスカーソルを移動させてクリックしていると思います。

もしくはCommand + tabで切り替えるというレアな方もいるかもしれません。

その作業、トラックパッドでやりませんか？

ということでBetter Touch Toolで早速割り振っていきます。

昔から三本指で左右にスワイプを割り当てていたので今回もそれを割り振っていきます。

こちらはMacの使い心地に合わせるために動作は スワイプの方向と反対に移動するように設定しています。

設定内容としては、triggerは"3本指で左(右)にスワイプ"を選択し、アクションは"キーボードショートカット"で"Control + Tab (右の場合はControl + Shift + Tab)"を設定します。

これでマウスカーソルを動かさずに3本指で左右にスワイプするだけで、ブラウザのタブを移動できます。

(VSCodeのソースコード間の移動もできます!!)

これでネットサーフィンの効率は相当効率化されます!!

### 新しいタブ作成 (１本指でトラックパッドの左下をタップ)

ブラウザで新しいタブを作りたいとき、一々カーソルを右上に持っていくのはめんどくさくないですか？

そのめんどくさい作業、トラックパッドに割り当てましょう。

ということでこちらもBetter Touch Toolで割り当てていきます。

triggerで"1本の指で左下をタップ"を選択し、アクションでこれまた"キーボードショートカット"で"Command + T"を設定してください。

これでブラウジング中に左下をタップすると新しいタブがひらけます。

### ページを閉じる (四本指クリックで開いているタブやウィンドウを閉じる)

ネットサーフィンをしている時に煩わしく感じることの一つに見終わったタブを閉じる作業です。

これもトラックパッドに割り当てちゃいます。

triggerで"4本の指でクリック"を選択し、アクションでこれまた"キーボードショートカット"で"Command + W"を設定してください。

これでブラウジング中に4本指クリックでタブ、ウィンドウを閉じることができます。

こちら昔は3本指が多かったのですが、新しいトラックパッドは感度が上がったのか、タブ移動の際に誤爆が多かったので4本に変更しました。

### ブラウザページの更新 (1本指でトラックパッドの右下をタップ) 

新しいタブ作成とほぼ同じです。

triggerで"1本の指で右下をタップ"を選択し、アクションでこれまた"キーボードショートカット"で"Command + R"を設定してください。

これでブラウザで見ているページを手軽に更新できます。

### デスクトップの移動 (4本指でスワイプしてデスクトップを移動する)

Macのデフォルト設定でも移動できますが、3本指のスワイプをタブ移動に割り当てたのでこれを4本に当てます。

triggerで"4本の指で左(右)にスワイプ"を選択し、アクションで"スペース/デスクトップ間の移動"から"スペースを右(左)に移動"を設定してください。

### 単語を辞書で調べる (3本指でタップして単語を辞書で調べる)

Macを使う際に意外と便利な機能がこの単語検索機能です。

自分は英語の記事や論文を読むことが度々あるので、この機能は重宝しています。

どういう機能かというと、例えばブラウザで分からない英単語があれば、こんな感じでその場で単語の意味を教えてくれるのです。

デフォルトでもトラックパッドから起動できるのですが、1本指の長押しと少し使い勝手が微妙です。なので今回は空いている三本指のタップにこの機能を割り当てていきます。

triggerで"3本の指でタップ"を選択し、アクションで"macOS機能"から"カーソル下の探索語"を設定してください。

## 最後に

トラックパッドのカスタムは奥が深いです。

またいい設定を見つけたらまた追記していきたいと思います。

