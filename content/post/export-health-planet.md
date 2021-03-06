+++
title = "Health Planet ヘルスプラネットのデータをエクスポートするスクリプトを作ってみた"
description = "タニタ社から提供されているHealth Planet ヘルスプラネットのAPIからデータを取得するスクリプトを作ってみました"
date = 2020-04-03T01:35:12+09:00
image = ""
categories = ["クリエイティブ"]
tags = ["Python"]
+++


タニタ社から提供されているHealth Planet ヘルスプラネットのAPIからデータを取得するスクリプトを作ってみました。


少し前にタニタ社の体組織系を頂いて乗り始めてるんですが、累積データを取得して他で使えないかなーと思っていたので、とりあえず取るところを作りました。

実は過去に定期的に取得してS3に保存するバッチをServerless Frameworkでやろうとして挫折したんですが、今回はローカルで叩く想定のスクリプトを作成してみました。

[d-kusk/export-health-planet - github](https://github.com/d-kusk/export-health-planet)

詳しくはREADMEに書いてますが、Python 3.8.2で作成しました。  
ただ、特に3.8系独特のメソッドを使ったわけではないので、もう少し下のバージョンでも動くのかもしれません。(保証はしません)

「ちょっとしたスクリプトを書くならやっぱりPythonだよね」  
数秒後にしょうもないとこでハマって時間使いました。

たまには書いて感覚覚えとこう…

## スクリプトについて
タニタ社から提供されているHealth Planet ヘルスプラネットのAPIからデータを取得するスクリプトを作りました。

Health Planetの開発者ページでアプリを登録、認証後APIを2つ叩いてアクセストークンを取得していること前提です。  
そこもスクリプトを作りたかったんですが、エンコードの Windows-31J (MS932)  を解決するのが面倒だったのと、そもそもCLIでのアクセスを想定していないのかHTMLにフォームが載って返ってくるので、色々解決するの面倒と思って実際に計測データを取得するところだけ作りました。

### 標準ライブラリだけでも作れそう
いつものノリで requests モジュールを使いましたが、 urlib で取得することもできるかもしれないので、気が向いたら書き換えます。


### 引数指定で期間を調整できるようにしたい
今は実施日を元にその月のデータを取得するようにしていますが、期間指定をできるようにしたいなという気がします。


## レスポンス整形が大変だった
APIからのレスポンスが、計測日順に指定した項目ごとに分割して返ってきます。
なので、わかりやすいように計測日ベースで直してあげる処理を入れました。

一応できたんですが、後の使いやすさを考えるとCSV形式で、1計測1行で整形する方が良いんじゃないかという気も。(飽きたのでJSON止まりです)

## 今後
気が向いたら調整します
