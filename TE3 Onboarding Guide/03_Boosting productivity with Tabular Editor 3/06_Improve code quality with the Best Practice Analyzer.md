---
uid: bpa
title: Improve code quality with the Best Practice Analyzer
author: Daniel Otykier
updated: 2021-11-02
---

# Best Practice Analyzerでコード品質を向上させる

Tabular Object Model (TOM)が比較的複雑なデータ構造であり、さまざまな種類のオブジェクトやプロパティが存在することは、すでにご存じのことと思います。これらのプロパティに割り当てる最適な値は必ずしも明確ではなく、特定のユースケースやモデル設計に依存することも少なくありません。Tabular Editorの**Best Practice Analyzer**は、TOMを継続的にスキャンして、定義できるベストプラクティスルールに違反していないかどうかを確認します。これにより、オブジェクトのプロパティが常に理想的な値に設定されているかどうかを確認ができます。

ベストプラクティス・アナライザーで確認できること

- **DAX Expressions** 特定のDAX関数や構造が使用された場合に警告するルールを作成します。
- **DAX式** 特定のDAX関数や構造が使用された場合に警告するルールを作成します。
- **命名規則** 特定のタイプのオブジェクト（キーカラム、隠しカラムなど）が特定の名前パターンにしたがっているかどうかをチェックするルールを作成します。
- **パフォーマンス** 計算列の数を減らすことを推奨するなど、モデルのさまざまなパフォーマンス関連の側面をチェックするルールを作成します。

Best Practice Analyzerは、モデルの完全なメタデータにアクセスでき、より高度なシナリオのためにVertiPaq Analyzerの統計情報にもアクセスできます。

