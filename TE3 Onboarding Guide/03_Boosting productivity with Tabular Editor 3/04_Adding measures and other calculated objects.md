---
uid: creating-and-testing-dax
title: Adding measures and other calculated objects
author: Daniel Otykier
updated: 2021-10-08
applies_to:
  editions:
    - edition: Desktop
    - edition: Business
    - edition: Enterprise
---

# メジャーやその他の計算オブジェクトの追加

2017年初頭にTabular Editor2.xがリリースされて以来、メジャー全体のDAX式を素早く変更できる機能は、常にこのツールのもっとも人気のある機能となっています。戻る/進むナビゲーション、コピー/ペースト操作、DAX依存性の視覚化、アンドゥ/リドゥのサポートと相まって、このツールは、大規模で複雑なデータモデルを扱う人にとっては複数の小さな変更を素早く行えることが重要であり、常に好ましい選択肢となっています。

この点について、Tabular Editor2.xのユーザーが唯一不満に思っていたのは、DAXコードアシスト機能（「インテリセンス」と呼ばれることもある）がないことでした。とくに、DAXに100％精通していない場合（そんな人はほとんどいません！）、DAXコードエディターが構文や関数のパラメーターなどを覚えておくのをアシストしてくれるのは非常に便利です。

これは、Tabular Editor3で採用された新しいDAXコードエディターがすべてを解決しました。

