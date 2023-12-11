---
Title: Azure DevOpsを使ってCIを無料でやってみた 【アカウント作成～Azure DevOps登録まで】
Category:
- CI
- AzureDevOps
Date: 2019-05-16T23:15:36+09:00
URL: https://pregum-fox.hatenablog.jp/entry/2019/05/16/Azure_DevOps%E3%82%92%E4%BD%BF%E3%81%A3%E3%81%A6CI%E3%82%92%E7%84%A1%E6%96%99%E3%81%A7%E3%82%84%E3%81%A3%E3%81%A6%E3%81%BF%E3%81%9F_%E3%80%90%E7%99%BB%E9%8C%B2%E7%B7%A8%E3%80%91
EditURL: https://blog.hatena.ne.jp/pregum_fox/pregum-fox.hatenablog.jp/atom/entry/17680117127135469737
---

こんにちは。 pregum_foxです。

今回はAzure Depopsを使ってCIをやってみたいと思います。

背景としてはここ数日までCIは聞いたことあるけどどうやるかわからない。。。

Jenkins？~~あの執事のおじさんだよね。~~

ということぐらいしか知らずCIやってるよという方に憧憬の念を抱いていたので私も話についていける程度には理解しておこうということでいろいろググってみていました。

そうすると、少し前に[こちらのサイト](https://qiita.com/yamuuuuuun/items/8ceaae59bea3837c57e1)と[こちらのサイト](http://zutto-megane.com/azure/azuredevops_info/)や[こちらのAdvent Calendar](https://qiita.com/advent-calendar/2018/azuredevops)等で**Azure Devops**というキーワードが目に入ってきました。

上のサイトを少しのぞいてみるとどうやら


> 誤解を恐れず乱暴に言うと、ReadmineとGithubとJenkinsが一緒になったツール

だということで、これが無料で利用できるという個人としてはありがたい内容だったので触らないでいるのはもったいないと思い、触ってみました。




<!-- more -->



目次です。


[:contents]


### 必要なもの

* Microsoftのアカウント([こちらのサイト](https://support.microsoft.com/ja-jp/help/4026324/microsoft-account-how-to-create)に登録する方法が記載されています) or GitHubのアカウント((Azure DevOpsのAzure ADでサポートされている組織に関連付けられている場合は不可))

* 少しの元気

### Azure DevOpsを使用するまでの大まかな流れ

#### 1. Azure DevOpsへサインインする

Microsoftのアカウントでの方法を記載しますが、GitHubのアカウントを使用する場合と違うのは最初のサインインの方法程度です。

GitHubのアカウントでAzure DevOpsを利用したいという方は[こちら](https://docs.microsoft.com/ja-jp/azure/devops/user-guide/sign-up-invite-teammates?view=azure-devops)を参照して下さい。

 [こちらのサイト](https://azure.microsoft.com/ja-jp/services/devops/?&OCID=AID736753_SEM_4yZ9CNsK&MarinID=s4yZ9CNsK_323084868360_%2Bazure%20%2Bdevops_b_c__60121929530_kwd-399530214682_)からAzure DevOpsのサイトに移動し、以下の無料で開始ボタンを押します。

<figure class="figure-image figure-image-fotolife" title="Microsoft Azure DevOpsのサイト">[f:id:pregum_fox:20190516205142p:plain]<figcaption>Microsoft Azure DevOpsのサイト</figcaption></figure>

そうすると、サインインの画面が表示されるので、アカウント登録に使用したメールアドレス/電話番号等を入力し、その後パスワード入力画面も似たような画面で表示されるので入力し、サインインします。

<figure class="figure-image figure-image-fotolife" title="サインイン画面">[f:id:pregum_fox:20190516210125p:plain]<figcaption>サインイン画面</figcaption></figure>

そうすると、以下のような画面が表示されるので、Continueを押して次へ進みます。

<figure class="figure-image figure-image-fotolife" title="サインイン直後の画面">[f:id:pregum_fox:20190516220117p:plain]<figcaption>サインイン直後の画面</figcaption></figure>

次に組織名の入力とデータホストの場所

私はとりあえずEast Asiaを選択しました。

<figure class="figure-image figure-image-fotolife" title="組織名とデータのホスト場所の入力">[f:id:pregum_fox:20190516222441p:plain]<figcaption>組織名とデータのホスト場所の入力</figcaption></figure>

#### 2. プロジェクトの作成

すると、プロジェクトを作成しましょうと入力画面が表示されるので

* プロジェクト名
* 説明
* プロジェクトの可視性(Public / Private)

を入力・選択します。

その下にAdvancedと書かれたボタンをクリックすると、Version controlとWork item processの項目が表示され、バージョン管理ツール(Git / Team Foundation Version Control)とプロセスモデル(Basic / agile / SMMI / Scrum)の選択を迫られますが、私はどちらもデフォルトのGit, Basicを選択しました。

全て入力が完了しましたら、"Create project"をクリックします。

<figure class="figure-image figure-image-fotolife" title="プロジェクト作成画面">[f:id:pregum_fox:20190516224236p:plain]<figcaption>プロジェクト作成画面</figcaption></figure>


これでAzure DevOpsの組織とプロジェクト作成が完了しました！

<figure class="figure-image figure-image-fotolife" title="概要">[f:id:pregum_fox:20190516225355p:plain]<figcaption>概要</figcaption></figure>

後は実際にリポジトリを登録し、テストコードを自動で走らせる処理を書いて終わりと行きたいところですが

結構量があるため、次の記事でリポジトリの登録、テストコードを走らせる処理の記述をやっていきたいと思います。


読んでいただき、ありがとうございました。


### 参考記事

* [Azure devOps Documentation](https://docs.microsoft.com/en-us/azure/devops/index?view=azure-devops)

* [azure pipeline を使った　CIがとても使いやすかった](https://qiita.com/yamuuuuuun/items/8ceaae59bea3837c57e1)

* [Azure DevOpsをやってみたいあなたに。Azure DevOpsの気になる疑問に答えます！](http://zutto-megane.com/azure/azuredevops_info/)

* [Azure DevOps Advent Calendar 2018](https://qiita.com/advent-calendar/2018/azuredevops)


