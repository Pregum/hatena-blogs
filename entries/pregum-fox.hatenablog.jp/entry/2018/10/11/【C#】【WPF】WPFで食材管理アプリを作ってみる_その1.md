---
Title: 【C#】【WPF】WPFで食材管理アプリを作ってみる その1
Category:
- C#
- WPF
- 食材管理アプリ
Date: 2018-10-11T00:29:21+09:00
URL: https://pregum-fox.hatenablog.jp/entry/2018/10/11/%E3%80%90C%23%E3%80%91%E3%80%90WPF%E3%80%91WPF%E3%81%A7%E9%A3%9F%E6%9D%90%E7%AE%A1%E7%90%86%E3%82%A2%E3%83%97%E3%83%AA%E3%82%92%E4%BD%9C%E3%81%A3%E3%81%A6%E3%81%BF%E3%82%8B_%E3%81%9D%E3%81%AE1
EditURL: https://blog.hatena.ne.jp/pregum_fox/pregum-fox.hatenablog.jp/atom/entry/10257846132648826288
---

<p>最近はjavascript, ruby, python, phpなどなどwebに関係する言語が流行している今日ですが、今回は、今せっせと作っている食材管理アプリについて記事を書いていこうと思います。</p>
<p> </p>
<p>とはいっても、サーバーサイドの知識やレンタルサーバーを借りることは一切無しでデスクトップアプリケーションで作っていこうと思います。<span style="text-decoration: line-through; font-size: 80%;">(時代遅れとか言わないで。。。)</span></p>
<p> </p>
<p><!-- more --></p>
<p> </p>
<p> </p>
<p>開発環境は以下に記載します。</p>
<p>Visual Studio Community 2017</p>
<p>C#.NET/WPF</p>
<p>.Net framework 4.6</p>
<p> </p>
<p>あと、使用するNuGetパッケージ関係では今の所これらを予定しています。今後増えていくと思います。</p>
<p>Reactive Property 3.6.0</p>
<p>System.Reactive 4.1.1</p>
<p>Prism 6.3.0</p>
<p>Newtonsoft.Json 11.0.2</p>
<p> </p>
<p>今回は、プロジェクトを作りNuGetパッケージを追加するところまで書いていきます。</p>
<p> </p>
<p>まずは、Visual Studio を起動し、プロジェクトを作成します。</p>
<p><img class="hatena-fotolife" title="f:id:pregum_fox:20181011001123p:plain" src="https://cdn-ak.f.st-hatena.com/images/fotolife/p/pregum_fox/20181011/20181011001123.png" alt="f:id:pregum_fox:20181011001123p:plain" /></p>
<p> </p>
<p>その後、実際にプロジェクトが開かれたら、F5を押下しデバック環境で実行します。</p>
<p>なにもないウインドウが表示されたら成功です。</p>
<p><img class="hatena-fotolife" title="f:id:pregum_fox:20181011001305p:plain" src="https://cdn-ak.f.st-hatena.com/images/fotolife/p/pregum_fox/20181011/20181011001305.png" alt="f:id:pregum_fox:20181011001305p:plain" /></p>
<p> </p>
<p>つぎに、NuGetを追加していきます。</p>
<p>ソリューションウインドウを開き、プロジェクトを右クリックします。</p>
<p>そしてNuGetパッケージの管理というメニューがあるのでそれをクリック。</p>
<p><img class="hatena-fotolife" title="f:id:pregum_fox:20181011001416p:plain" src="https://cdn-ak.f.st-hatena.com/images/fotolife/p/pregum_fox/20181011/20181011001416.png" alt="f:id:pregum_fox:20181011001416p:plain" /></p>
<p>そうすると、NuGetパッケージの管理のウインドウが表示されるので、そこの検索ウインドウで"ReactiveProperty"と入力し、検索すると表示されるので、その項目をクリックし、バージョンを3.6.0に合わせインストールします。</p>
<p><img class="hatena-fotolife" title="f:id:pregum_fox:20181011001612p:plain" src="https://cdn-ak.f.st-hatena.com/images/fotolife/p/pregum_fox/20181011/20181011001612.png" alt="f:id:pregum_fox:20181011001612p:plain" /></p>
<p> </p>
<p>Prismも似たような感じで"Prism.Wpf"で検索し、Prism.Wpfのバージョンを6.3.0に設定し、インストールしてください。</p>
<p><img class="hatena-fotolife" title="f:id:pregum_fox:20181011001841p:plain" src="https://cdn-ak.f.st-hatena.com/images/fotolife/p/pregum_fox/20181011/20181011001841.png" alt="f:id:pregum_fox:20181011001841p:plain" /></p>
<p> </p>
<p>あとJsonファイルのシリアライズ・デシリアライズを簡単にできるNuGet「Json.Net」を追加(項目名はNewtonsoft.Jsonですので注意)</p>
<p> </p>
<p><img class="hatena-fotolife" title="f:id:pregum_fox:20181011002258p:plain" src="https://cdn-ak.f.st-hatena.com/images/fotolife/p/pregum_fox/20181011/20181011002258.png" alt="f:id:pregum_fox:20181011002258p:plain" /></p>
<p> </p>
<p> </p>
<p>私はこれで動かそうとしたのですが、これだけだとどうやら足りないみたいで、調べてみるとReactivePropertyにはSystem.Reactive.IScheduleが足りないみたいだったので、NuGetパッケージで"System.Reactive.Compatibility"と検索すると同じ名前のパッケージが出てきますのでそれをインストールするとうまく動作するようになりました。</p>
<p><img class="hatena-fotolife" title="f:id:pregum_fox:20181011001958p:plain" src="https://cdn-ak.f.st-hatena.com/images/fotolife/p/pregum_fox/20181011/20181011001958.png" alt="f:id:pregum_fox:20181011001958p:plain" /></p>
<p> </p>
<p> </p>
<p>ひとまずこれで、開発環境が整ったので次回から実際にコードを書いていく作業に入ってい来たいと思います。</p>
<p> </p>
<p> </p>
<p> </p>
<p> </p>
