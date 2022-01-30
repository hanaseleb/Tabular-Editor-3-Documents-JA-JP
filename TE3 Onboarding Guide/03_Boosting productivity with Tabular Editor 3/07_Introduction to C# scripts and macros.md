---
uid: cs-scripts-and-macros
title: Introduction to C# scripts and macros
author: Daniel Otykier
updated: 2021-11-03
---

# C#スクリプトとマクロの紹介

生産性を向上させると謳っているソフトウェアは、ユーザーのインタラクションを **自動化** する手段を提供するべきです。タブラー・エディターでは、まさにこの目的のためにC#スクリプトを書くことができます。タブラー・エディターのC#スクリプトを使用すると、例えば以下のことができます。

- メジャー、テーブル、計算項目などのTOMオブジェクトの作成の自動化
- TOMエクスプローラーで現在選択されているオブジェクトを操作する
- 複数のオブジェクトにプロパティを自動的に割り当てる
- 監査や文書化のために、さまざまな形式でメタデータをインポートおよびエクスポートする

スクリプトがモデルのメタデータを変更した場合、TOMエクスプローラーとプロパティビューで変更内容をすぐに確認ができます。さらに、**スクリプトの変更を取り消す**ことができ、効果的にモデルメタデータをスクリプトが実行される前の時点にロールバックができます。スクリプトの実行に失敗した場合は、デフォルトで変更内容が自動的にロールバックされます。

Tabular Editor 3にはシンプルな**script recorder**があり、モデルに変更を加える際にスクリプトコードの行を少しずつ追加することで、使用する構文を学ぶことができます。

スクリプトはスタンドアロンファイル（拡張子:`.csx`）として保存でき、Tabular Editorのユーザー間で共有できます。さらに、スクリプトは再利用可能な**マクロ**として保存することができ、スクリプトをTabular Editorのユーザーインターフェイスとより密接に統合できます。

## スクリプトの作成

新しいC#スクリプトを作成するには、**File > New > C# Script**メニューオプションを使用します。なお、このオプションは、Tabular Editorにモデルが読み込まれていない場合でも利用できます。

最初のスクリプトには、以下のコードを入力します。

```csharp
Info("Hello world!");
```

F5を押して、コードを実行します。

