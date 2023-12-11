---
Title: 【WPF】【MVVM】INotifyCollectionChangedを使ったサンプルを書いてみた
Category:
- C#
- MVVM
- WPF
Date: 2019-05-02T08:14:11+09:00
URL: https://pregum-fox.hatenablog.jp/entry/2019/05/02/%E3%80%90WPF%E3%80%91%E3%80%90MVVM%E3%80%91INotifyCollectionChanged%E3%82%92%E4%BD%BF%E3%81%A3%E3%81%9F%E3%82%B5%E3%83%B3%E3%83%97%E3%83%AB%E3%82%92%E6%9B%B8%E3%81%84%E3%81%A6%E3%81%BF%E3%81%9F
EditURL: https://blog.hatena.ne.jp/pregum_fox/pregum-fox.hatenablog.jp/atom/entry/17680117127101480864
---

こんにちは。 pregum_foxです。

巷では、.NET Core や .NET Standardが話題になっていますが、相変わらず.NET frameworkを触っています。

今回は、タイトルの通りINotifyCollectionChangedのサンプルを書いてみました。

とはいっても、実質ObservableCollectionをほぼそのまま使っているだけなんですけどね。（汗

とはいっても自分自身いざ書こうとするとド忘れしてググりながら書いていたので

確認がてらサンプルを書いてみました。

ただサンプルを書いてみるだけだと、理解できないと思いコレクションの操作処理のログを画面に表示する機能も追加してみました。


<!-- more -->



目次です。

[:contents]

<a name="section1"></a>
結論としては、通知だけ行いたい場合そのままのObservableCollectionをインスタンス化 [^1]するのが一番記述が少なく済み不具合が起きづらいかなと書いてて思いました。

ただ今回みたいにコレクション操作履歴を表示したい等の処理を追加したい時はObservableCollectionを継承してInsertItem()メソッドなどをoverrideすると良い感じに拡張できるかとおもいます。

[^1]:  ジェネリッククラスを```ObservableCollection<SampleModel>```等具象型にすること。

今回作成したサンプルのクラス図は以下の通りです。

<figure class="figure-image figure-image-fotolife" title="クラス図">[f:id:pregum_fox:20190502021757p:plain]<figcaption>クラス図</figcaption></figure>

いろいろ書いていますが、重要なのはViewModelはModelの影に徹しているということです。
開発リソースの配分にもよるかと思いますが、基本としてはViewModelはModelの影になっていることが望ましいと私は思っています。その方がエラー処理など煩雑な処理はModel側ですべて処理ができViewModelはその状態を受け取ってViewに通知する役割に専念でき肥大化しないからです。そして処理の見通しが良くなるかと思っています。

シーケンス図は以下の通りです。

<figure class="figure-image figure-image-fotolife" title="シーケンス図">[f:id:pregum_fox:20190502024954p:plain]<figcaption>シーケンス図</figcaption></figure>

処理の流れはMainWindowクラスでボタンをクリックされたらModelのメソッドを呼び出しViewModelに変更を通知します。ViewModelは受け取り、内部のプロパティなどの情報を更新し、Viewへ通知します。

本当ならMainWindowからボタンをクリックされた時は、ViewModelが呼び出された方が望ましいと思いますが今回はINotifyCollectionChangedのサンプルということでMainWindowから直接Modelのメソッドを読んでいます。

----
<a name="section2"></a>

###INotifyCollectionChangedインターフェースについて

この記事をご覧になっている方には蛇足かと思いますが、一応、INotifyCollectionChangedインターフェースについて説明しておきます。

> 項目が追加、削除された場合やリスト全体がクリアされた場合など、動的な変更をリスナーに通知します。

