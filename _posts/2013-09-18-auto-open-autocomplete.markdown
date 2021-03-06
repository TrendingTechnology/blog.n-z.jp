---
layout: post
title: "jQuery UI Autocomplete を自動で開く"
date: 2013-09-18 15:43
comments: true
category: blog
tags: rails jquery-ui coffeescript javascript html5
---
Google カレンダーの時刻入力のように combobox 的な入力欄で、
デフォルトは選択肢から選んで、
選択肢にないときは直接入力も出来るようにしたいと思いました。

いくつかの方法を試した結果、
jQuery UI の Autocomplete に少しコードを足したら
希望通りの挙動に出来ました。

<!--more-->

## HTML5 の datalist と autocomplete

最初は HTML5 の datalist 要素を試しました。
`simple_form` gem を使っているので、
以下のように書きました。

```haml
= f.input :drink, input_html: { autocomplete: "on", list: "drink_list" }
%datalist#drink_list
  %options(value="不要")
  %options(value="水")
  %options(value="お茶")
  %options(value="コーヒー")
```

この方法の問題点としては、
IE は IE10 (以降?) しか対応していなかったり、
Mobile Safari も対応してなさそうだったりして
対応環境が微妙だったのと、
入力欄にフォーカスだけで自動で開くという挙動が出来なくて、
矢印の上下で開く必要があったので、
諦めました。

## Bootstrap Typeahead

挙動としては
HTML5 の datalist の方法や
jQuery UI Autocomplete と似ていたのですが、
Bootstrap 3 では外されているという話があったり、
別途新しい typeahead.js が開発されているという話があったりして、
先にある程度 jQuery UI Autocomplete を試していたこともあり、
別途新しいライブラリを入れるなどの深追いはせずに
jQuery UI Autocomplete を使うことにしました。

## jQuery UI Autocomplete

`jquery-ui-rails` gem を使っていて、
必要なものだけ CSS や JS を require していたので、
まず `app/assets/stylesheets/application.css` に

```
 *= require jquery.ui.autocomplete
```

を追加しました。

次に `app/assets/javascripts/autocomplete.js.coffee` として
以下のような内容で作成しました。

```coffeescript
#= require jquery.ui.autocomplete
jQuery ->
  drink_list = [
    "不要"
    "水"
    "お茶"
    "コーヒー"
  ]
  $("#somemodel_drink").autocomplete
    source: drink_list
    autoFocus: true
    delay: 0
    minLength: 0
  .on "focus", ->
    $("#somemodel_drink").autocomplete("search", "")
```

## 余談

今回の話とは直接は関係ないのですが、
ファイルを細かく分割すると管理しやすくなるのは良いのですが、
他のライブラリの中のファイル名と重ならないようにしないと
`bootstrap.js.coffee` の中で `#= require bootstrap` しても
自分自身を `require` してしまって空っぽになって悩むことに
なるので注意が必要でした。