> [!!! 注意]
> Tabular Editorには、ルールは付属していません。最初に独自のルールを定義するか、[Power BI CAT Teamが推奨するルール](https://powerbi.microsoft.com/en-ca/blog/best-practice-rules-to-improve-your-models-performance/)などの標準的なルールセットを使用する必要があります。

## ベストプラクティスルールの管理

モデルに適用するルールを追加、削除、修正するには、「ツール」→「BPAルールの管理」メニューを使用します。

![Bpa Manager](https://docs.tabulareditor.com/images/bpa-manager.png)

このUIには2つのリストがあります。一番上のリストは、現在読み込まれているルールの**コレクション**を表しています。このリストでコレクションを選択すると、そのコレクション内で定義されているすべてのルールが下のリストに表示されます。モデルが読み込まれると、以下の3つのルールコレクションが表示されます。

- **現在のモデル内のルール**。**Rules within the current model**: その名の通り、これは現在のモデル内で定義されたルールのコレクションです。ルールの定義は、Modelオブジェクトのアノテーションとして保存されています。
- **ローカルユーザーのためのルール**: これは、`%LocalAppData%TabularEditor3\BPARules.json` ファイルに保存されているルールです。これらのルールは、現在ログインしているWindowsユーザーがTabular Editorで読み込んだすべてのモデルに適用されます。
- **ローカルマシン上のルール**: これらのルールは、`%ProgramData%\TabularEditor\BPARules.json`に保存されます。これらのルールは、現在のマシンでTabular Editorに読み込まれているすべてのモデルに適用されます。

同じルール（ID）が複数のコレクションにある場合、優先順位は上から下の順になります。つまり、モデル内で定義されたルールは、ローカルマシンで定義された同じIDのルールよりも優先されます。これにより、モデル固有の規約を考慮するなど、既存のルールを上書きすることができます。

リストの一番上には、「**(Effective rules)**」という特別なコレクションが表示されます。このコレクションを選択すると、現在読み込まれているモデルに実際に適用されるルールのリストが表示され、前述のように、同一のIDを持つルールの優先順位が尊重されます。下段のリストには、ルールがどのコレクションに属しているかが表示されます。また、同様のIDを持つルールがより優先度の高いコレクションに存在する場合、ルールの名前が抹消されていることに気づくでしょう。

![Rule Overrides](https://docs.tabulareditor.com/images/rule-overrides.png)

## コレクションの追加

ルールコレクションは、特定のモデルに追加することができます。ネットワーク共有にルールファイルがある場合、そのファイルをルールコレクションとして現在のモデルに含めることができます。ファイルの場所に書き込み権限があれば、そのファイルからルールの追加・変更・削除を行うことができます。このようにして追加されたルール・コレクションは、モデル内で定義されたルールよりも優先されます。このようなコレクションを複数追加した場合は、それらを上下に移動させて相互の優先順位をコントロールすることができます。

新しいルール・コレクションをモデルに追加するには、「追加...」ボタンをクリックします。これには次のようなオプションがあります。

![Best Practiceルールコレクションの追加](https://docs.tabulareditor.com/images/add-rule-file.png)

- **Create new Rule File**: これにより、指定した場所に空の.jsonファイルが作成され、後からルールを追加することができます。ファイルを選択する際に、相対ファイルパスを使用するオプションがあることに注意してください。これは、ルールファイルを現在のモデルと同じコードリポジトリに保存したい場合に便利です。ただし、相対的なルール ファイルの参照は、モデルがディスクからロードされている場合にのみ機能することに注意してください（Analysis Services のインスタンスからモデルをロードする際には作業ディレクトリが存在しないため）。
- **ローカル・ルール・ファイルを含める**。ルールを含む.jsonファイルがすでにあり、それをモデルに含める場合は、このオプションを使用します。ここでも、相対ファイルパスを使用するオプションがあり、ファイルがモデルのメタデータの近くにある場合には有益です。ファイルがネットワーク共有にある場合（一般的には、現在読み込まれているモデルのメタデータがある場所とは異なるドライブにある場合）、絶対パスを使用してのみ含めることができます。
- **Include Rule File from URL**: このオプションでは、有効なルールのセット（json形式）を返すHTTP/HTTPS URLを指定できます。このオプションは、オンラインソースのルールを含める場合に便利です。例えば、[BestPracticeRules GitHubサイト](https://github.com/TabularEditor/BestPracticeRules)の[standard BPA rules](https://raw.githubusercontent.com/TabularEditor/BestPracticeRules/master/BPARules-standard.json)などがあります。なお、オンラインソースから追加されたルールコレクションは読み取り専用となります。

## コレクション内のルールの変更

画面の下部では、現在選択されているコレクション内のルールの追加、編集、複製、削除を行うことができます。ただし、コレクションが保存されている場所への書き込み権限がある場合に限ります。さらに、[移動先...]ボタンを使用すると、選択したルールを別のコレクションに移動またはコピーすることができ、複数のルールコレクションを簡単に管理できます。

## ルールの追加

新しいルールをコレクションに追加するには、「**新しいルール...**」ボタンをクリックします。これにより、Best Practice Rule Editorが表示されます（以下のスクリーンショットを参照）。

![Bpaルールエディタ](https://docs.tabulareditor.com/images/bpa-rule-editor.png)

新しいルールを作成する際には、以下の詳細を指定する必要があります。

- **Name**: ルールの名前で、Tabular Editorのユーザーに表示されます。
- **ID**: ルールの内部ID。ルールの内部IDです。ルールコレクション内で一意である必要があります。複数のルールが異なるコレクション内で同一のIDを持つ場合、コレクション内でもっとも優先順位の高いルールのみが適用されます。
- **Severity**：重大度。しかし、[Tabular Editorのコマンドラインインターフェイス](xref:command-line-options)を使用してベストプラクティス分析を実行する場合は、この数値によってルール違反の「深刻さ」が決定されます。
  - 1 = 情報のみ
  - 2 = 警告
  - 3 (またはそれ以上) = エラー
- **カテゴリー**。これは、ルールの管理を容易にするために、ルールを論理的にグループ化するために使用されます。
- **Description** (オプション): ルールが何を目的としているかの説明に使用されます。Best Practice Analyzerビューでは、ツールチップとして表示されます。説明欄には以下のプレースホルダー値を使用して、より文脈に沿ったメッセージを提供できます。
  - `object%` は、現在のオブジェクトへの完全修飾された DAX 参照（該当する場合）を返します。
  - `objectname%` は、現在のオブジェクトの名前のみを返します。
  - `%objecttype%` は現在のオブジェクトのタイプを返します。
- **適用されます**。ルールを適用するオブジェクトのタイプを選択します。
- **式**: Dynamic LINQ](<https://dynamic-linq.net/expression-language>)の検索式を入力します。この検索式は、ルールに違反したオブジェクト（**Applies to**ドロップダウンで選択したオブジェクトタイプのうち）について、`true`と評価されます。Dynamic LINQ式は、選択されたオブジェクトタイプで利用可能なTOMプロパティに加えて、幅広い標準的な.NETメソッドとプロパティにアクセスできます。
- **最小の互換性レベル**。一部のTOMプロパティは、すべての互換性レベルで利用できません。汎用ルールを作成する場合は、このドロップダウンを使用して、ルールを適用するモデルの最小互換レベルを指定します。

ルールがディスク上のルールコレクションに保存されると、上記のすべてのプロパティがJSON形式で保存されます。JSONファイルを編集することで、ルールの追加/編集/削除も可能です。また、ルールの`FixExpression`プロパティを指定することもできます。これは、ルール違反を修正するためにモデルに適用される[C#スクリプト](xref:cs-scripts-and-macros)の生成に使用される文字列です。

## ベストプラクティス・アナライザー・ビューの使用

Tabular Editorは、ベストプラクティスのルール違反を**Best Practice Analyzerビュー**に表示します。また、メインウィンドウの下部にあるステータスバーにルール違反の数が表示されます。ビューにフォーカスを当てるには、**View > Best Practice Analyzer**メニューオプションを使用するか、ステータスバーの「# BP issues」ボタンをクリックします。

[ベストプラクティス・アナライザー・ビュー](<https://docs.tabulareditor.com/images/best-practice-analyzer-view.png>)

ベストプラクティスアナライザービュー**には、違反しているオブジェクトがあるすべてのルールのリストが表示されます。各ルールの下には、違反しているオブジェクトのリストが表示されます。リスト内のオブジェクトをダブルクリックすると、**TOM Explorer**でそのオブジェクトに移動することができます。

![アイテムオプション](https://docs.tabulareditor.com/images/bpa-options.png)

オブジェクトを右クリックすると、上図のようなオプションが表示されます。これらは以下の通りです。

- **Go to object**: これは、オブジェクトをダブルクリックして、**TOM Explorer**でそのオブジェクトに移動するのと同じです。
- **Ignore object**: オブジェクトに注釈を追加し、Best Practice Analyzerにそのオブジェクトの特定のルールを無視するように指示します。無視されるルールはIDで指定されます。
- **Generate fix script**：このオプションは、ルールが修正された場合にのみ使用できます。このオプションは、ルールに `FixExpression` プロパティが指定されている場合にのみ使用できます。このオプションを選択すると、Tabular Editorは選択されたルールの `FixExpression` に基づいて新しいC#スクリプトを作成します。
- **修正を適用する**。このオプションは、ルールに `FixExpression` プロパティが指定されている場合にのみ使用できます。このオプションを選択すると、Tabular Editorは選択されたルールの`FixExpression`を実行して、ルール違反を自動的に修正します。

> [!NOTE].
> ベストプラクティス・アナライザー・ビューでは、ShiftキーやCtrlキーを押しながらオブジェクトを複数選択することができます。

上記のオプションは、「Best Practice Analyzer」ビューの上部にあるツールバー・ボタンとしても利用できます。さらに、すべてのアイテムの展開/折りたたみ、無視されたルール/オブジェクトの表示、手動での更新（バックグラウンドスキャンを無効にした場合に必要となります、下記参照）を行うためのボタンも用意されています。

## Best Practice Analyzerの無効化

場合によっては、Best Practice Analyzerのバックグラウンドスキャンを無効にすることができます。例えば、評価に比較的時間がかかるルールがある場合や、非常に大きなモデルを扱っている場合などです。

バックグラウンドスキャンを無効にするには、**Tools > Preferences > Features > Best Practice Analyzer**で、**Scan for Best Practice violations in the background**のチェックを外します。

なお、バックグラウンドスキャンが無効になっていても、前述のように**Best Practice Analyzerビュー**の**Refresh**ボタンを使って手動でスキャンを実行することができます。

## 次のステップ

- cs-scripts-and-macros
- パーソナリゼーション