[msdn](https://docs.microsoft.com/ja-jp/dotnet/api/system.collections.specialized.inotifycollectionchanged?redirectedfrom=MSDN&view=netframework-4.8#events)から引用

上記だけでは、いまいちピンとこなかったのですが、実装方法はObservableCollectionのソースコードが参考になりました。

[https://referencesource.microsoft.com/#System/compmod/system/collections/objectmodel/observablecollection.cs:embed:cite]

INotifryCollectionChangedインターフェース内のNotifyCollectionChangedEventHandlerイベントの第2引数のNotifyCollectionChangedEventArgsクラス内は以下のようになっています。

<figure class="figure-image figure-image-fotolife" title="NotifyCollectionChangedEventArgsクラスの内容">[f:id:pregum_fox:20190502075139p:plain]<figcaption>NotifyCollectionChangedEventArgsクラスの内容</figcaption></figure>

赤枠の一番上のNotifyCollectionChangedAction列挙型が追加(Add)、削除(Remove)、置換(Replace)、移動(Move)、リセット(Reset)が通知することができます。
以下の4つは古いオブジェクトリストと新しいオブジェクトリスト、それぞれの変更されたIndexの情報を持っています。これらを用いて変更されたり追加された項目を通知することができます。

なのでINotifyCollectionChangedインターフェースを実装することで、追加、削除、置換、移動、リセット等の処理の内容と変更された項目をCollectionChangedイベントを用いて通知する機能が実装できることがわかります。

----
<a name="section3"></a>

### サンプルプログラム

サンプルを以下に示します。

<figure class="figure-image figure-image-fotolife" title="サンプル画像">[f:id:pregum_fox:20190502050927p:plain]<figcaption>サンプル画像</figcaption></figure>

左の列がModelから伝搬されてきたリストで、2列目が操作履歴を表示するテキストボックスです。

右端は選択された項目に対して操作を行うボタンです。

作成、削除、置換、移動、リセット通知も書いてみました。

#### 作成処理(Add)

<figure class="figure-image figure-image-fotolife" title="作成処理">[f:id:pregum_fox:20190502051458g:plain]<figcaption>作成処理</figcaption></figure>

一番最後の項目に新しい項目が追加されていることがわかるかと思います。

この処理では、SampleModelsでNotifyCollectionChangedAction.Addが呼ばれSampleViewModelsに通知されます。

#### 削除処理(RemoveAt)

<figure class="figure-image figure-image-fotolife" title="削除処理">[f:id:pregum_fox:20190502051617g:plain]<figcaption>削除処理</figcaption></figure>

選択されている項目が削除されていることがわかるかと思います。

削除後、操作履歴に「-1の要素がコレクションの~」という表示がありますが、これは削除後選択されている項目がないため-1がViewからViewModelに未選択という意味で通知されているためです。

#### 置換処理(Replace)

<figure class="figure-image figure-image-fotolife" title="置換処理">[f:id:pregum_fox:20190502051701g:plain]<figcaption>置換処理</figcaption></figure>

置換処理は以下のコードを見てもらえばわかるのですが、オブジェクトを丸ごと入れ替えています。

```cs
// 置換処理の通知を受けたSampleViewModelsクラスの処理
var ram = new Random();
int index = this._sampleViewModels.SelectedIndex;
this._sampleModels[index] = new SampleModel( "new太郎" + ram.Next(20), ram.Next(30, 70), (FoxGenusKind)ram.Next(0, 6));
```

上記のようにすることで、SampleModelsクラスが継承しているObservableCollectionのSetItemメソッドが呼ばれます。

SetItemメソッドが呼ばれることでOnCollectionChanged()メソッドが呼ばれ、SampleViewModelsに通知されます。

上記のようにオブジェクトを丸ごと入れ替えず、プロパティのみ変更した場合、SetItem()メソッドが呼ばれずSampleViewModelsに通知されないため、注意してください。

一部のプロパティの変更をViewModelsに通知したい場合は、各項目のModelのPropertyChangedイベントをViewModelから受け取ってその変更をViewModelsへPropertyChangedで通知する必要があります。(今回のサンプルはINotifyCollectionChangedのみの対応です。)

以下のような流れになります。

<figure class="figure-image figure-image-fotolife" title="１項目のプロパティの変更通知シーケンス図">[f:id:pregum_fox:20190502055921p:plain]<figcaption>１項目のプロパティの変更通知シーケンス図</figcaption></figure>

#### 移動処理(Move)

<figure class="figure-image figure-image-fotolife" title="移動処理">[f:id:pregum_fox:20190502063947g:plain]<figcaption>移動処理</figcaption></figure>

見づらいですが、選択されている項目が上下に移動していることがわかるかと思います。

#### リセット処理(Clear)

<figure class="figure-image figure-image-fotolife" title="リセット処理">[f:id:pregum_fox:20190502061451g:plain]<figcaption>リセット処理</figcaption></figure>

リセット処理後、操作対象のリストがすべてなくなっていることがわかるかと思います。


サンプルプログラムは以下のURLに置いていますので、興味のある方はご自由に見てください。


[https://github.com/Pregum/hatena_blog/tree/master/ObservableCollectionImplementSample:embed:cite]


----
<a name="section4"></a>
### ObservableCollectionのプロパティ通知方法について

今回はModelのコレクションクラスはObservableCollectionクラスを継承していますが、ObservableCollectionクラスのPropertyChangedイベントはprotectedで記述されているため、SampleViewModelsでSampleModelsの通知を受け取るためにはキャストする必要があります。

今回はSampleModelsから操作履歴と選択されている項目のIndexの変更通知を受け取るために使用しています。

```cs
// SampleViewModelsのコンストラクタ内の処理
((INotifyPropertyChanged)this._models).PropertyChanged += this.SampleViewModels_PropertyChanged;
```

<figure class="figure-image figure-image-fotolife" title="ObservableCollectionクラスの内容">[f:id:pregum_fox:20190502065535p:plain]<figcaption>ObservableCollectionクラスの内容</figcaption></figure>

----
<a name="section5"></a>
### ObservableCollectionから呼び出したメソッドと発火されるCollectionChangedイベントのアクションの種類の対応表

ObservableCollectionクラスは外部のクラスから直接CollectionChangedイベントを発火させることはできませんが、Add, Remove, Replace, Move, Resetに対応するメソッドは存在していますので、その対応表を以下に示します。

ObservableCollectionクラスの呼び出されたメソッドと発火されるCollectionChangedイベントのアクションの種類の対応表

| 呼び出すメソッド | 内部で呼ばれるメソッド | 発火されるアクションの種類 |
|----|----|----|
| Add() | InsertItem() | Add |
| Insert() ※今回は使用していません。 | InsertItem() | Add |
| RemoveAt() | RemoveItem() | Remove |
| Remove() ※今回は使用していません。 | RemoveItem()| Remove |
| this[index] ※インデクサ | SetItem()| Replace |
| Move() | MoveItem()| Move |
| Clear() | ClearItems() | Reset |

※ Move()メソッドは[ObserableCollection.cs](https://referencesource.microsoft.com/#System/compmod/system/collections/objectmodel/observablecollection.cs,d97337991c3e5d3a,references)に記述されていますが、Add()メソッド等は[Collection<T\>](https://referencesource.microsoft.com/#mscorlib/system/collections/objectmodel/collection.cs,5c61cc24de82f7e7,references)クラスで実装されているメソッドなので、[Collection<T\>](https://referencesource.microsoft.com/#mscorlib/system/collections/objectmodel/collection.cs,5c61cc24de82f7e7,references)クラスを見ないとわからないです。

また、CollectionChangedイベントのみ着目していますが、一緒にコレクションの個数のプロパティ(Count)やインデックスを表すプロパティ(Item[])も変更されていますが、ObservableCollectionを使用しているのであれば特に気にしなくて良いと思います。サンプルプログラムでもCountやItem[]の変更通知は受け取っても何も処理を行っていません。

### 補足

一度理解すれば、PropertyChangedイベントの実装と同じような流れなのですんなり理解できるかと思います。

サンプルプログラムでは、各単体のオブジェクトの通知は実装していないため、実際に使用する際には実装する必要があるかと思いますが、そちらはたくさん？他の方のサンプルがあると思いますのでそちらをご覧ください。

また今回は単体の変更通知のみなので実装していませんが、AddRange()等で複数の変更通知を行いたい場合は通知を受け取った時foreach等でループを回して処理を行う必要があります。

読んでいただき、ありがとうございました。

----
<a name="section_url"></a>
### 参考サイト

* [ジェネリック ++C++; // 未確認飛行 C](https://ufcpp.net/study/csharp/sp2_generics.html)
* [INotifyCollectionChanged Interface](https://docs.microsoft.com/ja-jp/dotnet/api/system.collections.specialized.inotifycollectionchanged?redirectedfrom=MSDN&view=netframework-4.8#events)
* [ObservableCollection reference source ](https://referencesource.microsoft.com/#System/compmod/system/collections/objectmodel/observablecollection.cs)
* [ObservableCollection PropertyChanged event](https://stackoverflow.com/questions/1003344/observablecollection-propertychanged-event)
* [MVVMのModelにまつわる誤解](http://ugaya40.hateblo.jp/entry/model-mistake)


