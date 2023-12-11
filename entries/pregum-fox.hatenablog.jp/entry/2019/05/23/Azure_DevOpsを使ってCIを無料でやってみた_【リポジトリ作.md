---
Title: Azure DevOpsを使ってCIを無料でやってみた 【リポジトリ作成～単体テスト自動実行まで】
Category:
- AzureDevOps
- CI
- C#
Date: 2019-05-23T06:08:54+09:00
URL: https://pregum-fox.hatenablog.jp/entry/2019/05/23/Azure_DevOps%E3%82%92%E4%BD%BF%E3%81%A3%E3%81%A6CI%E3%82%92%E7%84%A1%E6%96%99%E3%81%A7%E3%82%84%E3%81%A3%E3%81%A6%E3%81%BF%E3%81%9F_%E3%80%90%E3%83%AA%E3%83%9D%E3%82%B8%E3%83%88%E3%83%AA%E4%BD%9C
EditURL: https://blog.hatena.ne.jp/pregum_fox/pregum-fox.hatenablog.jp/atom/entry/17680117127153191994
---

こんにちは。 pregum_foxです。

今回は、前回の記事で作成したAzure DevOpsのアカウントを使って、Azure Reposに新しくレポジトリを作成し、自動ビルドから単体テストまでできるようにしてみました。



<!-- more -->



前回の記事の内容は以下をご覧ください。

