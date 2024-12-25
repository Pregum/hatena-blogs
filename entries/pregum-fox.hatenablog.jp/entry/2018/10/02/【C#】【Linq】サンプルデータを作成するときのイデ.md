---
Title: 【C#】【Linq】サンプルデータを作成するときのイディオム
Category:
- C#
- イディオム
Date: 2018-10-02T22:30:43+09:00
URL: https://pregum-fox.hatenablog.jp/entry/2018/10/02/%E3%80%90C%23%E3%80%91%E3%80%90Linq%E3%80%91%E3%82%B5%E3%83%B3%E3%83%97%E3%83%AB%E3%83%87%E3%83%BC%E3%82%BF%E3%82%92%E4%BD%9C%E6%88%90%E3%81%99%E3%82%8B%E3%81%A8%E3%81%8D%E3%81%AE%E3%82%A4%E3%83%87
EditURL: https://blog.hatena.ne.jp/pregum_fox/pregum-fox.hatenablog.jp/atom/entry/10257846132643967692
---

自分がよくサンプルデータを作成するときのイディオムについて紹介します。  
<span style="font-size: 80%">ほとんどLINQの紹介になるかと思いますが。。。</span>  
用意するサンプルデータは、0～9の合計10個のintの配列とします。  

* 構文
* 個人的に感じるメリット・デメリット
* 使用例  
のような感じで書いていきます。  



<!-- more -->


  

##### 1.  for(int i = 0; i < 10; i++){  }
##### 2.  Enumerable.Range(0, 10);

---

### 1. for(int i = 0; i < 10; i++){  }
これは、大体のプログラミング言語には用意されているfor文での作成方法ですね。  
#### メリット・デメリット
メリットとしては、

 * usingディレクティブ(最初にusing System;とか入れる文のこと)が必要ない
 * 他のプログラミング言語を知っている人ならとっつきやすい  

デメリットとしては、

 * LINQに比べると冗長になりやすい
 * 配列等のコレクションを先に宣言しておかないといけない

何はともあれ一番ノーマルな感じなサンプルデータの作成方法だと思います。

#### 使用例

```cs
int[] collection = new int[10];
for( int i = 0; i < collection.Length; i++){
    collection[i] = i;
}
```
---
### 2. Enumerable.Range(0, 10); 
これを使用するには、ファイルの最初に```using System.Linq; ```と ```using System.Collections.Generic;```のusingディレクティブが必要になります。  
#### メリット・デメリット
メリットとしては、

 * サクッと書ける
 * Select()やWhere()等の拡張メソッドでさらに複雑なデータを生成できる
 * チョットドヤガオデキル?

デメリットとしては、

 * LINQという概念を理解しなければいけない

LINQについては学習コストはそれなりにありますがC#を今後も使っていくのであれば、覚えておいて損はないと断言できます。  
むしろほかの言語でもその考え方は活用できますので、ぜひLINQを使ってみてください。

#### 使用例

```cs
using System.Linq;
using System.Collections.Generic;

int[] secondColllection = Enumerable.Range(0, 10).ToArray();
```

自分が一番使用しているイディオムですね。短くて済むのはいいですね。

---
3つ目もありましたが、ほとんど2番目とかぶっていたので、代わりに別のサンプルデータの作成方法を書いて終わりにしたいと思います。

サンプルデータとしては、  
```cs
public class SampleData
{
    public int Number { get; set; }
    public bool Flag { get; set; }

    public SampleData(int number, bool flag)
    {
        this.Number = number;
        this.Flag = flag;
    }
}

```

 * 上記のSampleDataクラスの配列を5個作成する

```cs
// for文を用いた方法
SampleData[] sampleDatas = new SampleData[5];
for (int i = 0; i < sampleDatas.Length; i++)
{
    sampleDatas[i] = new SampleData(i, i % 2 == 0);
}

// LINQを用いた方法
SampleData[] sampleDatasByLinq = Enumerable.Range(0, 5)
                                           .Select(x => new SampleData(x, x % 2 == 0))
                                           .ToArray();
```

 * 上記のSampleDataクラス5個の配列から各１つプロパティをまとめた配列を作成する

```cs
// for文を用いた方法
bool[] flags = new bool[5];
for (int i = 0; i < flags.Length; i++)
{
    flags[i] = sampleDatas[i].Flag;
}

// LINQを用いた方法
bool[] flagsByLinq = sampleDatas.Select(x => x.Flag).ToArray();
```

 * 上記のSampleDataクラス5個から1プロパティがtrueの場合のみ、取得したクラスの配列を作成する


```cs
// 5. SampleDataクラス5個から1プロパティがtrueの場合のみ、取得したクラスの配列を作成する
// for文を用いた方法
List<SampleData> sampleDatasOnlyTrue = new List<SampleData>();
for (int i = 0; i < sampleDatas.Length; i++)
{
    if (sampleDatas[i].Flag == true)
    {
        sampleDatasOnlyTrue.Add(sampleDatas[i]);
    }
}
SampleData[] sampOnlyTrue = new SampleData[sampleDatasOnlyTrue.Count];
for (int i = 0; i < sampOnlyTrue.Length; i++)
{
    sampOnlyTrue[i] = sampleDatasOnlyTrue[i];
}

// LINQを用いた方法
SampleData[] sampOnlyTrueByLinq = sampleDatas.Where(x => x.Flag).ToArray();
```

上記のソースは以下のGistに記載しています。



[For文とLinqでのサンプルデータ作成方法](https://gist.github.com/9cb497bc2799c9339d366e11e4662c92)




以上です。


