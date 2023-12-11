---
Title: 【C#】クラス内の項目の書いていく順番を調べてみた
Category:
- C#
- コーディングスタイル
Date: 2019-04-22T04:02:10+09:00
URL: https://pregum-fox.hatenablog.jp/entry/2019/04/22/%E3%80%90C%23%E3%80%91%E3%82%AF%E3%83%A9%E3%82%B9%E5%86%85%E3%81%AE%E9%A0%85%E7%9B%AE%E3%81%AE%E6%9B%B8%E3%81%84%E3%81%A6%E3%81%84%E3%81%8F%E9%A0%86%E7%95%AA%E3%82%92%E8%AA%BF%E3%81%B9%E3%81%A6
EditURL: https://blog.hatena.ne.jp/pregum_fox/pregum-fox.hatenablog.jp/atom/entry/17680117127068555108
---

こんにちは。 pregum_foxです。

結論は<a href="#section1">こちら</a>です

C#で開発されている方以外でも関係ある事だと思いますが、

皆さんはクラス内のフィールドやプロパティ、コンストラクタなどの項目をどの順番で書いていますか？

私自身、フィールドとそれを使用しているプロパティ、コンストラクタその後はメソッドを順番に書いていています。

しかしイベントや非同期処理が入ってくると、どの順番で使用する少し考えこんでしまうことがあります。

このような感じで考えてしまうことがあるので、最初に順番が決まって入れば、時間が短縮できそうだなと思い、この記事を書いた次第です。



<!-- more -->



まずはmsdnに項目の順番についての規約があるか探してみました。

調べたものは以下の通りです。

* StyleCop[^1]  の項目の順番のルール

* StyleCopAnalyzer[^2] の項目の順番のルール

[^1]: c#のコーディング内容をチェックするツール

[^2]: [https://github.com/DotNetAnalyzers/StyleCopAnalyzers](https://github.com/DotNetAnalyzers/StyleCopAnalyzers)



それでは調査結果を以下に記載します。

## 調査結果

残念ながら公式のmsdnにはコーディング規約はありましたが私が調べた限りでは、項目の順番に関する文書はありませんでした。

しかし、[こちら](https://stackoverflow.com/questions/150479/order-of-items-in-classes-fields-properties-constructors-methods)のstackoverflowにそれらしき質問がありました。

リンク先の一番上の回答に記載されていた内容では、

以下のような順番になってました。

1. 定数フィールド
1. フィールド
1. コンストラクタ
1. ファイナライザ（デストラクタ）
1. デリゲート
1. イベント
1. 列挙型
1. インタフェース（インタフェース実装）
1. プロパティ
1. インデクサー
1. メソッド
1. 構造体
1. クラス

その各項目でアクセス順に並べます

1. public 
1. internal
1. protected internal
1. protected
1. private

そのアクセスグループ内でstatic、非staticの順に並べます。

1. static 
1. 非static

これだけです。

virtualについては特に気にしないみたいですね。

また上記のサイトには記述がありませんでしたが、クラス外についての順番についてはStyleCopのルールに記載されていました。

[SA1201：ElementsMustAppearInTheCorrectOrder](https://sites.google.com/site/manbou404/stylecop#SA12xx)

上記のサイトによるとファイル内の順番は以下の通りです。

1. 外部エイリアス
1. usingディレクティブ 
1. 名前空間
1. デリゲート
1. 列挙型
1. インターフェース
1. 構造体
1. クラス


これだけでも概ね項目の順番を決めることができそうですが、StyleCopの後継であるStyleCopAnalyzerでのルールではどうなっているか気になったのでそちらも調べてみました。

--- 

項目の順番のルールについては、StyleCopのものとほとんど同じようですがいくつか増えていました。

[SA1216：Using static directives should be placed at the correct location.](https://github.com/DotNetAnalyzers/StyleCopAnalyzers/blob/master/documentation/SA1216.md)

上記のルールはC#6.0の機能の1つのusing staticとC#7.0の機能の1つのusing aliasディレクティブについてのルールですね。

何とか自分なりに解読した所、using ディレクティブ, using static ディレクティブ, using aliasディレクティブの順番で記載してねだということだそうです。

```cs

using System;                    // usingディレクティブ
using static System.Console;     // using staticディレクティブ
using Except = System.Exception; // using aliasディレクティブ

```

[SA1217：Using static directives should be ordered alphabetically](https://github.com/DotNetAnalyzers/StyleCopAnalyzers/blob/master/documentation/SA1217.md)

これについては、SA1216の各using ディレクティブの中でもアルファベット順に書いてねということみたいですね。


## 結果

<h4 id="section1">ファイル内の順番</h4>

1. 外部エイリアス
1. usingディレクティブ
1. 名前空間
1. デリゲート
1. 列挙型
1. インターフェース
1. 構造体
1. クラス

<h4 id="section2">クラス、構造体、インターフェース内の順番</h4>

1. 定数フィールド
1. フィールド
1. コンストラクタ
1. ファイナライザ (デストラクタ)
1. デリゲート
1. イベント
1. 列挙型
1. インターフェース (インターフェースの実装)
1. プロパティ
1. インデクサー
1. メソッド
1. 構造体
1. クラス

<h4 id="section3">アクセス修飾子の順番</h4>

1. public
1. internal
1. protected internal
1. protected
1. private

<h4 id="section4">各アクセスの項目内のstatic, 非staticで分ける</h4>

1. static 
1. 非static

ということになりました。

誰かの参考になれば幸いです。


## 雑感

Roslynを使えばコマンド一つで並べ替えられるものが作れるのかな？

ちなみにmsdnのコーディング規約は[こちら](https://docs.microsoft.com/ja-jp/dotnet/csharp/programming-guide/inside-a-program/coding-conventions)です。


## 参考サイト

[StyleCopのルール内容](https://sites.google.com/site/manbou404/stylecop#SA12xx)

[StyleCopAnalyzerの使い方](https://opcdiary.net/?p=30738)

[StyleCopAnalyzerのルール内容](https://dotnetanalyzers.github.io/StyleCopAnalyzers/)

