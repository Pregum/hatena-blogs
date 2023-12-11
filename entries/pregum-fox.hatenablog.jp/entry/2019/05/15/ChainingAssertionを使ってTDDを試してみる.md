---
Title: ChainingAssertionを使ってTDDを試してみる
URL: https://pregum-fox.hatenablog.jp/entry/2019/05/15/ChainingAssertion%E3%82%92%E4%BD%BF%E3%81%A3%E3%81%A6TDD%E3%82%92%E8%A9%A6%E3%81%97%E3%81%A6%E3%81%BF%E3%82%8B
EditURL: https://blog.hatena.ne.jp/pregum_fox/pregum-fox.hatenablog.jp/atom/entry/17680117127124787176
Draft: true
---

こんにちは。 pregum_foxです。

今回はChainingAssertionというテストコードの実装を楽にしてくれるライブラリを使用してTDDを行っていきたいと思います。

では目次です。

[:contents]


### ChainingAssertionについて

ChainingAssertionとは、ReactiveProperty(WPFや様々なプラットフォームでMVVMパターンのサポートを行ってくれる)やUniRx(UnityのためのReactive Extensions)を作成されている@neuecc様作のテストコード用のライブラリです。
現在(2019/05/15)Visual StudioのNugetパッケージマネージャーを見る限りですが、[.NetFramework版](https://archive.codeplex.com/?p=chainingassertion)( MSTest / NUnit / MbUnit / xUnit.net )があるみたいです。.Net Core版も検索時表示されていましたが、他の方がフォークして作成されているようです。



ChainingAssertionのgithubのURLを以下に記載します。


[https://github.com/neuecc/ChainingAssertion:embed:cite]


ChainingAssertionについては上記に書いた通りテストコードの実装を楽にしてくれるというライブラリなのですが、では具体的にはどう楽になるのかということはあまり自分でも説明できないまま使用していた為、ここでまとめさせてもらいます。

ChainingAssertion
