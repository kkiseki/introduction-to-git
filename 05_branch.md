# ひとりで使う - ブランチを知る

さて、今までの内容で、あなたはもう作業ディレクトリの変更内容を自由自在に stage, unsatage したり commit したりできるようになりましたね？「なってない」というひとは先に進む前に前回までの内容を復習してください。

さて、ここで、再度 [VCS入門](https://github.com/masaru-b-cl/introduction-to-vcs/blob/master/2.power-of-vcs.md) を紐解いてみましょう。

> 1. 履歴を残す

> VCSでできることの一番大きなものは「作業履歴を残すこと」です。これは、言い換えると「誰が」「いつ」「何を」したかが残るということになります。
> 
> (中略)
> 
> 履歴が残ると何ができるのか。大まかには次の3つができるようになります。
> 
> * 参照
> * 再現
> * 分岐
> 
> (中略)
>
> (2) 再現
> 
> VCSを使うと、履歴を参照するだけでなく、その時点の状態を再現することも可能です。つまり、動かなくなったとしても正常に動作していた状態に巻き戻せる、ということです。
>
> この特徴を利用して、動作しなくなる一つ手前に戻り作業をやり直すといったことや、バグが発生する時点の成果物を、他のメンバーに確認してもらう、といったことが可能になります。
> 
> (3) 分岐
>
> 過去の状態を再現した上で、そこから別の変更を加えることで、履歴を分岐することもできます。つまり、複数のアプローチにより、作業を行うことができるということです。
> 
> このことから、動作しなくなる手前の状態を元に別の変更を加えていき、それぞれのパターンをメンバーに評価してもらう、といったこともできるようになります。

なるほど。で、この「再現」と「分岐」をするのに使えるのが、「ブランチ」です。ブランチっていってもブレックファストとランチの中間のbrunch のほうではなくて、branch、つまり枝とか分枝とかを意味するほうのことです。分岐を管理するのに branch が使えるというのは直感的ですね。

とはいえ、イメージはわかったけどじゃあ実際の「再現」とか「分岐」ってなに？って感じもあります。このあたりは、習うよりも慣れろな感じがあるので、実際に手を動かしてみましょう。まずは Git におけるブランチとは何者なのか、というのから見ていきましょう。

## Git におけるブランチはただの「ポインタ」である

あっ！ ブラウザを閉じないでください！ この「ポインタ」は C 言語とかのポインタとはまっっっっっったく関係のない言葉です！ 怖くないですよ！ 「ポインタ」を和訳してみてください。「指し示すもの」ですね。では、なにがなにを指し示しているのでしょうか。

それは、「あるブランチ」が、「あるコミットオブジェクト」を指し示しているのです。まずは実際を見てみましょう。

現在リポジトリに存在しているブランチの一覧を表示するには、「git branch」でできます。

    $ git branch
    * master

はい、master というブランチが表示されました。つまり、今このリポジトリには「master」というブランチが存在していることが見れたわけです。以前からちょくちょく出てきている「On master branch」のことですね。このmasterというブランチも、なんらかのコミットオブジェクトを指し示しているはずです。ではそれを見てみましょう。

    $ git log --graph --pretty=format:'%s %d'

はい、git log --graph に --pretty=format:'%s %d' というオプションを渡してみました。これは、コミットの履歴を'%s %d'というフォーマットで表示してね、という意味です。'%s' はコミットメッセージ、'%d' がブランチを意味します。

    * 動物ファイルを animals ディレクトリに移動  (HEAD, master)
    * ファイル名を英語に変更 
    * 豚の鳴き声は要らないので削除 
    * 犬と豚の鳴き声を追加 
    * 猫の鳴き声を nyan から mew に変更 
    * 猫の鳴き声を管理するファイルを作成
    
こんなログが表示されたのではないでしょうか。これを見てみると、「HEAD」と「master」というのがありますね。HEADはとりあえず今は置いておいて、「動物ファイルを animals ディレクトリに移動」というコミットの横に master と表示されています。これは、 master ブランチがこのコミットを指している、という意味です。

うーん、なんか、「で？それがなんなん？」って感じですね。でも、今はよくわかんなくていいです。とにかく今は、「master というブランチが存在していて、その master ブランチが「動物ファイルを animals ディレクトリに移動」というコミットオブジェクトを指している」、と理解してくれれば良いです。

ではここで恒例の、唐突な git tips です。

    $ git config --global alias.graph "log --graph --date-order --all --pretty=format:'%h %Cred%d %Cgreen%ad %Cblue%cn %Creset%s' --date=short"

と打ち込んでおきましょう。すると、次回から

    $ git graph

とするときれいにフォーマットされたログが見れるようになります。

## ブランチを新しく作ってみよう

さて、今は master が最新のコミットを指していることがわかりました。では、新しくブランチを作ってみましょう。

    $ git branch my_first_branch

はい、これで、「my_first_branch」というブランチが作られました。ちゃんと作られているかどうか、確認してみましょう。

    $ git branch
    * master
      my_first_branch

さっきは存在しなかった my_first_branch が出来上がってますね。

ところで、git branch でブランチ一覧を表示したとき、 master のよこに \* がついているのがわかるでしょうか。これは、今自分が選択しているブランチが master であることを示しています。選択しているブランチ？ よくわからないですね。今はよくわからないままでいいです。

git graph の表示も確認しておきましょう。

``` 
* 5fe17c7  (HEAD, my_first_branch, master) 2013-05-04 Shinpei Maruyama 動物ファイルを animals ディレクトリに移動
* bb8f610  2013-05-04 Shinpei Maruyama ファイル名を英語に変更
* 8faaa1f  2013-05-04 Shinpei Maruyama 豚の鳴き声は要らないので削除
* 66c681b  2013-05-04 Shinpei Maruyama 犬と豚の鳴き声を追加
* 66346b5  2013-05-04 Shinpei Maruyama 猫の鳴き声を nyan から mew に変更
* 33028c1  2013-05-03 Shinpei Maruyama 猫の鳴き声を管理するファイルを作成
```

my_first_branch というブランチが出来ていて、masterと同じく最新のコミットオブジェクトを指しているのが見て取れるとおもいます。

さて、よくわからない感じのことが増えてきましたが、もう少しでその謎が解けます。頑張ってやっていきましょう。

## コミットしてみよう

よくわらかないことをわかるために、とにかく、なにかコミットしてみましょう。bow.txt の中身が "wan" のままなので、これを "bow" に書き換えてみましょうか。

書き換えて、stage して、コミット。コミットメッセージは「犬ファイルの中身も英語に合わせた」あたりにしておきましょうか。

できましたか？

それでは、この状態で、コミットログがどうなっているか確認してみましょう。

```
$ git graph
* c3ef974  (HEAD, master) 2013-05-05 Shinpei Maruyama 犬ファイルの中身も英語に合わせた
* 5fe17c7  (my_first_branch) 2013-05-04 Shinpei Maruyama 動物ファイルを animals ディレクトリに移動
* bb8f610  2013-05-04 Shinpei Maruyama ファイル名を英語に変更
* 8faaa1f  2013-05-04 Shinpei Maruyama 豚の鳴き声は要らないので削除
* 66c681b  2013-05-04 Shinpei Maruyama 犬と豚の鳴き声を追加
* 66346b5  2013-05-04 Shinpei Maruyama 猫の鳴き声を nyan から mew に変更
* 33028c1  2013-05-03 Shinpei Maruyama 猫の鳴き声を管理するファイルを作成
``` 

おっ。変化がありましたね。さっきまでは、my_first_branch も master も「動物ファイルを animals ディレクトリに移動」というコミットを指し示していましたが、master ブランチを選択した状態で新しく commit を行ったら、新しいコミットオブジェクトが生まれて、masterはその新しいコミットオブジェクトを指すようになりました。しかし、my_first_branch のほうは依然として「動物ファイルを animals ディレクトリに移動」を指し示したままです。

これはどういうことでしょうか？ それを知るために、コミットが行われたときにリポジトリに何が起こっているのか、もう一度確認してみましょう。

## コミットオブジェクト、みたび

さて、コミットが行われると、コミットオブジェクトが作成されるのでしたね。そして、そのコミットオブジェクトは、「親コミットオブジェクトはどれなのか」という情報と、「親コミットオブジェクトからどのような変更を行ったのか」という情報を持っているのでした。ここまではいいですか？

では、新しくコミットを行ったとき、「親コミットオブジェクトがどれなのか」という情報はどこから引っ張ってくるのでしょうか？以前は、「直前のコミット」とぼかした言い方をしていましたね。

察しの良いひとは薄々感付いているかもしれませんね。そうです。実は、「親コミットオブジェクト」になるのは、「コミットするときに選択されていたブランチが指し示しているコミットオブジェクト」なのです。

コミットが行われると、Git さんは「選択されているブランチが指し示すコミットオブジェクト」を親として、新しいコミットオブジェクトを作成します。その後、Git さんは「選択されているブランチ」が、この新しくできたコミットオブジェクトを指し示すように更新します。

そのような動きを頭にいれたあと、再度さきほどの git graph の結果を眺めてください。master ブランチを選択している状態で新しいコミットを行ったため、新しいコミットオブジェクトが生まれ、選択していたmasterブランチがその新しいコミットオブジェクトを指し示していることが見て取れると思います。

そして、このタイミングで HEAD なぞにも答えておきましょう。実はこの HEAD というのは特殊なブランチ(ポインタ)で、「今選択しているブランチがどれなのか」という情報を保持しているのです。今、HEAD は master と同じところにありますね。つまり、master が選択されている、ということです。

## 選択中のブランチを切り替えてみる

では、選択されているブランチを切り替えてみましょう。ブランチを切り替えるには git checkout です。

    $ git checkout my_first_branch
    Switched to branch 'my_first_branch'

うん、「my_first_branch っていうブランチに切り替えたよ」と言われましたね。 git graph で確認してみます。

```
$ git graph
* c3ef974  (master) 2013-05-05 Shinpei Maruyama 犬ファイルの中身も英語に合わせた
* 5fe17c7  (HEAD, my_first_branch) 2013-05-04 Shinpei Maruyama 動物ファイルを animals ディレクトリに移動
* bb8f610  2013-05-04 Shinpei Maruyama ファイル名を英語に変更
* 8faaa1f  2013-05-04 Shinpei Maruyama 豚の鳴き声は要らないので削除
* 66c681b  2013-05-04 Shinpei Maruyama 犬と豚の鳴き声を追加
* 66346b5  2013-05-04 Shinpei Maruyama 猫の鳴き声を nyan から mew に変更
* 33028c1  2013-05-03 Shinpei Maruyama 猫の鳴き声を管理するファイルを作成
```

見事に HEAD が移動してmy_first_branch と同じところを指すようになりました。

さて、今、選択したブランチを切り替えましたね？ そして、「ブランチ」は「コミットオブジェクトを指し示している」のでした。ということは、選択しているブランチが変われば、「選択しているコミットオブジェクト」も変わっているはずです。

これはどういうことでしょうか？ ここで、以前「コミットオブジェクトさえあれば、Git さんはコミットがされたときの状態を復元できる」と言ったのが生きてきます。

あるブランチを checkout すると、Gitさんはふたつのことを行います。

1. HEADを指定したブランチに切り替える
2. 作業ディレクトリの内容を、そのブランチが指し示すコミットのときの状態に復元する

です。1 のほうはもう見たので、2 のほうを見てみましょう。my_first_branchが指しているコミットしたときというのは、どんな状態なんでしたっけ？ ログを見ると、「ファイルを animals ディレクトリに移動したけど、bow.txt の中身は英語になっていない」状態なんだな、というのがわかります。では作業ディレクトリの中身を見てみてください。実際、bow.txt の中身が「wan」に戻っているのが確認できると思います。

さあ、これで [VCS 入門](https://github.com/masaru-b-cl/introduction-to-vcs) に書かれていた、VCSの履歴を残す能力によりできるみっつのことのうち、「参照」と「復元」を実際に行ってみました。残るは「分岐」です。

## my_first_branch でもコミットを行う

現在選択しているブランチは my_first_branch、そしてそのブランチが指し示しているのは「動物ファイルを animals ディレクトリに移動」のコミットでした。

では、ここから、なにか変更をしてみましょう。そうですね、気まぐれディレクターが、「牛の鳴き声も追加したいなー」などと言っています。じゃあ、ここから牛の鳴き声を追加してみましょう。

"mow" と書かれた mow.txt を animals ディレクトリの中に作成し、git add で stage、「牛の鳴き声を追加」というコミットメッセージでコミットしてみてください。

OKですか？

では、 git graph でコミット後の様子を見てみましょう。

```
* bab8d49  (HEAD, my_first_branch) 2013-05-05 Shinpei Maruyama 牛の鳴き声を追加
| * c3ef974  (master) 2013-05-05 Shinpei Maruyama 犬ファイルの中身も英語に合わせた
|/  
* 5fe17c7  2013-05-04 Shinpei Maruyama 動物ファイルを animals ディレクトリに移動
* bb8f610  2013-05-04 Shinpei Maruyama ファイル名を英語に変更
* 8faaa1f  2013-05-04 Shinpei Maruyama 豚の鳴き声は要らないので削除
* 66c681b  2013-05-04 Shinpei Maruyama 犬と豚の鳴き声を追加
* 66346b5  2013-05-04 Shinpei Maruyama 猫の鳴き声を nyan から mew に変更
* 33028c1  2013-05-03 Shinpei Maruyama 猫の鳴き声を管理するファイルを作成
```

* コミット前に my_first_branch が指し示していた「動物ファイルを animals ディレクトリに移動」を親コミットオブジェクトに持つ、「牛の鳴き声を追加」というコミットが作成されていること
* my_first_branchが指し示すコミットが、新しく作成されたコミットになっていること

を確認してください。

これで、「動物ファイルを animals ディレクトリに移動」を親コミットオブジェクトに持つ、ふたつの異なったコミットが生まれたことになります。これが、「分岐」です。さて、しつこいようですが、今どういう状態になっているか、整理しますよ。

まず、起点として、「動物ファイルを animals ディレクトリに移動」というコミットがあります。そして、そこからふたつの異なるコミットが生まれています。

その片方、master ブランチが指し示しているコミットには、「bow.txtの中身を書き換えた」という情報が詰まっていますが、「牛の鳴き声を追加した」という情報は詰まっていません。

もう一方、my_first_branch が指し示しているコミットには、「animals/mow.txtを追加した」という情報が詰まっていますが、「犬ファイルの中身を書き換えた」という情報は詰まっていません。

実際にその様子を見てみましょう。

    $ git checkout master

として、master ブランチを checkout します。これにより、

1. 選択しているブランチが master になり
2. 犬ファイルの中身も英語に合わせた、というコミットのときの状態が作業ディレクトリに復元される

のでしたね。では、作業ディレクトリの中身を覗いてみてください。このコミットには「犬の鳴き声を書き換えた」という情報は詰まっているけれど、「牛の鳴き声を追加した」という情報が詰まっていないので、「bow.txt」の中身は "bow" に書き変わっているけれど、作業ディレクトリに「mow.txt」は存在しないはずです。

次に、

    $ git checkout my_first_branch

として、my_first_branch を checkout してみましょう。これで、

1. 選択しているブランチが my_first_branch になり
2. 牛の鳴き声を追加、というコミットのときの状態が作業ディレクトリに復元される

のでした。このコミットには「牛の鳴き声を追加した」は詰まっているけれど、「犬の鳴き声を書き換えた」は詰まっていません。作業ディレクトリの中を覗いてみると、mow.txt は追加されているけれど、bow.txt の中身は "wan" のままなのが見て取れますね。

こんな感じで、ブランチを活用することで、「ある時点のコミット」を親にもつ、複数の異なるコミットを作り出すことで、「分岐」が行えるわけです。

この例だと、こんな分岐の何がうれしいのか、って感じがないではないですね。それは次回「マージ」について見てみてから、嬉しい例をいくつか紹介しようと思います。

では、今回のまとめです。

* Git のブランチは「コミットオブジェクトを指し示すポインター」である
* リポジトリには最初から「master」というブランチが存在している。
* HEADという特殊なブランチも存在していて、このブランチは「現在選択しているブランチ」を指している。
* "git branch new_branch_name" とすることで、現在選択しているブランチが指しているのと同じコミットオブジェクトを指す、新しいブランチを new_bnrach_name という名前で作成することができる
* "git checkout some_branch_name" とすると、"some_branch_name"という名前のが指しているコミットの状態が作業ディレクトリ内に復元され、そのブランチが選択された状態になる
* 新しくコミットを行うと、現在選択しているブランチが指しているコミットオブジェクトを親コミットオブジェクトとした新しいコミットが作られ、選択しているブランチが指すコミットオブジェクトはその新しく作られたコミットオブジェクトとなる。
* このようなブランチを活用することで、「あるときの状態の復元」や「そこから分岐したコミット」を作り出すことができる。

少しずつ内容が高度になってきましたね。でも、最初から丁寧に読めば、きっと理解できると思います。がんばってください。

次回はマージについて見て行く予定です。