---
Title: C#初心者がDartとC#のコードを比較してみた【その1】
Category:
- C#
- Dart
Date: 2019-08-09T23:29:49+09:00
URL: https://pregum-fox.hatenablog.jp/entry/2019/08/09/C%23%E5%88%9D%E5%BF%83%E8%80%85%E3%81%8CDart%E3%81%A8C%23%E3%81%AE%E3%82%B3%E3%83%BC%E3%83%89%E3%82%92%E6%AF%94%E8%BC%83%E3%81%97%E3%81%A6%E3%81%BF%E3%81%9F
EditURL: https://blog.hatena.ne.jp/pregum_fox/pregum-fox.hatenablog.jp/atom/entry/17680117127179457819
---

こんにちは。　pregum_foxです。

C#初心者である私が、flutterを触ることになったのですが、flutterで開発するには、Dartという言語を使用するため、まずDartという言語について、調べてみました。

### 注意
こちらの公式ドキュメントを読みながらプログラムを書いて動作を確認していますが、一部DartとC#が合っていない所やよろしくない書き方をしている可能性があるのでその場合は、コメント等で指摘していただけると幸いです。



<!-- more -->




目次です。

[:contents]


### Dartとは？

>Dart（ダートもしくはダーツ。当初は Dash と呼ばれていた）はGoogleによって開発されたウェブ向けのプログラミング言語である。2011年10月10日 - 12日に開催された デンマークのオーフスで開催された「GOTOカンファレンス」で公開された。この言語は、ウェブブラウザ組み込みのスクリプト言語であるJavaScriptの代替となることを目的に作られた。

> 2018年2月にはDart 2が発表された。強力な型システムなどの言語機能の強化が行われている。

(wikpediaより引用)

