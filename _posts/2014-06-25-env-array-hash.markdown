---
layout: post
title: "環境変数にハッシュの配列を入れてみた"
date: 2014-06-25 21:37:22 +0900
comments: true
category: blog
tags: ruby rails
gist: true
---
[環境変数にハッシュを入れたいと思って、ちょっと考えてみたら LTSV を使えば良いということに気づいた](https://twitter.com/znz/status/481691675136245760)ので、
実装してみました。
実際には単独のハッシュではなくハッシュの配列がほしかったので、
連続する数値を末尾に付けて複数受け取れるようにしています。

<!--more-->

## 実装

キーワード引数を使っているので、
以下の実装のままだと ruby 2.0.0 以降必須です。
必要なら適当に変更して使ってください。

<amp-gist
 data-gistid="849166c048a2117c341d"
 data-file="env_ltsv_each.rb"
 layout="fixed-height"
 height="901">
 <a href="https://gist.github.com/znz/849166c048a2117c341d#file-env_ltsv_each-rb">env_ltsv_each.rb</a>
</amp-gist>

## 詳細

`String#scrub!` は 2.1.0 以降か
[string-scrub gem](https://rubygems.org/gems/string-scrub)
が必要なのでデフォルトでは呼ばないようにしています。

`Hash` だと取り出すのが面倒だったので、
`OpenStruct` を使ってみました。
メソッド名として問題がある場合は
`h['send']` のように普通の `Hash` のように取り出せます。

添字をまわすのに `succ` を使っているので `FOO_A`, `FOO_B`, ... のような使い方も出来ると思います。
