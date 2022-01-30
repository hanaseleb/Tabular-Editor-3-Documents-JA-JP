---
uid: general-introduction
title: General introduction and architecture
author: Daniel Otykier
updated: 2021-09-30
---

# 一般的な紹介とアーキテクチャ

Tabular Editorは、タブラーモデルを開発するためのWindowsデスクトップアプリケーションです。具体的には、Tabular Object Model (TOM) のメタデータを編集できます。このツールは、ファイルまたは既存のAnalysis ServicesデータベースからTOMメタデータを読み込むことができ、更新されたTOMメタデータをAnalysis Servicesに展開することもできます。

> [!!! 注意]
> Analysis Services TabularはPower BIで使用されるデータモデルエンジンであるため、Analysis Services TabularモデルとPower BIデータセットの両方を表すために**tabularモデル**という用語を使用しています。同様に、**Analysis Services**という用語を使用する場合、SQL Server Analysis Services、Power BI Desktop、Power BI Service XMLA Endpointなどの「Analysis Servicesのあらゆるインスタンス」を意味しています。

## Tabular Object Model (TOM) metadata

データモデルは、いくつかのテーブルによって構成されています。各テーブルには1つまたは複数の列があり、テーブルにはメジャーや階層が含まれることもあります。通常、データモデルには、テーブル間の関係、接続の詳細を含むデータソース、データを読み込むためのデータソース式（SQLまたはMクエリ）を含むテーブルパーティションなども定義されています。これらの情報は総称して**モデルのメタデータ**と呼ばれ、**Tabular Object Model：TOM**と呼ばれるJSONベースのフォーマットで保存されます。

- 表計算モデルをVisual Studioで作成した場合、TOMのメタデータを表すJSONは**Model.bim**というファイルに格納されます。
- Power BI Desktopを使用してデータモデルを作成した場合、TOMメタデータは`.pbix`または`.pbit`ファイル内に埋め込まれます（このファイル形式には、ビジュアルやブックマークの定義など、データモデル自体とは関係のない詳細情報も多く含まれているため）。

