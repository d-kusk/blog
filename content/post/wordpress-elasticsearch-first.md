+++
date = "2017-04-02T11:23:29+09:00"
tags = ["WordPress","Elasticsearch"]
categories = ["クリエイティブ"]
description = "2017年1月のBench京都でプラグインを使ってWordPressとElasticsearchを連携させる話をしてきた"
title = "WordBench京都でWordPressとElasticsearchを連携させる話をしてきた"
author = "Daisuke Konishi"

+++

1月のWordBench京都は、WordPressに色んなサービスを連携するというテーマで行われたのですが、その中でElasticsearchと連携する話をしました。っていう話をそういえば書いてないわと思って今書いてるわけです。

スライドは<a href="https://speakerdeck.com/dkonishi/wordpressnielasticsearchwolian-xi-sitarapu-falsejian-suo-gajia-su-sitahua">SpeakerDeck</a>であげてます。

## はじめに
もともとElasticsearchを触っていたわけでも無いし、普段から検索周りのものを作ってるってわけではなかったんですが、岡本さんが登壇するとき結構な割合で名前を聞いていて、ちょっと気になるなーと思っていました。
ある日こんな感じのツイートがきたのでLikeしたら登壇するところまでいきました。ノリって怖いｗ

<blockquote class="twitter-tweet" data-lang="ja"><p lang="ja" dir="ltr">「サービス連携祭り」とかいってWordPressと他のサービスを組み合わせる話ばっかりするWordBenchとかやってみようかな。 <a href="https://twitter.com/hashtag/fb?src=hash">#fb</a><br>kintoneとかMauticとかElasticsearchとか</p>&mdash; hidetaka okamoto (@motchi0214) <a href="https://twitter.com/motchi0214/status/789839230285459456">2016年10月22日</a></blockquote>
<script async src="//platform.twitter.com/widgets.js" charset="utf-8"></script>

<blockquote class="twitter-tweet" data-conversation="none" data-lang="ja"><p lang="ja" dir="ltr">なんかリアクションしてくれる人いたから、1月か2月にやります。</p>&mdash; hidetaka okamoto (@motchi0214) <a href="https://twitter.com/motchi0214/status/789840451368955905">2016年10月22日</a></blockquote>
<script async src="//platform.twitter.com/widgets.js" charset="utf-8"></script>

<blockquote class="twitter-tweet" data-conversation="none" data-lang="ja"><p lang="ja" dir="ltr">とりあえず <a href="https://twitter.com/misumi_takuma">@misumi_takuma</a> さんと <a href="https://twitter.com/skd_nw">@skd_nw</a> さんなんかやりません？</p>&mdash; hidetaka okamoto (@motchi0214) <a href="https://twitter.com/motchi0214/status/789841524083216386">2016年10月22日</a></blockquote>
<script async src="//platform.twitter.com/widgets.js" charset="utf-8"></script>

## Elasticsearchを調べるところから
<a href="https://www.elastic.co/jp/products/elasticsearch">Elasticsearchのサイト</a>を見たりいくつかのサイトを見ていると、Elastic社の出しているサービス郡の1つで、**オープンソースの分散型RESTful検索/分析エンジン**というのが分かりました。

他にも、以下の特徴が目に止まりました。

* 多くの型をサポートしてる
* 検索が早くて精度が高い
* JSONみたいな感じでデータを扱える
* 位置情報なんかのデータも扱える
* <a href="https://www.elastic.co/jp/products/kibana" target="_blank">Kibana</a>という同社開発のツールを使うことでElasticsearchで扱っているデータを可視化できる

GithubのリポジトリやDockerのコンテナの検索なんかで使われてるみたいです。


## WordPressと連携させる

### そもそもなぜ連携？
そもそもなんでWordPressと連携させるのか、検索ってもともとあるじゃんと思っていたのですが、どうやら**WordPressの検索がLikeしか対応していない**らしく…。

Like検索というのが、

> 検索条件が完全一致しない対象を、一定のルールのもとで抽出する検索方法のこと
> - weblio

結構曖昧な結果になっちゃうみたいです。  
なので、Like検索だと

* あんまり精度が良くない
* DBのインデックスが使えないからDBをフルスキャンするため時間がかかり、データ数によっては負荷がすごい
* 条件幾つか指定してAND検索したいときに対応できない

なのでElasticsearchを連携させて、検索はそっちでやっちゃおうという感じです。


### Amazon Elasticsearch Serviceを使って連携させる

実際に連携させる上で、Elasticsearchはどうしたらいいんだろうと思って調べたのですが、**自分で立てるか、サービスを使うか** って感じらしいです。  

一度ローカルでやるときにVagrantでやりかけたのですが、少しやってみて自分で立てるのはその後がしんどそうだなと思ってサービスを使う方にシフトしました。

サービスは、以下のどちらかを使うのが良いらしく、今回はAmazon Elasticsearch Searviceを使ってみました。

* <a href="https://aws.amazon.com/jp/elasticsearch-service/" target="_blank">Amazon Elasticsearch Service</a>
* <a href="https://www.elastic.co/jp/cloud" target="_blank">ElasticCloud</a>

連携自体はやってくれるプラグインがあったのでそれを利用しました。

<a href="https://ja.wordpress.org/plugins/wp-simple-elasticsearch/" target="_blank">WP Simple Elasticsearch — WordPress Plugins</a>

連携方法は<a href="http://dev.classmethod.jp/server-side/elasticsearch/wordpress-plugin-wp-elasticsearch/" target="_blank">クラスメソッドさんの記事</a>を参考にしました。

手順はざっくり以下。細かいやり方はちゃんと覚えていないので、上のサイトを見るか調べてください。

1. AES側で使えるように準備
2. プラグインのインストールして有効化
3. 設定画面から1.で作成したものの情報を入力
4. 変更を保存

基本的にはタイトルと本文の情報をElasticsearchに渡すらしいのですが、指定すればカスタムフィールドの情報も渡すことが出来るらしいです。

### 実際に連携してみると…
導入前と導入後で同じ時間帯に同じキーワードで検索を書けたところ、**表示まで5秒かかっていたものが2秒ほどに短縮**する事が出来ました。めっちゃ早いです。


## やってみて
全然深くは触れていないので顔見知り程度なレベルなのですが、実際やってみて得られる恩恵は結構ありそうなので、Elasticsearchはちょっと触ってみてもいいなという気になりました。