[https://pregum-fox.hatenablog.jp/entry/2019/05/16/Azure_DevOps%E3%82%92%E4%BD%BF%E3%81%A3%E3%81%A6CI%E3%82%92%E7%84%A1%E6%96%99%E3%81%A7%E3%82%84%E3%81%A3%E3%81%A6%E3%81%BF%E3%81%9F_%E3%80%90%E7%99%BB%E9%8C%B2%E7%B7%A8%E3%80%91:embed:cite]


CIとは関係ない、Nugetの仕組みを全然知らなかったことで数日悩まされていましたが、何とか単体テストまでこぎつけることができたので次回からは同じことを繰り返さないよう、残しておきます。

単体テストは基本的に[こちらの記事](http://iyemon018.hatenablog.com/entry/2019/03/17/155833)を大変参考にさせて頂いたので、単体テストの部分だけ試したい！という方はそちらの方の記事をご覧ください。

目次です。


[:contents]


### 開発環境

| 項目 | 内容 |
| --- | --- |
| 言語 | C# + .Net Framework 4.7.2 |
| テスティングフレームワーク | xUnit 2.4.0 |
| IDE | Visual Studio 2019 |
| OS | Windows 10 |
| バージョン管理システム | Git |

### 大まかな流れ

今回やる作業は以下の手順で行っていきます。

```
1. Azure ReposにSSH接続用の公開鍵を作成し登録
2. Azure Reposでリポジトリ作成し、ローカルにpullを行う
3. 3. 作成したリポジトリにサンプル用プロジェクトとテスト用プロジェクトを追加し、Azure Reposにpushを行う
4. Azure Pipelinesで自動ビルドを実行するJobを登録
5. Azure PipelinesのJobに単体テストを行うtaskを追加し、正常に実行されていることを確認
6. 単体テストのテスト結果を集計するtaskを追加し、正常に実行されていることを確認
```

1. は特筆することはないと思いますが、

私は鍵の作り方から忘れていたのでここにまとめておきます。。。

2.と3. は1. が正常に作業できていましたら問題なくできるかと思います。

4.と5.と6. は私は未知の世界でしたので、実験しながら作業していきました。

<span style="font-size: 80%">~~pipelineのazure-pipelines.ymlの変更に40回ぐらい失敗して不貞寝したことは秘密です。~~</span>


それでは実際に単体テストまでの手順を記載していきます。

#### 1. Azure ReposにSSH接続用の公開鍵を作成し登録する。

前回作成したProjectに接続する為に、SSH接続用の公開鍵と秘密鍵を作成します。

別の方法でProjectに接続する場合や既に分かっている方はこの手順は飛ばしてもらって大丈夫です。

例としては[こちらのサイト](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)からダウンロードしたputtygen.exeを使用して公開鍵と秘密鍵を作成します。

<figure class="figure-image figure-image-fotolife" title="putty.zipをダウンロード">[f:id:pregum_fox:20190522034835p:plain]<figcaption>putty.zipをダウンロード</figcaption></figure>

適当なフォルダでputty.zipフォルダを解凍し、中からputtygen.exeを起動します。

<figure class="figure-image figure-image-fotolife" title="puttygen.exe">[f:id:pregum_fox:20190523060130p:plain]<figcaption>puttygen.exe</figcaption></figure>

すると、画面が立ち上がるので、鍵の強度が2048bitでない場合は2048bitに変更し、Generateボタンをクリックし
進捗バーが消えるまで適当にカーソルを動かします。

<figure class="figure-image figure-image-fotolife" title="鍵の生成">[f:id:pregum_fox:20190522035713p:plain]<figcaption>鍵の生成</figcaption></figure>

※ たまに上記の鍵の生成後表示されるKey fingerprint:が"ssh-rsa 2047 ..."の場合があり、その場合は"ssh-rsa 2048 ..."になるまで先程の手順を繰り返してください。


<figure class="figure-image figure-image-fotolife" title="生成後の注意">[f:id:pregum_fox:20190522042008p:plain]<figcaption>生成後の注意</figcaption></figure>

生成ができましたら、key passphraseを確認用も含め2回入力し、公開鍵と秘密鍵を保存します。

<figure class="figure-image figure-image-fotolife" title="公開鍵と秘密鍵を保存">[f:id:pregum_fox:20190522042548p:plain]<figcaption>公開鍵と秘密鍵を保存</figcaption></figure>

ファイル名はここでは

*  公開鍵はid_rsa_2048_pub.ppk
*  秘密鍵はid_rsa_2048_priv.ppk

としておきます。

保存フォルダはWindowsキーを入力後、cmdと入力しコマンドプロンプトのデフォルトのフォルダに.sshというフォルダを作成し、そこに保存しました。

<figure class="figure-image figure-image-fotolife" title="鍵保存場所">[f:id:pregum_fox:20190522043248p:plain]<figcaption>鍵保存場所</figcaption></figure>

ブラウザでAzure DevOpsのSSH接続設定用画面を開き、先ほど作成した公開鍵を登録します。

<figure class="figure-image figure-image-fotolife" title="設定画面">[f:id:pregum_fox:20190522043913p:plain]<figcaption>設定画面</figcaption></figure>

設定画面に行くと、左下に"SSH public keys"という項目があるのでそこをクリックし、公開鍵の登録画面に移動します。

<figure class="figure-image figure-image-fotolife" title="公開鍵登録画面">[f:id:pregum_fox:20190522044215p:plain]<figcaption>公開鍵登録画面</figcaption></figure>

その画面で公開鍵の名前を任意で入力し、公開鍵の中身をテキストエディタ等でコピペし、Saveをクリックし登録処理を行います。

<figure class="figure-image figure-image-fotolife" title="公開鍵の追加">[f:id:pregum_fox:20190522044606p:plain]<figcaption>公開鍵の追加</figcaption></figure>

以上でSSH接続用の公開鍵の登録作業が完了しました。

次に登録するリポジトリを作成します。

#### 2. Azure Reposでリポジトリ作成し、ローカルにpullを行う

Azure ReposにはGitHubからリポジトリをインポートすることができたりしますが、ここではAzure Reposで作成したリポジトリをクローンしてソースを追加していきます。

ここではSourcetreeを使用してリポジトリをクローンします。

コマンドラインで作成される方や他のGitのクライアントを使用される方は適宜置き換えてください。

Azure Repos側でリポジトリを作成します。

画像ではプロジェクト名が変わっていますが、記事用に作成しただけですので他の方は元のプロジェクトで問題ありません。

<figure class="figure-image figure-image-fotolife" title="リポジトリの作成">[f:id:pregum_fox:20190522053354p:plain]<figcaption>リポジトリの作成</figcaption></figure>

リポジトリが作成されるので、クローンを行います。

画面右上辺りにCloneと書かれている箇所があるのでボタンをクリックし、SSH接続の方はSSHに変更しコピーを行いローカルのgitクライアントのクローン先URLに設定し、クローンします。

<figure class="figure-image figure-image-fotolife" title="クローン用URLのコピー">[f:id:pregum_fox:20190522054041p:plain]<figcaption>クローン用URLのコピー</figcaption></figure>

<figure class="figure-image figure-image-fotolife" title="Sourcetreeのクローン">[f:id:pregum_fox:20190522055727p:plain]<figcaption>Sourcetreeのクローン</figcaption></figure>

無事クローンができたら、次の作業に移ります。


#### 3. 作成したリポジトリにサンプル用プロジェクトとテスト用プロジェクトを追加し、Azure Reposにpushを行う

.Net Frameworkのサンプルプロジェクトとテストプロジェクトを追加します。

詳細は省略しますが以下の形でリポジトリに登録します。

```
/(リポジトリルート)
|  .git
|  .gitignore
|  README.md
|
\---ConsoleAppProject
    |   ConsoleAppProject.sln
    |
    +---ConsoleAppProject
    |   |   App.config
    |   |   ConsoleAppProject.csproj
    |   |   Program.cs
    |   |
    |   \---Properties
    |           AssemblyInfo.cs
    |
    +---packages   # .gitignoreにpackagesフォルダを記入しておく。
    |
    \---UnitTestProject
        |   packages.config
        |   UnitTest1.cs
        |   UnitTestProject.csproj
        |
        +---bin
        |
        \---Properties
                AssemblyInfo.cs
```

Program.csは以下の通りです

``` cs

// /ConsoleAppProject/ConsoleAppProject/Program.cs
namespace ConsoleAppProject
{
    class Program
    {
        static void Main(string[] args)
        {
        }
    }

    public class SampleClass
    {
        public string Greeting()
        {
            return "Hello World.";
        }

        public int GetNine()
        {
            return 9;
        }

        public int Add(int a, int b)
        {
            return a + b;
        }
    }
}


```

UnitTest1.csの中身は以下の通りです。

``` cs

// /ConsoleAppProject/UnitTestProject/UnitTest1.cs

using ConsoleAppProject;
using Xunit;

namespace UnitTestProject
{
    public class UnitTest1
    {
        [Fact(DisplayName ="Greetingメソッドのテスト")]
        public void GreetingTest()
        {
            var sample = new SampleClass();
            Assert.Equal("Hello World.", sample.Greeting());
        }

        [Fact(DisplayName ="GetNineメソッドのテスト")]
        public void GetNineTest()
        {
            var sample = new SampleClass();
            Assert.Equal(9, sample.GetNine());
        }

        [Theory(DisplayName ="Addメソッドのテスト")]
        [InlineData(1,2,3)]
        [InlineData(1,4,5)]
        [InlineData(-11,40,29)]
        public void AddTest(int a, int b, int sum)
        {
            var sample = new SampleClass();
            Assert.Equal(sum, sample.Add(a, b));
        }
    }
}

```

xunitを使用したテストプロジェクトの作成方法は[xUnitのGetting Started](https://xunit.net/docs/getting-started/netfx/visual-studio)をご覧ください。


Nugetは
 
 * xunit 2.4.1
 * xunit.runner.console 2.4.1
 * xunit.runner.visualstudio 2.4.1

の2つのライブラリをTestUnitProjectに追加しています。

Azure Pipelinesで実施するテスト実行exeはxunit.runner.consoleをプロジェクトに追加していないと

使用できないので追加するようにしてください。

またTestUnitProjectの参照にConsoleAppProjectを追加します。

<figure class="figure-image figure-image-fotolife" title="ConsoleAppProjectの参照追加">[f:id:pregum_fox:20190522064141p:plain]<figcaption>ConsoleAppProjectの参照追加</figcaption></figure>

ローカルで単体テストが成功したことを確認したら、Azure Reposにコミット後、pushを行います。

<figure class="figure-image figure-image-fotolife" title="テスト結果">[f:id:pregum_fox:20190522064701p:plain]<figcaption>テスト結果</figcaption></figure>

pushが正常にできたら次の手順に移ります。

#### 4. Azure Pipelinesで自動ビルドを実行するJobを登録

次にdevelopブランチにコミットされたタイミングで自動ビルドが動作するようにします。

Azure DevOpsで PipelinesのBuilds項目でNew pipelineをクリックします。

<figure class="figure-image figure-image-fotolife" title="Azure pipelines画面">[f:id:pregum_fox:20190522065533p:plain]<figcaption>Azure pipelines画面</figcaption></figure>

どのリポジトリか問われるので、Azure Repos Gitを選択し、3.で更新されたリポジトリを選択します。

<figure class="figure-image figure-image-fotolife" title="リポジトリの種類選択画面 ">[f:id:pregum_fox:20190522065906p:plain]<figcaption>リポジトリの種類選択画面 </figcaption></figure>

<figure class="figure-image figure-image-fotolife" title="リポジトリ選択画面">[f:id:pregum_fox:20190522070125p:plain]<figcaption>リポジトリ選択画面</figcaption></figure>

次は、pipelineのテンプレートを問われるのでshow moreをクリックし、.NET Desktopの項目を

選択します。

<figure class="figure-image figure-image-fotolife" title="pipelineテンプレート選択画面">[f:id:pregum_fox:20190522070625p:plain]<figcaption>pipelineテンプレート選択画面</figcaption></figure>


pipelineの編集画面が表示されるので、右上のSave and runをクリックします。

<figure class="figure-image figure-image-fotolife" title="pipeline編集画面">[f:id:pregum_fox:20190522070926p:plain]<figcaption>pipeline編集画面</figcaption></figure>

ポップが出ますがそのまま右下のSave and runをクリックします。

するとPipelinesのBuilds項目をクリックすると先ほどのJobの実行状況がリストで表示され、クリックすると詳細が表示されます。
<figure class="figure-image figure-image-fotolife" title="Jobの実行状況">[f:id:pregum_fox:20190522072515p:plain]<figcaption>Jobの実行状況</figcaption></figure>


#### 5. Azure PipelinesのJobに単体テストを行うtaskを追加し正常に実行されていることを確認

この作業は[伊右衛門 (id:iyemon018)](http://iyemon018.hatenablog.com/about)さんの[こちらの記事](http://iyemon018.hatenablog.com/entry/2019/03/17/155833)を大変参考にさせて頂きました。 ありがとうございます。

PipelinesのBuilds項目をクリックして、右上のEditボタンをクリックしてazure-pipelines.ymlを編集する画面に移動します。

<figure class="figure-image figure-image-fotolife" title="job実行状況画面">[f:id:pregum_fox:20190523040720p:plain]<figcaption>job実行状況画面</figcaption></figure>

ではazure-pipelines.ymlの最後の辺りに書いているtask: VSTest@2のブロックを削除します。

<figure class="figure-image figure-image-fotolife" title="不要なtaskの削除">[f:id:pregum_fox:20190523041415p:plain]<figcaption>不要なtaskの削除</figcaption></figure>

右の検索バーに"command"を打ってCommand Lineをクリックします。
するとポップアップが右側から出てくるので、そのままAddをクリックします。

<figure class="figure-image figure-image-fotolife" title="Command Line taskの追加">[f:id:pregum_fox:20190523042032p:plain]<figcaption>Command Line taskの追加</figcaption></figure>

すると、azure-pipelines.ymlにcommand line用のtaskが追加されるので、script
以降に書かれている部分にxunitの単体テストを動作させるコマンドを記述します。

```
# task

- task: CmdLine@2
  inputs:
    script:
      ConsoleAppProject\packages\xunit.runner.console.2.4.1\tools\net472\xunit.console.exe 
      $(System.DefaultWorkingDirectory)\ConsoleAppProject\UnitTestProject\bin\$(buildConfiguration)\UnitTestProject.dll
```
1行目のxunit.console.exeの後ろに半角スペースを入れる必要があるので注意してください。

また、上記のパスは私のフォルダ構成での例ですので
ソリューションやプロジェクトのフォルダ名は適宜読み替えてください。

入力後、右上のSaveをクリックし、表示されたポップアップの右下のSaveをクリックするとJobが走ります。

<figure class="figure-image figure-image-fotolife" title="azure-pipelines.ymlの保存">[f:id:pregum_fox:20190523044701p:plain]<figcaption>azure-pipelines.ymlの保存</figcaption></figure>

PipelinesのBuilds項目をクリックし、Jobの実行状況を確認します。

※Jobの実行状況がHistoryに追加されていない場合、実行されていない為右上のEditをクリック後、右上のRunをクリックすれば、手動でJobを実行することができます。

Jobの詳細を確認するには、Jobの項目をクリックしてください。

<figure class="figure-image figure-image-fotolife" title="Jobの実行状況画面">[f:id:pregum_fox:20190523045501p:plain]<figcaption>Jobの実行状況画面</figcaption></figure>

<figure class="figure-image figure-image-fotolife" title="Jobの詳細">[f:id:pregum_fox:20190523045623p:plain]<figcaption>Jobの詳細</figcaption></figure>

Jobの項目でCmdLineと書かれているのが単体テストの実行taskです。

この項目が失敗している場合、コマンドが間違っている可能性が高いので、エラーメッセージで調べる等で修正する必要があります。

私の場合はpackageフォルダがリポジトリにコミットされておりNugetの復元ができていなかったことがあったのでで、そちらも確認してみるといいかもしれません。

一応単体テストを自動で実行できるようになりましたが、まだJobの詳細のTestsタブには単体テストの結果が表示されていません。

次の手順でここにテスト結果を表示させたいtaskを追加します。

#### 6. 単体テストのテスト結果を集計するtaskを追加し、正常に実行されていることを確認

ではまた、azure-pipeline.ymlの編集画面に移動します。

5. で追加した単体テスト実行コマンドを以下のように変更します。

```
# task

- task: CmdLine@2
  inputs:
    script:
      ConsoleAppProject\packages\xunit.runner.console.2.4.1\tools\net472\xunit.console.exe 
      $(System.DefaultWorkingDirectory)\ConsoleAppProject\UnitTestProject\bin\$(buildConfiguration)\UnitTestProject.dll -xml ./test-results.xml
```

変更箇所はコマンドの最後に -xml ./test-reuslts.xmlを追加しただけです。

検索バーにtest と入力し、Publish Test Resultsの項目を選択します。

<figure class="figure-image figure-image-fotolife" title="Publish Test Resultsの追加">[f:id:pregum_fox:20190523051021p:plain]<figcaption>Publish Test Resultsの追加</figcaption></figure>

すると右からポップアップが表示され、テストのフォーマット等を聞かれるので、

* Test result formatをXUnit
* Test results files ./test-results

に変更し、Addをクリックしazure-pipelines.ymlに追加します。

<figure class="figure-image figure-image-fotolife" title="テスト結果の公開taskの追加">[f:id:pregum_fox:20190523052439p:plain]<figcaption>テスト結果の公開taskの追加</figcaption></figure>

azure-pipelines.ymlをSaveし、Jobの詳細のTestsタブにテスト結果が表示されていることを確認します。

<figure class="figure-image figure-image-fotolife" title="テスト結果の確認">[f:id:pregum_fox:20190523052832p:plain]<figcaption>テスト結果の確認</figcaption></figure>

### Tips

#### SSH接続の代わりとなるpersonal access tokensがある

私自身もよくわかってはいませんが、Azure DevOpsにはUserに付与できるPersonal access tokensがあるようで、wikiの編集からリポジトリへのアクセスまで幅広い設定ができるようです。

参考

[https://docs.microsoft.com/ja-jp/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate?view=azure-devops:embed:cite]


#### Azure Pipelinesの無料ビルドの時間の確認方法

Azure Pipelinesは5 ユーザーまでならCI/CD 用の 1 個のホスト ジョブ (1,800 分/月) および 1 個のセルフホスト ジョブが使用できます。

ただ、残り時間はどこを確認すればいいかわからなかったので調べたところ、どうやら左下の"Project settings"をクリックし、Parallel jobsの項目を選択すれば何分使用しているかがわかります。

<figure class="figure-image figure-image-fotolife" title="無料ビルドの残り時間確認">[f:id:pregum_fox:20190523054426p:plain]<figcaption>無料ビルドの残り時間確認</figcaption></figure>


### 雑感

思った以上に長くなりました。

とはいえまだできていないことがあるので(Job実行をmasterブランチ以外で行う、azure-pipelines.ymlの中身を理解する)、試してわかり次第書いていきたいなと思っています。

またBoardsやArtifacts等は全くわからないままなので、どういったことができるか等程度は理解しておき簡単な機能から試してみたいと思います。

ここまで見てくださってありがとうございました。

### 参考記事

* [NuGet の概要](https://docs.microsoft.com/ja-jp/nuget/what-is-nuget#tracking-references-and-restoring-packages)

* [（Azure DevOps）Azure Pipelines で.NET Framework + xUnit のユニットテストの実行と集計方法について](http://iyemon018.hatenablog.com/entry/2019/03/17/155833)

* [Use SSH key authentication](https://docs.microsoft.com/en-us/azure/devops/repos/git/use-ssh-keys-to-authenticate?view=azure-devops)

* [https://www.rahulpnath.com/blog/azure-pipelines-how-to-find-remaining-build-minutes/:title]

* [Azure Pipelines YAML schema](https://docs.microsoft.com/en-us/azure/devops/pipelines/yaml-schema?view=azure-devops&tabs=schema#triggers)

* [Azure DevOps Documentation](https://docs.microsoft.com/en-us/azure/devops/?view=azure-devops)

* [Tip of the Week: Azure Pipelines - How to Find Remaining Free Build Minutes?](https://www.rahulpnath.com/blog/azure-pipelines-how-to-find-remaining-build-minutes/)

* [YAML 基本記法まとめ](https://qiita.com/tfrcm/items/5f8e4c5795ce41b214d1)