![複雑なDAX式の編集](https://docs.tabulareditor.com/images/dax-editor screenshot.png)

この記事の残りの部分では、メジャーやその他の計算オブジェクトを作成する方法、およびこれらのオブジェクトのDAX式を修正する方法について説明します。DAXコード・エディターの多くの機能については、<xref:dax-editor>を参照してください。

## メジャーの追加

いくつかのテーブルをモデルに[インポート](xref:importing-tables-data-modeling#importing-new-tables)し、テーブル間の関係を[作成](xref:importing-tables-data-modeling#modifying-relationships-using-the-diagram)したら、ビジネスロジックを含む明示的なメジャーを追加しましょう。

> [!TIP］
> 技術的には、Power BIレポートでデータを視覚化する前に、モデルに明示的なメジャーを追加する必要はありません。しかし、MDXベースのクライアントツール（ExcelやTabular Editor3のPivot Gridなど）は明示的なメジャーを必要とするため、必ず追加することがベストプラクティスです。また、[Calculation-groups](https://docs.microsoft.com/en-us/analysis-services/tabular-models/calculation-groups?view=asallproducts-allversions)は明示的なメジャーにのみ適用されます。

Tabular Editorを使用して新しいメジャーを追加するには、メジャーを追加するテーブルを右クリックして、**Create > Measure** (ALT+1) を選択します。

![新規メジャーの追加](https://docs.tabulareditor.com/images/adding-new-measure.png)

新しい小節を追加すると、その小節の名前が編集可能になります。小節の名前を入力したら、ENTERキーを押してください。名前は後で**Properties**ビューで編集することもできますし、**TOM Explorer**で小節を選択した状態でF2を押すこともできます。

**Expression Editor** ビューを使用して、メジャーのDAX式を入力します。コードを入力すると、DAXエディターがコードの候補を表示したり、構文や意味上のエラーに下線を引いたりすることに注意してください。

![Add Measure Edit Dax](https://docs.tabulareditor.com/images/add-measure-edit-dax.png)

**式エディター**の左上にあるドロップダウン・ボックスは、現在選択されているオブジェクトの異なるDAXプロパティを切り替えるために使用します。たとえば、新しいバージョンのAnalysis Servicesでは、メジャーには [Expression] プロパティのほかに [Detail Rows Expression](https://www.sqlbi.com/articles/controlling-drillthrough-in-excel-pivottables-connected-to-power-bi-or-analysis-services/) があります。他のタイプのオブジェクトは、DAXコードを含む異なるプロパティを持つことができます。たとえば、[KPI](https://docs.microsoft.com/en-us/analysis-services/tabular-models/kpis-ssas-tabular?view=asallproducts-allversions)には3つの異なるDAXプロパティがあります。Tabular EditorでKPIを追加するには、メジャーを右クリックして、**Create > KPI**を選択します。

![Kpisの編集](https://docs.tabulareditor.com/images/editing-kpis.png)

メジャーを非表示にしたい場合は、右クリックして **Make invisible** (CTRL+I) オプションを選択するだけです。同様に、メジャーの非表示を解除するには、**Make visible** (CTRL+U) オプションを選択します。

## その他のメジャーのプロパティ

名前]、[式]、[非表示]プロパティに加えて、**プロパティ**ビューを使用すると、**TOMエクスプローラー**で現在選択されているオブジェクトのすべてのプロパティの値を確認および編集することができます。メジャーでは、ここで `Format String` を設定することなどができます。詳細については、[プロパティビュー](xref:properties-view)を参照してください。

## 計算列の追加

計算列を追加するには、列を追加したいテーブル上で右クリックし、**Create > Calculated Column** (ALT+2)を選択します。カラムに名前を付け、**Expression Editor**を使用して、上記のメジャーの場合と同様に、DAX式を編集します。

>重要
> このオプションは、Power BI Desktopモデルに接続されている場合、デフォルトでは使用できません。これは、[Power BI Desktopがサポートする外部ツールの制限](xref:desktop-limitations)があるためです。詳しくはリンクをクリックしてください。

> [!!! NOTE]について
> 計算列のDAX式が変更された場合、列をレポートで使用する前に、その列が存在するテーブルをリフレッシュする必要があります。詳しくは<xref:refresh-preview-query#refreshing-data>を参照してください。

## 計算されたテーブルの追加

計算テーブルを追加するには、モデルまたは "Tables "フォルダーを右クリックし、**Create > Calculated Table** (ALT+6)を選択します。テーブルに名前を付け、上記のメジャーの場合と同様に、**Expression Editor**を使用してDAX式を編集します。DAX式を変更すると、テーブルの列が自動的に変更されることに注意してください。これは、他のDAX式がテーブルを参照している場合や、列が階層的に使用されている場合に、カスケード効果を引き起こす可能性があります。

> [!!! 重要!!!］
> このオプションは、Power BI Desktopモデルに接続されている場合、デフォルトでは利用できません。これは、[Power BI Desktopがサポートする外部ツールの制限](xref:desktop-limitations)があるためです。詳しくはリンクをクリックしてください。

> [!!! NOTE]について
> 計算テーブルのDAX式を変更した場合、レポートで使用する前にテーブルをリフレッシュする必要があります。詳しくは、<xref:refresh-preview-query#refreshing-data>を参照してください。

## Calculation-groupの追加

計算グループ](https://docs.microsoft.com/en-us/analysis-services/tabular-models/calculation-groups?view=asallproducts-allversions)を追加するには、モデルまたは "Tables"フォルダーを右クリックして、**Create > Calculation Group** (ALT+7)を選択します。計算グループに名前を付けます。また、デフォルトの**Name**列には別の名前を検討してください。

>重要
> このオプションは、互換性レベル1500以上のモデルでのみ使用できます。

計算項目を追加するには、新しく作成した計算グループを右クリックして、**Create > Calculation Item**を選択します。計算項目に名前を付け、上記のメジャーの場合と同様に**Expression Editor**を使用してDAX式を編集します。

計算項目の表示順は、TOMエクスプローラーでドラッグするか、**プロパティ**ビューで`Ordinal`プロパティを設定することで変更できます。

>注意
> 計算アイテムを計算グループに追加、名前変更、削除した場合、レポートで使用する前に計算グループをリフレッシュする必要があります。詳細は、<xref:refresh-preview-query#refreshing-data>を参照してください。

## 一般的なモデリング操作

### コピー/ペースト

TOM ExplorerのすべてのオブジェクトはTabular Editorでコピー＆ペーストできます。また、Tabular Editorの異なるインスタンス間、さらにはTabular Editor2.xとTabular Editor3の間でもコピー＆ペーストが可能です。おなじみのキーボードショートカットを使うことができます。

- エディット > コピー** (CTRL+C)
- エディット＞カット＞（CTRL+X）
- エディット > ペースト** (CTRL+V)

> [! TIP].
> あるテーブルを別のテーブルに置き換えたい場合は、テーブルをクリップボードにコピーしてから、TOMエクスプローラーで置き換えたいテーブルを選択してペーストしてください。選択したテーブルをクリップボードにあるテーブルに置き換えるかどうかの確認が表示されます。

## アンドゥ/リドゥ

Tabular Editorでオブジェクトやプロパティに変更が加えられると、変更の完全な履歴が追跡され、加えられたすべての変更を取り消すことができます。おなじみのキーボードショートカットが使えます。

- 編集」→「元に戻す」** (CTRL+Z)
- 編集」→「やり直し」**（CTRL+Y）

> [!!! 注意]
> Tabular Editor3のすべてのテキストエディターは、独自のアンドゥ/リドゥ履歴を持っているので、カーソルが現在テキストエディター内にある場合、キーボードショートカットはそのエディター内の入力をアンドゥ/リドゥします。編集**メニューのオプションを使用してモデルレベルでアンドゥ/リドゥを実行したり、ユーザーインターフェースの他の要素(TOM Explorerなど)をクリックして現在のテキストエディターを非アクティブにすることができます。

## ナビゲーション

DAXエディターのオブジェクト参照にカーソルがあるとき、右クリックして**Go to definition**(F12)を選択すると、そのオブジェクトに素早くジャンプします。もちろん、TOMエクスプローラーを使ってオブジェクト間を移動することもできます。

式エディター**の右上にある矢印ボタンを使って、訪問したオブジェクト間を素早く行き来ができます。

## DAX依存関係

オブジェクト間のDAX依存関係を表示するには、**TOMエクスプローラー**でオブジェクトを選択し、右クリックして**Show dependencies**（SHIFT+F12）を選択します。これにより、選択したオブジェクトの依存関係（両方向）を表示するウィンドウが開きます。このウィンドウ内のオブジェクトをダブルクリックすると、そのオブジェクトにすばやく移動できます。

![DaxDependencies And Tom Explorer](https://docs.tabulareditor.com/images/dax-dependencies-and-tom-explorer.png)

## フォルダーの表示

モデルにかなりの数のメジャーが増えてきたら、表示フォルダーを使って整理するのが良い方法です。タブラエディターで表示フォルダーを作成するには、**プロパティ**ビューで`Display Folder`プロパティを編集するか、メジャーを右クリックして**Create > Display Folder**オプションを選択します。

また、表示フォルダー間でオブジェクトをカット/コピー/ペーストしたり、ドラッグ＆ドロップすることもできます。

## 次のステップ

- @dax-script-introduction
- @bpa
- Csスクリプトとマクロ