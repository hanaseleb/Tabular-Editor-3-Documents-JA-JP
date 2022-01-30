---
uid: refresh-preview-query
title: Refreshing, previewing and querying data
author: Daniel Otykier
updated: 2021-09-30
applies_to:
  editions:
    - edition: Desktop
      partial: Refreshing tables through external tools is currently not supported by Power BI Desktop, even though Tabular Editor 3 Desktop Edition allows this operation. Querying data is fully supported.
    - edition: Business
    - edition: Enterprise
---

# データのリフレッシュ、プレビュー、クエリの実行

Tabular Editor 3がAnalysis Servicesのインスタンスに接続されている場合、いくつかの追加の**接続機能**が利用でき、Tabular Editor 3をAnalysis Servicesのクライアントツールとして使用することができます。

> [!!! 注意]
> 「Analysis Servicesのインスタンスに接続されている」とは、以下のいずれかを意味します。
>
> - **ワークスペースモード**でのモデルの読み込み(xref:workspace-mode)
> - SQL Server Analysis Services、Azure Analysis Services、またはPower BI XMLAエンドポイントから直接モデルをロードする。
> - Power BI Desktopの外部ツールとしてTabular Editor 3を使用する

要約すると、これらの連携機能は以下の通りです。

- データ更新操作
- テーブルデータのプレビュー
- ピボットグリッド(PivotGrids)
- DAXクエリー
- VertiPaqアナライザー

## ♪データのリフレッシュ

Tabular Editorは、データモデルに変更が加えられても、Analysis Servicesのリフレッシュ操作を自動的にトリガーしません。これは、メタデータの変更をAnalysis Servicesに保存する際、時間がかかりすぎないようにするための設計です。リフレッシュ操作が完了するまでに長い時間がかかり、その間はサーバー上で追加のメタデータは更新されない可能性があります。もちろん、Tabular Editorを使用して変更を行うと、モデルが部分的にしかクエリできない状態になったり、まったくクエリできない状態になったりするという難点があります。どのようなタイプのデータモデル変更が行われたかによって、異なるレベルのリフレッシュが必要になる場合があります。

一般的には、以下のような変更を行った場合、そのオブジェクトをクエリできるようにする前、完全なリフレッシュが必要となります（つまり、データのリフレッシュの後に計算のリフレッシュが必要となります）。

- モデルに新しいテーブルを追加する
- テーブルへの新しいカラムの追加

一般的に、以下の変更は計算の更新を必要とします。

- 計算テーブルや計算カラムのDAX式の変更
- リレーションシップの追加または修正
- 計算グループへの計算項目の追加、名前の変更、削除

なお、モデルからメジャーを追加、変更、または削除しても、いかなるタイプのリフレッシュも必要ありません (メジャーが計算列によって参照されている場合、その列が存在するテーブルを再計算する必要がある場合を除く)。

Tabular Editorを使ってリフレッシュを行うには、リフレッシュしたいテーブルまたはパーティションを右クリックして、**テーブルのリフレッシュ**または**パーティションのリフレッシュ**に移動し、実行するリフレッシュのタイプを選択します。