![あなたの最初のスクリプト](https://docs.tabulareditor.com/images/first-script.png)

コードを入力するときに間違えた場合は、シンタックスエラーが **メッセージビュー** に表示されます。

- スクリプトをファイルとして保存するには、**File > Save** (Ctrl+S)をクリックします。
- ファイルからスクリプトを開くには、 **ファイル > 開く > ファイル...** (Ctrl+O)を使用します。ファイルを開く」ダイアログは、デフォルトでは拡張子が `.cs` または `.csx` のファイルを探します。

## スクリプトレコーダーの使用

C#スクリプトがフォーカスされている間、Tabular Editorの**C# Script > Record script**メニューオプションを使ってスクリプトレコーダーを起動ができます。スクリプトが記録されている間、モデルのメタデータに変更を加えると、スクリプトにコードの行が追加されます。なお、記録を停止するまでは、スクリプトを手動で編集することはできません。

![Csharp Script Recorder](https://docs.tabulareditor.com/images/csharp-script-recorder.png)

## モデルのメタデータにアクセスする

現在読み込まれているモデル内の特定のオブジェクトにアクセスするには、Tabular Object Model (TOM)階層をナビゲートするC#構文を使用する必要があります。この階層のルートは`Model`オブジェクトです。

以下のスクリプトは、現在ロードされているモデルの名前を出力します。モデルがロードされていない場合は、警告が表示されます。

```csharp
if(Model != null)
    Info("The name of the current model is: " + Model.Name);
else
    Warning("No model is currently loaded!");
```

Model`オブジェクトは、[Microsoft.AnalysisServices.Tabular.Model](https://msdn.microsoft.com/en-us/library/microsoft.analysisservices.tabular.model.aspx)クラスのラッパーであり、そのプロパティのサブセットを公開し、利便性のためにいくつかのメソッドとプロパティを追加しています。

特定のメジャーにアクセスするには、そのメジャーの名前と、メジャーが存在するテーブルの名前を知る必要があります。

```csharp
var myMeasure = Model.Tables["Internet Sales"].Measures["Internet Total Sales"];
myMeasure.Description = "The formula for this measure is: " + myMeasure.Expression;
```

上記のスクリプトの1行目では、「インターネット売上」テーブル上の「インターネット合計売上」メジャーを探し、そのメジャーへの参照を `myMeasure` 変数に格納しています。

スクリプトの2行目では、ハードコードされた文字列とメジャーの(DAX)式に基づいて、メジャーの説明を設定しています。

Tabular Editorでは、TOMエクスプローラーからC#スクリプトビューにオブジェクトをドラッグ＆ドロップすることで、特定のオブジェクトを参照するコードを自動生成ができます。

![ドラッグによるオブジェクト参照の生成](https://docs.tabulareditor.com/images/generate-csharp-code.gif)

Tabular EditorのほとんどのTOMオブジェクト(テーブル、カラム、メジャーなど)は、AMO/TOMクライアントライブラリを直接使用したときに利用できるのと同じプロパティのセットを公開しています。このため、[MicrosoftのAMO/TOMドキュメント](https://docs.microsoft.com/en-us/dotnet/api/microsoft.analysisservices.tabular?view=analysisservices-dotnet)を参照して、どのプロパティが利用できるかを知ることができます。たとえば、[ここ](https://docs.microsoft.com/en-us/dotnet/api/microsoft.analysisservices.tabular.measure?view=analysisservices-dotnet#properties)には、利用可能なメジャーのプロパティに関するドキュメントがあります。

## 現在のTOM Explorerの選択にアクセスする

スクリプトを再利用するためには、上記のようにモデル内のオブジェクトを名前で直接参照できるだけでは不十分です。代わりに、Tabular Editorの**TOM Explorerビュー**で現在選択されているオブジェクトを参照すると便利です。これは、`Selected`オブジェクトを使うことで可能になります。

```csharp
Info("You have currently selected: " + Selected.Measures.Count + " measure(s).");
```

`Selected`オブジェクトはそれ自体、現在選択されているすべてのオブジェクトのコレクションで、選択された表示フォルダー内のオブジェクトも含まれます。さらに、`Selected` オブジェクトには複数のプロパティが含まれており、上記の例で示した `.Measures`プロパティのように、特定のオブジェクトタイプを簡単に参照できます。一般に、これらのプロパティは複数形（`.Measures`）と単数形（`.Measure`）の両方が存在します。前者は、反復処理が可能なコレクションで、現在の選択範囲にそのタイプのオブジェクトが含まれていない場合は空になります。

useful-script-snippetsには、さまざまなタスクを実行するために `Selected` オブジェクトを使用するスクリプトの例が多数掲載されています。

## ユーザーとのインタラクション

上の例では、`Info(...)` と `Warning(...)` のグローバルメソッドを使って、さまざまなフレーバーでユーザーにメッセージを表示しました。タブラー・エディターには、これらのグローバルメソッドの他に、情報を表示したり収集するための拡張メソッドや、その他のさまざまな一般的な作業のためのメソッドが用意されています。もっともよく使われるものを以下に示します。

- `void Output(object value)` - スクリプトの実行を停止し、提供されたオブジェクトの詳細情報を表示します。提供されたオブジェクトがTOMオブジェクトまたはTOMオブジェクトのコレクションである場合、すべてのプロパティの詳細なビューが表示されます。
- `void SaveFile(string filePath, string content)` - テキストデータをファイルに保存する便利な方法です。
- `string ReadFile(string filePath)` - ファイルからテキストデータをロードする便利な方法です。
- `string ExportProperties(IEnumerable<ITabularNamedObject> objects, string properties = "...")` - 複数のオブジェクトからプロパティのセットをTSV文字列としてエクスポートする便利な方法です。
- `void ImportProperties(string tsvData)` - TSV文字列から複数のオブジェクトにプロパティをロードする便利な方法です。
- `string ConvertDax(dax, useSemicolons)` - DAX式をUS/UKと非US/UKのロケール間で変換します。useSemicolons`がtrue（デフォルト）の場合、`dax`文字列はネイティブのUS/UKフォーマットから非US/UKフォーマットに変換されます。つまり、カンマ（リストの区切り文字）はセミコロンに、ピリオド（小数点の区切り文字）はカンマに変換されます。useSemicolons`がfalseに設定されている場合はその逆になります。
- `void FormatDax(IEnumerable<IDaxDependantObject> objects, bool shortFormat, bool? skipSpace)` - 与えられたコレクション内のすべてのオブジェクトに対してDAX式をフォーマットします。
- `void FormatDax(IDaxDependantObject obj)` - スクリプトの実行が完了したとき、または `CallDaxFormatter` メソッドが呼ばれたときに、DAX式のフォーマッティングのためにオブジェクトをキューに入れます。
- `void CallDaxFormatter(bool shortFormat, bool? skipSpace)` - これまでにキューイングされたオブジェクト上のすべてのDAX式をフォーマットします。
- `void Info(string message)` - 情報提供のメッセージを表示します。
- `void Warning(string message)` - 警告メッセージを表示します。
- `void Error(string message)` - エラーメッセージを表示します。
- `measure SelectMeasure(Measure preselect = null, string label = "...")` - すべてのメジャーのリストを表示し、ユーザーに選択を促します。
- `T SelectObject<T>(this IEnumerable<T> objects, T preselect = null, string label = "...") where T: TabularNamedObject` - 提供されたオブジェクトのリストを表示し、ユーザーに1つを選択するように促し、そのオブジェクトを返します（キャンセルボタンが押された場合は、NULLを返します）。
- `IList<T> SelectObjects<T>(this IEnumerable<T> objects, IEnumerable<T> preselect = null, string label = "...") ここで、T: TabularNamedObject` - 与えられたオブジェクトのリストを表示し、ユーザーに任意の数のオブジェクトを選択するように促し、選択されたオブジェクトのリスト（キャンセルボタンが押された場合はnull）を返します。

## スクリプトをマクロとして保存する

よく使うスクリプトは、再利用可能なマクロとして保存することができ、Tabular Editorの起動時に常に利用可能です。さらに、マクロは**TOM Explorerビュー**のコンテキストメニューに自動的に統合され、**Tools > Customize...** オプションを使って、既存またはカスタムのメニューやツールバーにマクロを追加することもできます。

スクリプトをマクロとして保存するには、**C#スクリプト > マクロとして保存...** オプションを使用します。

![新規マクロの保存](https://docs.tabulareditor.com/images/save-new-macro.png)

マクロの名前を指定します。例えば、"My Macros\Test "のような名前は、TOMエクスプローラーのコンテキストメニューに "My Macros "サブメニューを作成し、このサブメニューの中にスクリプトを起動する "Test "メニューオプションがあります。

また、マクロで作成されたメニューオプションにカーソルを合わせたときに表示されるツールチップをオプションで指定することもできます。

また、マクロのコンテキストを指定する必要があります。これは、マクロがコンテキストメニューで利用できるようにするため、選択する必要のあるオブジェクトの種類を指定します。

最後に、**マクロの有効条件（詳細）**で、真/偽を評価するC#式を指定することができます（通常は、`Selected`または`Model`オブジェクトに基づいて評価します）。これにより、現在の選択範囲に基づいて、マクロを有効にするかどうかをより詳細に制御できます。たとえば以下のような表現が考えられます。

```csharp
Selected.Measures.Count == 1
```

を使うと、ちょうど1つのメジャーが選択されたときにだけマクロを有効にできます。

## マクロの管理

以前に保存したすべてのマクロは、**マクロビュー**で見ることができます。このビューを表示するには、**View > Macros**メニューオプションを使用します。このビューでは以下のことができます。

- **マクロの名前を変更する**（**名前**列にカーソルを置き、新しい名前を入力するだけ）
- マクロを選択して、マクロリストの上にある赤い "X"ボタンをクリックします。
- **マクロの編集：** リストの中のマクロをダブルクリックします（リストの「Id」列をダブルクリックします）。これにより、マクロが新しいC#スクリプトビューで開かれ、コードを変更できます。Ctrl+Sを押すと、コードの変更が保存されます。他のマクロのプロパティ（ツールチップ、マクロのコンテキストなど）を編集する必要がある場合は、**C#スクリプト > マクロの編集...** メニューオプションを使用します。

## 次のステップ

- パーソナライゼーション
- 生産性向上のために

## 続きを読む

- Csharpスクリプト
- 役に立つスクリプトスニペット