Dartの言語機能や一部のライブラリは[Dart Pad](https://dartpad.dartlang.org/)で実行することができます。

ファイル操作関係のライブラリを試したい場合はSDKを落としてくる必要があるみたいです。 


[https://dart.dev/tools/dartpad]



[dartのサイト](https://dart.dev/)のチュートリアルを少し見た感想ですが、基本的にJavaやC#に似た感じだなと思いました。
C#やJavaのジェネリクスやJavaのアノテーション、があり抽象クラス、例外処理等がありますがC#やJavaにはない書き方(カスケード記法やコンストラクタでフィールドに値を代入等)があって便利だなと思う書き方がいくつもありました。

C#にも是非コンストラクタでフィールドに値を代入する構文は欲しいなと思いました。

dartはmain()関数がエントリポイントとなっています。

### 動作環境

以下の動作環境で確認しました。

 * Dart
     * Dart SDK 2.4.0

 * C#
     * .Net Core 2.1 / C# 7.2  ((とはいえほとんど.Net Frameworkでも動くと思います。))
 
### 内容

以下にDartとC#の同じ処理をするプログラムを書いていきます。
ここでいう同じ処理というのは同じ文字が表示される、同じ計算がされる等のレベルでの処理のことです。

またDart2のコードを記載しています。Dart1には対応していない箇所がありますのでご了承ください。

##### 変数

```dart
void main() {
  var stem1 = 3;  // C#のvarと同じように型推論を行う。
  int stem2 = 4;  // もちろん型を明示的に指定することもできる。
  print(stem1 + stem2); // 7
  String str = "文字列は"    // [+]演算子なしでOK
  "行を分けても大丈夫";
  print(str);   // => 文字列は行を分けても大丈夫
  var str2 = """これでも
大丈夫""";
  print(str2);
  // これでも
  // 大丈夫 
  final double dbl = 3.3; // finalが付いていると再代入不可
  // dbl = 2.2; // error
  const String morning = "おはよう"; // コンパイル時定数
}
```

```cs
class Program{
  static void Main(string[] args){
    var stem1 = 3;
    int stem2 = 4;
    System.Diagnostics.Debug.WriteLine(stem1 + stem2);  // 7
    string str = "文字列は" + 
    "行を分けても大丈夫";
    System.Diagnostics.Debug.WriteLine(str);  // 文字列は行を分けても大丈夫
    string str2 = @"これでも
大丈夫";
    System.Console.WriteLine(str2);
    readonly double dbl = 3.3; // 再代入不可
    // dbl = 2.2; //error
    const string morning = "おはよう"; // コンパイル時定数
  }
}
```

##### 組み込み型

###### 数値

ビットシフト演算も可能ですがそれは演算子の項目に記載しています。

```dart
void main() {
  int a = 1;
  int hex = 0xffff; // 16進数でのリテラルの設定も可能
  double b = 3.4;
  double exponents = 1.42e5;
  double z = 1; // Dart 2.1より前はエラーになっていたらしい。
  num c = 5;
  num number = 1.1;
  print('${a + b}');

  // String -> int
  int one = int.parse('1');
  print('$one');

  // String -> double
  double onePointOne = double.parse('1.1');
  print('$onePointOne');

  // int -> String
  String oneAsString = 1.toString();
  print('$oneAsString'); // => 1

  // double ->String
  String piAsString = 3.14159.toStringAsFixed(2);
  print('$piAsString'); // => 3.14 
}
```

```cs
    class Program
    {
        static void Main(string[] args)
        {
            int a = 1;
            int hex = 0xffff;
            double b = 3.4;
            double exponents = 1.42e5;
            var c = 5; // numはC#にはない
            var number = 1.1;
            System.Diagnostics.Debug.WriteLine($"{a + b}");

            // string -> int
            int one = int.Parse("1");
            System.Diagnostics.Debug.WriteLine($"{one}");

            // string -> double
            double onePointOne = double.Parse("1.1");
            System.Diagnostics.Debug.WriteLine($"{onePointOne}");

            // int -> string
            string oneAsString = 1.ToString();
            System.Diagnostics.Debug.WriteLine($"{oneAsString}");

            // double -> string
            string piAsString = 3.14159.ToString("F2");
            System.Diagnostics.Debug.WriteLine($"{piAsString}");
        }
    }
```


###### 文字列

```dart
void main() {
  var dog = "犬";
  print("dogは日本語で" + dog);
  print("dogは日本語で$dog");  // 変数なら$を頭につけるだけで展開される。
  var item1 = 7;
  var item2 = 10;
  print("${item1 + item2}"); // 式の場合は{}でくくる
}
```

```cs
class Program{
  static void Main(string[] args){
    var dog = "犬";
    System.Diagnostics.Debug.WriteLine("dogは日本語で" + dog);
    System.Diagnostics.Debug.WriteLine($"dogは日本語で{dog}");  // $での文字列補間では波かっこをつける
    var item1 = 7;
    var item2 = 10;
    System.Diagnostics.Debug.WriteLine($"{item1 + item2}");
  }
}
```

###### 真偽値

```dart
void main() {
  // 文字列が空か検証
  var fullName = '';
  assert(fullName.isEmpty);

  // 0以下か検証
  var hitPoints = 0;
  assert(hitPoints <= 0);

  // nullか検証
  var unicorn;
  assert(unicorn == null);

  // NaNか検証
  var iMeantToDoThis = 0 / 0;
  assert(iMeantToDoThis.isNaN);
}
```

```cs
    class Program
    {
        static void Main(string[] args)
        {
            // 文字列が空か検証
            var fullName = "";
            System.Diagnostics.Debug.Assert(fullName.Length == 0);
            // string.IsNullOrEmpty(fullName); だとnullまでtrueになってしまうので長さで判定している。

            // 0以下か検証
            var hitPoints = 0;
            System.Diagnostics.Debug.Assert(hitPoints <= 0);

            // nullか検証
            // C#の場合未割当の参照型は使用できないため、nullを指定している。
            object unicorn = null;
            System.Diagnostics.Debug.Assert(unicorn == null);

            // Nanか検証
            var iMeantToDoThis = 0 / .0;
            System.Diagnostics.Debug.Assert(double.IsNaN(iMeantToDoThis));
        }
    }
```

###### リスト

Dart2.3 以降はスプレット演算子(... や ...?)に対応しているそうです。

```dart
void main() {

  // List<int>を推論しています。
  var list = [1, 2, 3];
  assert(list.length == 3);
  assert(list[1] == 2);

  // コンパイル時定数リスト
  var constantList = const [4, 5, 6];

  // スプレット演算子(...)
  // 型を指定したい時は<T>[];と書く。以下に例を示す。
  var list2 = <int>[1, 2, ...constantList];
  // var list2 = List<int>[1, 2, ...constantList]; と同等だがListは省略するのが好ましいとのこと。
  assert(list2.length == 5);
  assert(list2[4] == 6);

  // null対応スプレット演算子(...?)
  var list3;
  var list4 = [11, 12, 13, ...?list3, ...list];
  list[2] = 5;
  assert(list3 == null);
  assert(list4.length == 6);
  assert(list4[5] == 3);

  // リストの生成
  var generatedList = List<int>.generate(10, (i) => i);
  assert(generatedList.length == 10);
  assert(generatedList[9] == 9);

// リストの中にif文がかける。
bool promoActive = false;
var nav = <String>['Home', 'Furniture', 'Plants', if(promoActive) 'Outlet'];
assert(nav.length == 3);

// foreachも
var listOfInts =[1,2,3];
var listOfStrigs = [ '#0', for (var i in listOfInts) '#$i'];
assert(listOfStrigs[1] == '#1');
}
```

```cs
using System.Collections.Generic;
using System.Linq;

    class Program
    {
        static void Main(string[] args)
        {
            var list = new System.Collections.Generic.List<int>(){ 1, 2, 3};
            System.Diagnostics.Debug.Assert(list.Count == 3);
            System.Diagnostics.Debug.Assert(list[1] == 2);

            // C#にはコンパイル時定数リストはないので書けない。
            // const List<int> list2 = new List<int>() { 4, 5, 6 };
            List<int> contantList = new List<int>() { 4, 5, 6 };

            // spred演算子の代わり
            var list2 = new List<int>() { 1, 2 };
            list2.AddRange(contantList);
            System.Diagnostics.Debug.Assert(list2.Count == 5);
            System.Diagnostics.Debug.Assert(list2[4] == 6);

            // null対応spred演算子の代わり
            List<int> list3 = null;
            var list4 = new List<int>() { 11, 12, 13 };
            if (list3 != null) list4.AddRange(list3);
            list4.AddRange(list);
            list[2] = 5;
            System.Diagnostics.Debug.Assert(list4[5] == 3);

            //リストの生成
            var generatedList = Enumerable.Range(0, 10).ToList();
            System.Diagnostics.Debug.Assert(generatedList.Count == 10);
            System.Diagnostics.Debug.Assert(generatedList[9] == 9);

            // C#は初期化中にifやforeachは書けない為、Linqを使用
            bool promoActive = false;
            var nav = new List<string>() { "Home", "Furniture", "Plants" };
            if (promoActive) nav.Add("Outlet");
            System.Diagnostics.Debug.Assert(nav.Count == 3);

            var listOfInts = new List<int>() { 1, 2, 3 };
            var listOfStrings = new List<string>() { "#0" };
            if( listOfInts.Any())
            {
                listOfStrings.AddRange(listOfInts.Select(x =>  "#" + x.ToString()));
            }
            System.Diagnostics.Debug.Assert(listOfStrings[1] == "#1");
        }
    }
```

###### セット

Dart2.3 以降はスプレット演算子(... や ...?)に対応しているそうです。

```dart
import 'dart:collection';

void main() {
  // デフォルトで呼ばれるSet<E>のデフォルト実装はLinkedHashSet<E>
  Set<String> tes = <String>{};
  // 下記の書き方だとMap<dynamic, dynamic>が作成されるため注意
  // var maps = {}
  Set<String> temp = LinkedHashSet<String>();

  print('tes type : ${tes.runtimeType}');
  print('temp type: ${temp.runtimeType}');
  // =>  tes type : _CompactLinkedHashSet<String>
  // =>  temp type: _CompactLinkedHashSet<String>

  tes.add('hoge');
  tes.add('fuga');
  tes.add('piyo');
  tes.add('hoge');

  // 型が違うと静的にエラーを表示してくれる。
  // tes.add(33);

  print(tes.where((x) => x.startsWith('p')).first);
  print(tes.join(", "));
}


```

```cs
using System.Collections.Generic;
using System.Linq;

class Program
{
    static void Main(string[] args)
    {
        // C#にはDartのLinkedHashSet<E>に対応するクラスが標準では存在しない為、
        // 代替案として挿入順が保証されないHashSet<T>で対応
        var tes = new HashSet<string>();

        tes.Add("hoge");
        tes.Add("fuga");
        tes.Add("piyo");
        tes.Add("hoge");

        System.Diagnostics.Debug.WriteLine(tes.Where(x => x.StartsWith("p")).First());
        System.Diagnostics.Debug.WriteLine(string.Join(", ", tes));
    }
}

```

###### マップ(連想配列)

Dart2.3 以降はスプレット演算子(... や ...?)に対応しているそうです。

```dart
import 'dart:collection';

void main() {
  // Mapのデフォルト実装はLinkedHashMap
  var gists = { 'fisrt': 'partridge'};
  var tmpGists = LinkedHashMap<String, String>();
  print('gists: ${gists.runtimeType}');
  print('tmpGists: ${tmpGists.runtimeType}');
  // gists: _InternalLinkedHashMap<String, String>
  // tmpGists: _InternalLinkedHashMap<String, String>

  gists['second'] = 'turtledoves';
  gists['fifth'] = 'golden rings';
  gists['second'] = 'second over write';

  for (var val in gists.entries) {
    print('${val.key} : ${val.value}');
  }
  // fisrt : partridge
  // second : second over write
  // fifth : golden rings

  assert(gists['fifth'] == 'golden rings');
  assert(gists['hoge'] == null);

  final constantMap = const {2: 'helium', 10: 'neon', 18: 'argon'};
  assert(constantMap.length == 3);
  // constantMap[2] = 'H'; コンパイル時定数のMapは再代入は不可
}
```

```cs
using System.Collections.Generic;

class Program
{
    static void Main(string[] args)
    {
        // C#には標準にはLinkedHashMapはない為
        // 代替案として、LinkedHashMapの挿入順が保証されないDictionary<string, string>()で対応
        var gists = new Dictionary<string, string>() { { "first", "partridge" } };

        gists["second"] = "turtledoves";
        gists["fifth"] = "golden rings";
        gists["second"] = "second over write";

        foreach (var val in gists)
        {
            System.Diagnostics.Debug.WriteLine($"{val.Key} : {val.Value}");
        }
        // first : partridge
        // second : second over write
        // fifth : golden rings

        System.Diagnostics.Debug.Assert(gists["fifth"] == "golden rings");
        // 存在していないキーを参照しようとすると 
        // System.Collections.Generic.KeyNotFoundExceptionが発生するので、ContainsKey()で存在確認を行う。
        System.Diagnostics.Debug.Assert((gists.ContainsKey("hoge") ? gists["hoge"] : "null") == "null");

        // C#にコンパイル時定数のMapは作成できないため、以下の書き方はできない
        //const Dictionary<int, string> constantMap = new Dictionary<int, string>() { { 2, "helium" }, { 10, "neon" }, { 18, "argon" } };
    }
}

```

###### Rune(ルーン文字)

RuneはDartではUTF-32コードポイントです。

C# ではこれに相当するオブジェクトは今のところ存在しません。

ただ、.NET Core 3.0 Preview 7 でSystem.Text名前空間に[Rune 構造体](https://docs.microsoft.com/en-us/dotnet/api/system.text.rune?view=netcore-3.0)が追加されたみたいです。

今回はDartのみコードを記載します。

```dart
void main() {
  // 4桁以上の場合は{}で囲む
  String clapping = '\u{1f44f}';
  print(clapping);
  print(clapping.codeUnits);
  print(clapping.runes.toList());
  // 👏
  // [55357, 56399]
  // [128079]

  Runes input = Runes('\u2665 \u{1f605} \u{1f60e} \u{1f47b} \u{1f596} \u{1f44d}');
              //         ♥      😅       😎       👻        🖖        👍 
  print(String.fromCharCodes(input));
}
```

###### シンボル

これもDart側の機能です。

宣言された演算子や識別子を表すオブジェクトだそうです。

いまいち使用用途については具体的な例が検索しても見つからなかった為、詳しくはわからなかったのですが

おそらくC#でいうリフレクションのように使用されているみたいです。

[https://dev.classmethod.jp/client-side/dart_mirrorsystem/:embed:cite]

というわけでC#もリフレクションを使用してプロパティ等の取得を行うコードを記述しました。

このシンボルの項目に記述しているコードの出力はDartとC#が同じような出力になっていないことをご了承ください。

```dart
import 'dart:mirrors';

void main() {
  // Hoge生成
  var hoge = Hoge();
  // InstanceMirrorの取得
  InstanceMirror instanceMirror = reflect(hoge);

  // ClassMirrorの取得
  ClassMirror classMirror = instanceMirror.type;
  classMirror.instanceMembers.forEach((key, ins) {
    print('$key : $ins');
  });
}

class Hoge {
  static final String CONSTANT = 'CONSTANT';
  static String VARIABLE = 'VARIABLE';
  static void staticMethod() {
    print('static method');
  }

  String variable;
  final String constant;
  Hoge()
      : variable = 'variable',
        constant = 'constant';
  void method() {
    print('method called!');
  }

  String _privateVariable;
  String get accessor => _privateVariable;
  set accessor(String value) => _privateVariable = value;
}

// 出力結果
// Symbol("==") : MethodMirror on '=='
// Symbol("hashCode") : MethodMirror on 'hashCode'
// Symbol("toString") : MethodMirror on 'toString'
// Symbol("noSuchMethod") : MethodMirror on 'noSuchMethod'
// Symbol("runtimeType") : MethodMirror on 'runtimeType'
// Symbol("variable") : Instance of '_SyntheticAccessor'
// Symbol("variable=") : Instance of '_SyntheticAccessor'
// Symbol("constant") : Instance of '_SyntheticAccessor'
// Symbol("_privateVariable") : Instance of '_SyntheticAccessor'
// Symbol("_privateVariable=") : Instance of '_SyntheticAccessor'
// Symbol("method") : MethodMirror on 'method'
// Symbol("accessor") : MethodMirror on 'accessor'
// Symbol("accessor=") : MethodMirror on 'accessor='
```


```cs
class Program
{
    static void Main(string[] args)
    {
        var hoge = new Hoge();
        var members = hoge.GetType().GetMembers();
        foreach (var member in members)
        {
            System.Diagnostics.Debug.WriteLine($"{member.Name} : {member.MemberType}");
        }
    }
}

public class Hoge
{
    // C#ではpublicアクセス修飾子をつけていないと(T).GetMembers()で取得できない
    internal static readonly string CONSTANT = "CONSTANT";
    public static string VARIABLE = "VARIABLE";
    public static void StaticMethod()
    {
        System.Diagnostics.Debug.WriteLine("static method");
    }

    public string variable;
    public readonly string constant;
    public Hoge()
    {
        this.variable = "variable";
        this.constant = "constant";
    }

    public void Method()
    {
        System.Diagnostics.Debug.WriteLine("method called!");
    }

    private string _privateVariable;
    public string PrivateVariable { get => _privateVariable; set => _privateVariable = value; }
}

        // 出力結果
        // StaticMethod : Method
        // Method : Method
        // get_PrivateVariable : Method
        // set_PrivateVariable : Method
        // ToString : Method
        // Equals : Method
        // GetHashCode : Method
        // GetType : Method
        // .ctor : Constructor
        // PrivateVariable : Property
        // variable : Field
        // constant : Field
        // CONSTANT : Field
        // VARIABLE : Field
```


##### 関数

```dart
void main() {
  var one = 1;
  var zero = 0;

  assert(one != 0);
  assert(isZero(one) == false);
  assert(isZeroWithoutAnnotation(one) == false);
  assert(isZeroWithArrorSyntax(zero));
}

// 通常の関数の書き方(型は省略しない方が推奨)
bool isZero(int number) {
  return number == 0;
}

// 戻り値や引数の方は省略可能
isZeroWithoutAnnotation(number){
  return number == 0;
}

// 式が1つだけ含む関数なら、簡単な構文が使用可能
// arrow syntaxと呼ばれる構文らしい。
bool isZeroWithArrorSyntax(int number) => number == 0;
```

```cs
class Program
{
    static void Main(string[] args)
    {
        var one = 1;
        var zero = 0;

        System.Diagnostics.Debug.Assert(one != 0);
        System.Diagnostics.Debug.Assert(IsZero(one) == false);
        // boolでキャストしないとエラーが発生する。
        System.Diagnostics.Debug.Assert((bool)IsZeroWithoutAnnotation(one) == false);
        System.Diagnostics.Debug.Assert(IsZeroWithArrorSyntax(zero));
    }

    static bool IsZero(int number)
    {
        return number == 0;
    }

    // C#はdynamicはで対応
    static dynamic IsZeroWithoutAnnotation(dynamic number)
    {
        return number == 0;
    }

    static bool IsZeroWithArrorSyntax(int number) => number == 0;
}

```

###### オプションのパラメータ

```dart
// クラスの外側にも関数が記述可能。
void globalMethod() {
  print('クラスの外側です。');
}

void main() {
  var sampleClass = SampleClass(); // newは省略可能
  // var sampleClass = new SampleClass(); 上記と同義

  sampleClass.noArgumentMethod(); // => 引数なしです。
  // オプショナル引数は設定しなくてもよい
  sampleClass.optionalNamedParamMethod();  // => オプショナル引数(named)です。 名前: null
  // 設定する場合は名前を明記する必要がある。
  sampleClass.optionalNamedParamMethod(name: "name");  // => オプショナル引数(named)です。 名前: name
  // 下はエラー
  // sampleClass.optionalNamedParamMethod("name");

  // オプショナル設定は指定しなくてもよい
  sampleClass.optionalPositionParamMethod();  // => オプショナル引数(position)です。 名前: null
  // 設定する場合は引数の順番に設定する必要がある。
  sampleClass.optionalPositionParamMethod("name"); // => オプショナル引数(position)です。 名前: name
  // 下はエラー
  // sampleClass.optionalPositionParamMethod(name: "name");

  sampleClass.optionalNamedParamWithDefaultMethod(tmp1: 3);  
      // => デフォルト値付きオプショナル引数(named)です。 namedParam:named tmp1:3
  sampleClass.optionalPositionParamWithDefaultMethod("position"); 
      // => デフォルト値付きオプショナル引数(position)です。 positionParam: position, tmp1:255
}

/// サンプル用クラスです。
class SampleClass {
  void noArgumentMethod() {
    print('引数なしです。');
  }

  void optionalNamedParamMethod({String name}) {
    // dartの2種類あるオプショナル引数の内の1つoptional named parameters。
    // このオプショナル引数を設定する場合は引数名を明記する必要がある。
    // 指定しない場合は、nullが入る。
    print('オプショナル引数(named)です。 名前: $name');
  }

  void optionalPositionParamMethod([String name]) {
    // もう1つのオプショナル引数、optional position parameters。
    // このオプショナル引数を設定する場合は引数の順番に設定する必要がある。
    // 指定しない場合は、nullが入る。
    print('オプショナル引数(position)です。 名前: $name');
  }

  void optionalNamedParamWithDefaultMethod( {String namedParam = 'named param', int tmp1 = -1}) {
    print('デフォルト値付きオプショナル引数(named)です。 namedParam:$namedParam tmp1:$tmp1');
  }

  void optionalPositionParamWithDefaultMethod( [String positionParam = 'position param', int tmp1 = 255]) {
    print('デフォルト値付きオプショナル引数(position)です。 positionParam: $positionParam, tmp1:$tmp1');
  }

  // optional named parametersとoptional position parametersの両方を1つのメソッド内に使用するとエラーが発生する。
  // void optionalBothMethod({String namedParam}, [String positionParam]) {
  //   print('namedとpositionは両方は指定できません。');
  // }
}
```

```cs
    class Program
    {
        static void Main(string[] args)
        {
            // C#の場合は呼び出し側でオプション引数が指定できます。
            // なのでこちらはDartのように、引数名の明記などの制限ができません。
            var sampleClass = new SampleClass();
            sampleClass.NoArgumentMethod();
            sampleClass.OptionalNamedParamMethod();
            sampleClass.OptionalNamedParamMethod(name: "name");
            // sampleClass.OptionalNamedParamMethod("name"); もOK
            sampleClass.OptionalPositionParamMethod();
            sampleClass.OptionalPositionParamMethod("name");
            // sampleClass.OptionalPositionParamMethod(name: "name"); もOK
            sampleClass.OptionalNamedParamWithDefaultMethod(tmp1: 3);
            sampleClass.OptionalPositionParamWithDefaultMethod("position");
        }
    }

    // C#はクラスの外側には書けない。
    //public void GlobalMethod()
    //{
    //    System.Diagnostics.Debug.WriteLine("クラスの外側です。");
    //}

    public class SampleClass
    {
        public void NoArgumentMethod()
        {
            System.Diagnostics.Debug.WriteLine("引数なしです。");
        }

        public void OptionalNamedParamMethod(string name = null)
        {
            System.Diagnostics.Debug.WriteLine($"オプショナル引数(named)です。 名前: {name}");
        }

        public void OptionalPositionParamMethod(string name = null)
        {
            System.Diagnostics.Debug.WriteLine($"オプショナル引数(position)です。 名前: {name}");
        }

        public void OptionalNamedParamWithDefaultMethod(string namedParam = "named param", int tmp1 = -1)
        {
            System.Diagnostics.Debug.WriteLine($"デフォルト値付きオプショナル引数(named)です。 namedParam: {namedParam}, tmp1: {tmp1}");
        }

        public void OptionalPositionParamWithDefaultMethod(string positionParam = "position param", int tmp1 = 255)
        {
            System.Diagnostics.Debug.WriteLine($"デフォルト値付きオプショナル引数(position)です。 positionParam: {positionParam}, tmp1: {tmp1}");
        }
    }
```

######  【Dartのみ】コレクションのデフォルト値付き関数と必須named parameters

dartはコンパイル時定数リストやマップを作成することができるため

リストやマップのデフォルト値を設定した関数を作成することができます。

またDartを使用しているFlutterというフレームワーク(Dart触っている人はだいたい知ってる?)

にも利用されている@requiredアノテーションを使用することでオプショナル引数だが

設定していないよ！と警告を出すことができます。

```dart
// コンパイル時定数リストとマップ
void main() {
  doStuff();
}
// list: [1, 2, 3]
// gists: {first: one, second: two, third: three}

void doStuff(
    {List<int> list = const [1, 2, 3],
    Map<String, String> gists = const {
      'first': 'one',
      'second': 'two',
      'third': 'three',
    }}) {
  print('list: $list');
  print('gists: $gists');
}
```

```dart
// requiredアノテーションを使用する為に必要
import 'package:meta/meta.dart';

void main() {
  // 下記の処理だとageが設定されていないので警告が出る(実行は可能) & コンストラクタのassertでエラー
  // Person person = Person(describe: 'hoge' ); 
  Person person = Person(describe: 'dart', age: 16);
  print(person);
}

class Person{
  int age;
  String describe;

  // 仮引数にthisをつけるとそのままメンバフィールドに突っ込んでくれます。
  Person({@required this.age, this.describe}) : assert(age != null);
}
```

###### main() 関数

Dartはmain()関数がエントリポイントだそうです。(mは小文字)


C#もMain()メソッドです。(Mは大文字)

```dart
void main() {
  print('main()がDartのエントリポイントです。');
}

// 大文字・小文字は区別される模様
void Main(){
  print('Main()がDartのエントリポイントです。');
}

// 出力結果
// main()がDartのエントリポイントです。
```

```cs
class Program
{
    static void Main(string[] args)
    {
        System.Diagnostics.Debug.WriteLine("Main()がC#のエントリポイントです。");
    }

    static void main(string[] args)
    {
        System.Diagnostics.Debug.WriteLine("main()がC#のエントリポイントです。");
    }
}

// 出力結果
// Main()がC#のエントリポイントです。
```

###### 無名関数

別名だとラムダとかクロージャーと呼ばれているものです。

```dart
void main() {
  var list = ['apple', 'banana', 'mikan'];

  // 型は省略しても推論してくれるみたいです。
  list.forEach((item){
    print('${list.indexOf(item)}: $item');
  });

  // 1行なので arror 演算子で以下のようにも記述可能
  // list.forEach((item) => print('${list.indexOf(item)}: $item'));
}

// 出力結果
// 0: apple
// 1: banana
// 2: mikan
```

```cs
using System.Collections.Generic;

class Program
{
    static void Main(string[] args)
    {
        var list = new List<string> { "apple", "banana", "mikan" };
        // C#2.0時代の匿名メソッド式です。
        // C#3.0で追加されたラムダ式が匿名メソッド式の上位互換になっています。
        // 私は短くかける為、基本ラムダ式で記述します。
        list.ForEach(delegate (string item)
        {
            System.Diagnostics.Debug.WriteLine($"{list.IndexOf(item)} : {item}");
        });
        // ラムダ式
        //list.ForEach((item) =>
        //    System.Diagnostics.Debug.WriteLine($"{list.IndexOf(item)} : {item}")
        //);
    }

    // 出力結果
    // 0 : apple
    // 1 : banana
    // 2 : mikan
}

```

###### レキシカルスコープ (Lexical closures)

Dartのスコープはレキシカルスコープだそうです。

レキシカルスコープ(静的スコープ)とはスコープ(変数等の寿命)がコードのレイアウトによって静的に決定できるスコープの事らしいです。

C#やjavascript、python等よく耳にする言語はレキシカルスコープが多い気がします。

```dart
// トップレベルの変数や
bool topLevel = true;

void main() {
  // main()関数内の変数
  var insideMain = true;

  void myFunction() {
    // クロージャー内の変数も
    var insideFunction = true;

    void nestedFunction() {
      // 使用可能
      var insideNestedFunction = true;

      assert(topLevel);
      assert(insideMain);
      assert(insideFunction);
      assert(insideNestedFunction);
    }

    nestedFunction();
  }

  myFunction();
}
```

```cs
class Program
{
    // C#の場合はトップレベルはインスタンスがないためstaticで宣言している。
    // あとはDartと同じ
    static bool topLevel = true;
    static void Main(string[] args)
    {
        var insideMain = true;

        // C#7から追加されたローカル関数
        void myFunction()
        {
            var insideFunction = true;

            void newstedFunction() {
                var indeNestedFunction = true;

                System.Diagnostics.Debug.Assert(topLevel);
                System.Diagnostics.Debug.Assert(insideMain);
                System.Diagnostics.Debug.Assert(insideFunction);
                System.Diagnostics.Debug.Assert(indeNestedFunction);
            };

            newstedFunction();
        }

        myFunction();
    }
}
```

###### レキシカルクロージャー(Lexical closures)

レキシカルクロージャーは無名関数(匿名関数・ラムダ)を関数が元の範囲の外で使用された場合でも、
そのレキシカルスコープ内の変数にアクセスすることができるクロージャの事だそうです。

C#だとラムダ式の変数キャプチャと同じ意味だと思われます。

```dart
Function makeAdder(num addBy) {
  // C#でいうFunc<num, num>()を返している。
  return (num i) => addBy + i;
}

void main() {
  var add2 = makeAdder(2);
  var add4 = makeAdder(4);

  // クロージャー事にaddByが保持されていることがわかる
  assert(add2(3) == 5);
  assert(add4(3) == 7);
}
```

```cs
using System;

class Program
{
    static void Main(string[] args)
    {
        var add2 = MakeAdder(2);
        var add4 = MakeAdder(4);

        System.Diagnostics.Debug.Assert(add2(3) == 5);
        System.Diagnostics.Debug.Assert(add4(3) == 7);
    }

    static Func<int, int> MakeAdder(int addBy)
    {
        return (int i) => addBy + i;
    }
}
```

##### 途中ですが

大分長くなったので、一度ここで区切ろうと思います。

残りの項目は以下の通りです(DartのサイトのLanguage Tourそのままですが・・・)

 * [オペレータ](https://dart.dev/guides/language/language-tour#operators)
 * [制御構文](https://dart.dev/guides/language/language-tour#control-flow-statements)
 * [例外処理](https://dart.dev/guides/language/language-tour#exceptions)
 * [クラス](https://dart.dev/guides/language/language-tour#classes)
 * [ジェネリック](https://dart.dev/guides/language/language-tour#generics)
 * [ライブラリと可視性](https://dart.dev/guides/language/language-tour#libraries-and-visibility)
 * [非同期処理](https://dart.dev/guides/language/language-tour#asynchrony-support)
 * [ジェネレータ](https://dart.dev/guides/language/language-tour#generators)
 * [呼び出し可能クラス(Callable classes)](https://dart.dev/guides/language/language-tour#callable-classes)
 * [Isolate](https://dart.dev/guides/language/language-tour#isolates)
 * [Typedefs](https://dart.dev/guides/language/language-tour#typedefs)
 * [メタデータ](https://dart.dev/guides/language/language-tour#metadata)
 * [コメント](https://dart.dev/guides/language/language-tour#comments)


### 雑感

ほとんど遜色ないコードが書けたので、書いてて抵抗がなかったです。
中にはDartならではの書き方や概念があったりして面白いなと思いました。

個人的にはまだ出てきていないですがコンストラクタの引数をそのままメンバ変数に渡せる機能がいいなと思いました。(2回目)

しつこいようですが、上記のコードはDartは[DartPad](https://dartpad.dartlang.org)、C#は[Try.Net](https://dotnet.microsoft.com/platform/try-dotnet)や[wand box](https://wandbox.org/)等で試すことができます。

不備やここ間違ってるよ等の指摘をして頂けると幸いです。


読んで頂き、ありがとうございました。

### 参考サイト

* [https://ja.wikipedia.org/wiki/Dart:title]
* [https://qiita.com/uehaj/items/7c07f019e05a743d1022:title]
* [https://dev.classmethod.jp/client-side/dart_mirrorsystem/]
* [https://api.dartlang.org/stable/2.4.0/dart-mirrors/ClassMirror-class.html]
* [https://dart.dev/guides/language/language-tour]