![テーブルの更新](https://docs.tabulareditor.com/images/refresh-table.png)

また、モデルレベルでのリフレッシュは、**モデル > モデルのリフレッシュ**メニューで行うことができます。リフレッシュ操作が開始されると、"Data refresh started... "というテキストが表示されます。<ins>View refresh queue</ins>というテキストが表示されます。このリンクをクリックするか、**View > Data refresh**メニューで**Data refresh**ビューを表示します。このビューでは、すべてのリフレッシュ操作（現在および過去のもの）のリストが表示され、Analysis Servicesから返されたステータスメッセージ（進捗カウンタおよび期間を含む）が表示され、意図しないリフレッシュをキャンセルすることができます。

![データ リフレッシュ ビュー2](https://docs.tabulareditor.com/images/data-refresh-view2.png)

リフレッシュの進行中も、データモデルの作業を継続したり、データのクエリやプレビューを行ったり、新しいデータリフレッシュ操作をキューに入れたりすることができます（本記事参照）。ただし、すべてのデータ リフレッシュ操作が完了するまでは、モデルの変更を Analysis Services に保存することはできません。

> [!!! 注意]
> 現在、[Power BI Desktopは、外部ツールからトリガーされるリフレッシュ操作をサポートしていません。](https://docs.microsoft.com/en-us/power-bi/transform-model/desktop-external-tools#data-modeling-operations) このため、Tabular Editor 3では、Power BI Desktopのインスタンスに接続すると、これらのオプションが非表示になります。この動作を無効にするには、**ツール > 環境設定 > サポートされていないモデリング操作を許可**を有効にします。

## サポートされるリフレッシュ操作

Tabular Editor 3は、さまざまなオブジェクトタイプのリフレッシュ操作をサポートしています。サポートされているリフレッシュの種類は以下の通りです。

- **モデル** (Automatic, calculate, full)
- **(Imported) Table** (Automatic, calculate, data only, full)
- **パーティション** (full)
- **Calculated Table** (Calculate)
- **Calculation Group** (Calculate)

Analysis Services / Power BIがサポートするリフレッシュ操作の種類については、[リフレッシュの種類](https://docs.microsoft.com/en-us/analysis-services/tmsl/refresh-command-tmsl?view=asallproducts-allversions#request)を参照してください。

## テーブルデータのプレビュー

DAXのオーサリングやデータモデルの開発では、表の内容を行単位で確認する必要がある場合があります。もちろん、DAXクエリを書いてこれを実現することもできますが、Tabular Editor 3ではテーブルデータを直接プレビューできるので、さらに簡単です。これを行うには、テーブルを右クリックして、**データのプレビュー**オプションを選択します。

![データのプレビュー](https://docs.tabulareditor.com/images/preview-data-big.png)

このようなテーブルのプレビューは複数開くことができ、ユーザーインターフェイス上で好きなように配置することができます。また、各列のソートやフィルタリングも可能です。プレビューできる行の数に実用上の制限はありません。Tabular Editorは、モデルに対して[`TOPNSKIP`](https://dax.guide/topnskip)DAXクエリを実行するだけで、現在のビューを埋めるのに適した少数のレコードを返すことができます。

1つ以上の計算列が無効な状態にある場合、それらの列には *(Calculation needed)* というテキストが表示されます。テーブルを再計算するには、列を右クリックして **テーブルを再計算...** オプションを選択します。

![テーブルの再計算](https://docs.tabulareditor.com/images/recalculate-table.png)

## ピボット・グリッド

モデルにDAXメジャーを追加または編集した後、モデル開発者はこれらのメジャーをテストするのが一般的です。従来、この作業はExcelやPower BIなどのクライアントツールを使って行うのが一般的でした。Tabular Editor 3では、Excelの有名なピボットテーブルのように動作する**ピボットグリッド**を使用できるようになりました。ピボットグリッドを使用すると、モデル内のデータの要約ビューをすばやく作成でき、さまざまな列や階層でフィルタリングやスライスしたときのDAXメジャーの動作をテストすることができます。

新しいピボット・グリッドを作成するには、"File" > "New" > "Pivot Grid" オプションを使用します。ここから、TOMエクスプローラからメジャー、列、階層をグリッドにドラッグするか、**Pivot Grid > Show fields**メニュー・オプションを使用して、ピボット・グリッドにドラッグできるすべてのフィールドのポップアップ・リストを表示することができます（以下のスクリーンショットを参照）。

![Show Fields Pivot](https://docs.tabulareditor.com/images/show-fields-pivot.png)

ピボット・グリッドにフィールドがドラッグされると、Tabular Editor は MDX クエリを生成して Analysis Services に送信し、結果のデータを表示します。この点については、Excel のピボット・テーブルと非常によく似た動作です。ピボット・グリッドのフィールドはドラッグ・アンド・ドロップで再配置でき、データの表示方法をカスタマイズするためのさまざまな右クリック・メニュー・オプションも用意されています。

![ピボット・グリッドのカスタマイズ](https://docs.tabulareditor.com/images/customizing-pivot-grids.png)

ピボット・グリッドは、モデルに変更が加えられたり、更新操作が終了したりすると、自動的にリフレッシュされます。この自動更新機能は、**ピボットグリッド**メニューで切り替えることができます。

## DAXクエリ

モデル内のデータをより直接的に照会するには、DAXクエリを作成します。**ファイル > 新規作成 > DAXクエリ**メニューオプションを使用して、新しいDAXクエリドキュメントを作成します。同時に複数のDAXクエリ・ドキュメントを開くことができます。

DAXクエリは、`.dax`または`.msdax`というファイル拡張子を持つスタンドアロンファイルとの間で保存および読み込みが可能です。詳細は @supported-files を参照してください。

DAXの`EVALUATE`クエリをエディタに入力し、**Query > Execute**（F5）を押すと、Analysis Servicesにクエリが送信され、結果が表示されます。デフォルトでは、Tabular Editor 3はAnalysis Servicesから返される行数を1000行に制限していますが、これは**Tools > Preferences > Data Browsing > DAX Query**で変更できます。クエリがこの制限を超えると、Tabular Editor 3はすべてのレコードを取得できるショートカットを表示します（以下のスクリーンショットを参照）。

![クエリの行数制限](https://docs.tabulareditor.com/images/query-rowset-limit.png)

> [!!! WARNING] (警告)
> クエリ結果ウィンドウに大量のレコードを表示すると、時間がかかり、Tabular Editor 3が消費するメモリが大幅に増加する可能性があります。

Tabular Editor 3では、クエリの編集に、オブジェクトのDAX式の定義と同じDAXコードエディターを使用します。そのため、コードコンプリート、自動フォーマットなどに関するすべての機能が利用できます。詳細は @dax-editorを参照してください。さらに、DAXクエリはオブジェクト式とは若干異なる構文を持っているため、DAXクエリ・エディターには、一般的な作業のためのオプションがいくつか用意されています。

たとえば、メジャーの参照を右クリックすると、以下のスクリーンショットにあるように、**Define measure**というオプションがあります。このオプションを使用すると、DAXクエリの先頭に「DEFINE MEASURE」ステートメントが追加され、クエリの範囲内でそのメジャーのDAX式を簡単に変更できます。

![DAXクエリの機能](https://docs.tabulareditor.com/images/dax-query-features.png)

さらに、DAX クエリには複数の `EVALUATE` ステートメントを含めることができます。そのような場合、Tabular Editor 3はそのようなステートメントの結果をそれぞれ別の番号の付いたタブに表示します。ドキュメントに複数の`EVALUATE`文が含まれていても、単一の`EVALUATE`文だけを実行したい場合は、実行したい文のどこかにカーソルを置いて、**Query > Execute selection** (SHIFT+F5)オプションを使用することができます。

Tabular Editor 3のDAXクエリは、モデルに変更が加えられたときや、リフレッシュ操作が終了したときに自動的にリフレッシュされます。この自動更新機能は、**Query**メニュー内で切り替えることができます。

## なりすまし（Impersonation）

モデル内のデータを照会する際に、特定のユーザや複数のロールの組み合わせになりすまして、エンドユーザの視点からモデルの動作を確認できると便利な場合があります。Tabular Editor 3では、**Impersonate...** ボタンをクリックすることで、特定のユーザまたは1つ以上のロールになりすますことができます。これは、[テーブルのプレビュー](#previewing-table-data)、[ピボットグリッド](#pivot-grids)、[DAXクエリ](#dax-queries)に適用されます。

> [!!! 注意]
> ユーザーになりすますには、Analysis Servicesへの接続時に、Tabular Editorが[`EffectiveUserName`プロパティ](https://docs.microsoft.com/en-us/analysis-services/instances/connection-string-properties-analysis-services?view=asallproducts-allversions#effectiveusername)を接続文字列に追加します。ロールを偽装するには、Tabular Editorは[`Roles`プロパティ](https://docs.microsoft.com/en-us/analysis-services/instances/connection-string-properties-analysis-services?view=asallproducts-allversions#roles)を接続文字列に追加します。これは、なりすましを指定したデータビュー（DAXクエリ、ピボットグリッド、テーブルプレビューなど）にのみ適用されます。

**Impersonation...** ボタンをクリックすると（データビューの種類に応じて、**Query**、**Pivot Grid**、**Table Preview**のいずれかのメニューに表示されます）、ポップアップでユーザーを指定したり、1つまたは複数のロールを選択したりできます。

![Select Impersonation](https://docs.tabulareditor.com/images/select-impersonation.png)

インパーソネーションを有効にすると、**Impersonation...** ボタンがチェックされ、現在のデータビューにインパーソネーションが適用されるようになります。**Impersonation...** ボタンの横にある小さな矢印をクリックすると、最近使用された10個のなりすましを表示し、素早く切り替えることができます。

![なりすましドロップダウン](https://docs.tabulareditor.com/images/impersonation-dropdown.png)

データビューで自動更新が有効になっている場合、なりすましを変更するとすぐにビューが更新されます。

## VertiPaq Analyzer

Tabular Editor 3には、[SQLBI](https://sqlbi.com)によって作成されたオープンソースの[VertiPaq Analyzer](https://www.sqlbi.com/tools/vertipaq-analyzer/)ツールのバージョンが含まれています。VertiPaq Analyzerは、Power BIやTabularデータモデルのVertiPaqストレージ構造を分析するのに便利です。

Tabular Editor 3では、Analysis Servicesの任意のインスタンスに接続している間、VertiPaq Analyzerの統計情報を収集できます。また、統計情報を[.vpaxファイル](https://www.youtube.com/watch?v=zRa9y01Ub30)としてエクスポートしたり、.vpaxファイルから統計情報をインポートしたりすることもできます。

統計情報を収集するには、**VertiPaq Analyzer**ビューの**Collect stats**ボタンを押すだけです。

![Vertipaq Analyzer Collect Stats](https://docs.tabulareditor.com/images/vertipaq-analyzer-collect-stats.png)

統計情報が収集されると、VertiPaq Analyzerはモデルサイズ、テーブル数などの概要を表示します。より詳細な統計情報は、**Tables**、**Columns**、**Relationships**、**Partitions**のタブで確認できます。

また、統計情報が読み込まれると、Tabular Editor 3は、TOM Explorerのオブジェクトにマウスカーソルを置くとカーディナリティとサイズの情報をツールチップとして表示します。

![Vertipaq Analyzer Stats in TOM Explorer](https://docs.tabulareditor.com/images/vertipaq-analyzer-stats.png)

またはDAX式のオブジェクト参照の上にマウスカーソルを置いた場合は以下のようになります。

![Vertipaq Analyzer Stats in a DAX expression](https://docs.tabulareditor.com/images/vertipaq-analyzer-stats-dax.png)

# 次のステップ

- DAXの作成とテスト