[AMO/TOM](https://docs.microsoft.com/en-us/analysis-services/tom/introduction-to-the-tabular-object-model-tom-in-analysis-services-amo?view=asallproducts-allversions)というクライアントライブラリを使うことで、Tabular> EditorはこのJSONベースのフォーマットとの間でメタデータをロードしたりセーブができます。さらに、クライアントライブラリを使用すると、Tabular EditorをAnalysis Servicesの任意のインスタンスに直接接続して、既存のデータベースからモデルのメタデータを取得できます。これを以下の図に示します。

![アーキテクチャ](https://docs.tabulareditor.com/images/architecture.png)

> [!!! NOTE].
> 上の段落では、Analysis Services にデプロイされたモデルを表すために **database** という用語を使用しました。Power BI Serviceでは、同じもの、つまり表形式のモデルを表すために**dataset**という用語を使用しています。

Tabular Editorは、以下のソースからモデルのメタデータを読み込むことができます。

- [1] Model.bimファイル
- [2] Database.jsonファイル（詳細は @parallel-developmentを参照）
- [3] .pbitファイル（Power BIテンプレート）
- [4] SQL Server Analysis Services上のデータベース（Tabular）
- [5] Azure Analysis Services上のデータベース
- [6] Power BI Premium* ワークスペースのデータセット
- [7] Import/DirectQueryモードのPower BI Desktopレポート

*[XMLA Endpoint](https://docs.microsoft.com/en-us/power-bi/admin/service-premium-connect-tools)を有効にするには、Power BI Premium/Embedded CapacityまたはPower BI Premium-Per-Userが必要です。サードパーティツールがPower BIデータセットに接続するためには、XMLAエンドポイントを有効にする必要があります。

>[重要] Tabular Editor 2.xは、上記の1～7のすべてのソースをサポートしています。Tabular Editor 3では、使用している[Tabular Editor 3のエディション](xref:editions)に応じて、一部のソースのみをサポートしています。

モデルのメタデータがTabular Editorに読み込まれると、ユーザは**オブジェクト**の追加/編集/削除や**オブジェクトのプロパティ**の変更を自由に行うことができます。変更内容は、ユーザーが**File > Save**を選択するか、CTRL+Sを押してモデルを明示的に保存するまで、ソースには保存されません。モデルのメタデータがファイル・ソース（上記のソース1～3）から読み込まれた場合、そのファイルが更新されます。モデルのメタデータがAnalysis Servicesから読み込まれた場合（上記のソース4～7）、変更はAnalysis Servicesに保存されます。なお、変更によっては、オブジェクトがエンドユーザーからの問い合わせができない状態になることがあります。例えば、テーブルにカラムを追加した場合、ユーザーがテーブルのコンテンツやテーブルに依存するメジャーをクエリする前に、[テーブルのリフレッシュ](xref:refresh-preview-query#refreshing-data)が必要になります。

> [警告]
> モデルのメタデータの変更をPower BI Desktopに保存する際には、一定の制限があります（上記ソース7）。詳細は@desktop-limitationsを参照してください。

### TOMのオブジェクトとプロパティ

TOM メタデータは、**オブジェクト**と**プロパティ**で構成されています。

TOMの**オブジェクト**の例です。

- Data Sources
- Tables
- Partitions
- Measures
- KPIs
- Columns
- Model Roles

TOMの**オブジェクトプロパティ**の例

- `Name` (text)
- `Display Folder` (text)
- `Description` (text)
- `Hidden` (true/false)
- `Summarize By` (one of: None, Sum, Min, Max, ...)

ほとんどのプロパティは単純な値（テキスト、true/false、1つの選択項目、別名enum）ですが、プロパティは他のオブジェクトを参照することもできます（例えば、`Sort By Column`プロパティは列を参照する必要があります）。また、モデルロールオブジェクトの `Members` プロパティのように、プロパティをオブジェクトの配列にすることもできます。

Tabular Editorでは、オブジェクトやプロパティの名前は、[Microsoft.AnalysisServices.Tabular名前空間](https://docs.microsoft.com/en-us/dotnet/api/microsoft.analysisservices.tabular?view=analysisservices-dotnet)で定義されているものと同じものを使用します。特定のTOMオブジェクトやプロパティの詳細を知りたい場合は、必ず名前空間のドキュメントを参照してください。たとえば、「Summarize By」列プロパティの機能を知るには、まずMicrosoftのドキュメントで「Column」クラスを探し、「Properties」を展開して「SummarizeBy」までスクロールします。そして、[この記事](https://docs.microsoft.com/en-us/dotnet/api/microsoft.analysisservices.tabular.column.summarizeby?view=analysisservices-dotnet)にたどり着くはずです。

![SummarizeBy on Microsoft's docs](https://docs.tabulareditor.com/images/asdocs-summarizyby.png)

### プロパティ値の編集

どちらのバージョンのTabular Editorでも、オブジェクトモデルのメタデータは**TOM Explorer**ビューと呼ばれる階層型のビューで表示され、これはJSONメタデータの階層構造にほぼ対応しています。

![TOM Explorer](https://docs.tabulareditor.com/images/tom-explorer.png)

一般的に、TABULAR EDITORでは、まずTOM Explorerでオブジェクトを選択し(SHIFTまたはCTRLを押しながら複数のオブジェクトを同時に選択できます)、次に**Propertiesビュー**でプロパティ値を編集することで、オブジェクトのプロパティを変更できます(以下のスクリーンショットを参照)。

![プロパティビュー](https://docs.tabulareditor.com/images/properties-view.png)

Tabular Editorは、いくつかの基本的なルール（例えば、オブジェクト名は空にできない、メジャー名は一意でなければならないなど）を除いて、変更されたプロパティ値の明示的な検証を行いません。どのプロパティを設定し、どの値を使用するかは、表計算モデルの開発者の責任です。

プロパティ値の編集中にミスをした場合は、いつでもCTRL+Z（編集＞元に戻す）を押して、最後のプロパティ変更を元に戻すことができます。

## アーキテクチャ

上述したように、Tabular Editorには2つの異なる操作モードがあります。ファイルからのメタデータ（別名：**ファイルモード**）とAnalysis Servicesからのメタデータ（別名：**接続モード**）です。さらに、Tabular Editor 3では、[**workspace mode**](xref:workspace-mode)と呼ばれるハイブリッドなアプローチが導入されています。

先に進む前に、これらのモードの違いを理解することが重要です。

- ファイルモードでは、**Tabular Editorはすべてのモデルのメタデータをディスク上のファイルからロードしたり、ファイルに保存したりします。** このモードでは、Tabular Editorはモデルの**データ**を操作することはできません（つまり、テーブルのプレビュー、DAXクエリ、ピボットグリッド、データの更新操作はできません）。このモードは、Analysis Servicesのインスタンスが利用できない場合でも、完全にオフラインで使用できます。モデルのメタデータでサポートされているファイル形式は次のとおりです。
  - Model.bim（Visual Studioで使用されるフォーマットと同じ）
  - Database.json（Tabular Editorでのみ使用されるフォルダ構造）
  - .pbit (Power BIテンプレート)
- 接続モードでは、**Tabular EditorはAnalysis Servicesとの間でモデルのメタデータをロードおよびセーブします**。このモードでは、Tabular Editor 3を使用してモデルの**データ**を操作できます（テーブルのプレビュー、DAXクエリ、ピボットグリッド、データの更新）。このモードでは、Analysis Servicesのインスタンスへの接続が必要です。
- ワークスペース・モードでは、**Tabular Editor 3はディスク上のファイルからモデルのメタデータをロードし、そのメタデータをAnalysis Servicesにデプロイします**。その後の保存（CTRL+S）では、更新はディスクと接続されたAnalysis Servicesのインスタンスの両方に保存されます。モデルの**データ**を**接続モード** と同様に操作できます。

### メタデータの同期

標準的なツール（Visual Studio、Power BI Desktop）と比較したTabular Editorの大きな利点の1つは、モデルのメタデータが要求時にのみ保存されることです。つまり、オブジェクトやプロパティに複数の変更を加えても、各変更の間にAnalysis Servicesのインスタンスが同期されるのを待つ必要がありません。Analysis Servicesデータベースの同期は、データモデルのサイズや複雑さに応じて、完了するまでに数秒かかる操作です。Power BI Desktopでは、この同期は、悪評高い「作業中」のスピナーが画面表示されるたびに行われます。Tabular Editorでは、変更を明示的に保存（CTRL+S）したときにのみ発生します。

もちろん、メタデータの変更の影響をテストする前に、変更を明示的に保存することを忘れてはならないという欠点もあります。

## 次のステップ

- インストール・アクティベーション・ベーシック
- マイグレーション元バージョン
- デスクトップからの移行
- TE2からの移行
