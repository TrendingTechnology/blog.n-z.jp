---
layout: post
title: "ターミナルでの文字幅を調べる"
date: 2014-10-07 20:10:20 +0900
comments: true
category: blog
tags: shell zsh
---
[マルチバイト文字列の幅を 2 としてカウントする](http://uwabami.junkhub.org/log/20141007.html#p01 "マルチバイト文字列の幅を 2 としてカウントする")
という話を見て、
端末に実際の幅を問い合わせてみればいいのではないかと思って試してみました。

<!--more-->

## 実行例

こんな感じでできました。
zsh と bash で動作確認しています。
端末は
[iTerm2](http://iterm2.com/ "iTerm2")
で動作確認しています。

```console
% echo -ne '日本語\e[6n'; read -d';' a; read -dR a; printf '\e[M\r'; echo $[a-1]
6
% echo -ne 'abc日本語\e[6n'; read -d';' a; read -dR a; printf '\e[M\r'; echo $[a-1]
9
```

## スクリプト化

実行ファイルにするならこんな感じになります。

```shell
#!/bin/bash
echo -ne "$*"'\e[6n'; read -d';' a; read -dR a; printf '\e[M\r'; echo $[a-1]
```

## 解説

実装としては実際に表示して、カーソル位置を端末に問い合わせて、表示を消去して、桁を取り出して 1 引いています。

エスケープシーケンスなどに関しては、
[対応制御シーケンス](http://ttssh2.sourceforge.jp/manual/ja/about/ctrlseq.html "対応制御シーケンス")
を参考にしてください。

1. まず文字列を表示しています。
2. `CSI Ps n` (DSR) を使ってカーソルの位置を報告させています。
3. `CSI r ; c R` という応答のうち、 `;` までを捨てて、 `c` だけ変数 `a` に取り込んでいます。
4. `CSI Ps M` (DL) でカーソル行に表示された内容を消去しています。
5. `CR` で行頭にカーソルを戻しています。
6. カーソル位置なので 文字数+1 になっているので、1 引いて表示しています。

## 追記

実装の説明でわかると思いますが、1行を超える長さは調べられません。がんばるなら適当に区切って文字ごととかで調べてください。

実行開始時にカーソルは行頭にしておく必要があり、実行後にカーソル行は消えます。
スクリプト中に入れるには使いにくいと思います。

半分ネタなので、あんまりがんばっていません。
