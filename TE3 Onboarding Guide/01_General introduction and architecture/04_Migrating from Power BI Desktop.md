---
uid: migrate-from-desktop
title: Migrating from Power BI Desktop
author: Daniel Otykier
updated: 2021-09-30
---

# Power BI Desktopからの移行

Power BI Desktopでのデータモデリングの概念にすでに慣れている場合、この記事はデータモデリングをTabular Editorに移行する際に役立つことを目的としています。そのため、Power Query Editor、インポートされたテーブルと計算されたテーブル、計算された列、メジャーなどの概念をしっかりと理解していることを前提としています。

## Power BIとTabular Editor

歴史的には、Tabular Editorは、SQL Server Analysis Services（Tabular）やAzure Analysis Servicesの開発者向けのツールとして設計されました。Power BIが発売された当初は、サードパーティツールがPower BIのデータモデルをホストするAnalysis Servicesインスタンスにアクセスする方法はサポートされておらず、Power BIのデータセットを作成・編集するには、Power BI Desktopを利用するしかありませんでした。

これが変わったのは2020年3月、[MicrosoftがPower BI PremiumのXMLAエンドポイントの読み書きを発表](https://powerbi.microsoft.com/en-us/blog/announcing-read-write-xmla-endpoints-in-power-bi-premium-public-preview/)してからです。その数か月後には、[外部ツール機能の発表](https://powerbi.microsoft.com/en-us/blog/announcing-public-preview-of-external-tools-in-power-bi-desktop/)により、Power BI Desktopと連携してサードパーティのツールを使用することも可能になりました。

Power BI PremiumでXMLAエンドポイントが利用できるようになったことで、データモデル開発者は既存のスキルやツールを活用できるようになりました。
マイクロソフトが[Power BI PremiumをAnalysis Servicesのスーパーセットに](https://community.powerbi.com/t5/Webinars-and-Video-Gallery/Power-BI-Premium-as-a-superset-of-Analysis-Services-the-XMLA/m-p/1434121)するため多額の投資をしていることは周知の事実です。
言い換えれば、コミュニティツールや商用ツールなどのサードパーティツールとPower BIとの統合は、今後も継続されるものです。実際、Microsoft AnalyticsのCTOであるAmir Netz氏は、[SQLBIの創設者であるMarco Russo氏との共同声明](https://powerbi.microsoft.com/en-us/blog/community-tools-for-enterprise-powerbi-and-analysisservices/)を発表し、この点を肯定しています。

Tabular Editor ApSでは、Tabular Editor 3が現在入手可能な最高のタブラーデータモデリングツールであると確信しており、上述の統合のおかげで、このツールはもはやSQL ServerやAzure Analysis Servicesの開発者だけのものではありません。

先に進む前に、Tabular EditorがPower BIと連携して、2つのまったく異なるシナリオで使用できることを理解しておくことが重要です。

- **シナリオ1：** Power BI Desktopの外部ツールとしてのTabular Editor。
- **シナリオ2：** Power BI PremiumのXMLAエンドポイントでのTabular Editor。

> [!重要]です。
> Tabular Editorで.pbixファイルを直接読み込むことはできません。詳細については、<xref:desktop-limitations#power-bi-file-types>を参照してください。

### シナリオ1: Power BI Desktopの外部ツールとしてのTabular Editor

一般的にこのシナリオは、Power BI PremiumにアクセスできないセルフサービスのアナリストやPower BI Desktopユーザーを対象に、特定のデータモデリング操作を容易にしたり（メジャーの追加や編集など）、他では利用できない高度なモデリングオプション（Calculation Groups、パースペクティブ、メタデータの変換）を解除したりすることを目的としています。

外部ツールは、Power BI DesktopがホストするAnalysis Servicesモデルに接続します。これにより、ツールはデータモデルに特定の変更を加えることができます。ただし、現在のところ、Power BI Desktopでは、すべての種類のデータモデリング操作がサポートされているわけではありません。この制限と、Power BI Desktopの外部ツールとして使用した場合のTabular Editorの動作を理解することが重要です。これについての詳細は、<xref:desktop-limitations>を参照してください。

このシナリオでの典型的なワークフローは次のとおりです。

1. Power BI Desktopで`.pbit`または`.pbix`ファイルを開きます。
2. 外部ツール」リボンからTabular Editorを起動する
3. 必要な変更の種類に応じて、Tabular EditorとPower BI Desktopを行き来します。たとえば、Tabular Editorでメジャーを追加・編集することはできますが、モデルに新しいテーブルを追加する必要がある場合は、Power BI Desktopを使用する必要があります。
4. Tabular Editorで変更を行った場合は、**File > Save**（CTRL+S）を使用して、変更内容をPower BI Desktopに書き戻します。
5. 変更が完了したら、Tabular Editorを閉じます。その後、Power BI Desktopから通常通りレポートの発行または保存を行います。

> [!!!注意]
> 2021年10月現在、Power BI Desktopにはバグがあり、外部ツールで行った変更を反映させるために、フィールドリストやビジュアルをDesktopが自動的に更新できないことがあります。この問題が発生した場合、.pbixファイルを保存して再度開くか、モデル内のテーブルを手動で更新すると、通常はフィールドリストとすべてのビジュアルが正しく更新されます。

外部ツールに適用される[モデリングの制限](xref:desktop-limitations)は、書き込み操作/モデルの修正に関してのみ関係します。このガイドで後述するように、テーブルデータのプレビュー、ピボットグリッド、DAXクエリを使ってモデル内のデータを参照するには、Tabular Editor 3の接続機能を引き続き使用することができます。

### シナリオ2：Power BI Premium XMLAエンドポイントを使用したTabular Editor

このシナリオは、Power BI Premium CapacityまたはPower BI Premium-Per-Userワークスペースを使用している組織のBI担当者が、データセット開発の目的でPower BI Desktopを完全に置き換えることを想定しています。

基本的に、Power BI Premium XMLAエンドポイントは、Analysis Services（Tabular）のインスタンスを公開します。このシナリオでは、Tabular Editorの動作は、Azure Analysis ServicesやSQL Server Analysis Services（Tabular）に接続した場合と変わりません。

このシナリオでの典型的なワークフローは次のとおりです。

1. Tabular Editorにはじめて移行する際、XMLAエンドポイントを使用してPower BIデータセットをTabular Editorで開き、モデルのメタデータをファイル（Model.bim）またはフォルダー（Database.json）として保存します。詳しくは@parallel-developmentをご覧ください。
2. 今後は、手順1で保存したファイルまたはフォルダーからTabular Editorでモデルのメタデータを開きます。オプションで[workspace mode](xref:workspace-mode)を使用します。
3. Tabular Editorで変更を適用します。
4. ワークスペースモードを使用した場合、Tabular Editorで保存（CTRL+S）を押すたびに、Power BIサービスで変更がすぐに表示されるはずです。
5. ワークスペース・モードを使用していない場合、または変更が終わったら、Tabular Editorの **Model > Deploy...** オプションを使用して、Power BIサービスに変更を公開します。

このシナリオでは、モデルのメタデータの「真実のソース」は、ディスク上に保存されたファイルまたはフォルダー構造であるため、バージョンコントロール統合による並行開発だけでなく、Azure DevOpsなどの自動ビルドサーバーを使用した継続的統合/継続的デプロイメント（CI/CD）も可能になります。詳しくは<xref:powerbi-cicd>を参照してください。

> [! WARNING] (警告)
> Power BI サービスの XMLA エンドポイントを使用して Power BI データセットに変更を適用すると、そのデータセットを .pbix ファイルとしてダウンロードできなくなります。詳細は、[XMLAエンドポイントによるデータセットの接続性](https://docs.microsoft.com/en-us/power-bi/admin/service-premium-connect-tools#power-bi-desktop-authored-datasets)を参照してください。

XMLAエンドポイントを介してデータセットに接続するためにTabular Editorを使用する場合、書き込み操作やモデルの変更の種類に制限はありません。

この記事の残りの部分では、データモデル開発におけるPower BI DesktopとTabular Editorの違いに焦点を当てています。Power BI Desktop（シナリオ1）の外部ツールとしてTabular Editorを使用する際に適用される[モデリングの制限](xref:desktop-limitations)があるため、シナリオ2にのみ適用されるセクションもあります。

## Tabular Editor3のユーザーインターフェイス

Tabular Editorがはじめての方は、以下のリソースに目を通し、Tabular Editor 3のユーザーインターフェイスを理解することをオススメします。

- [Getting to know Tabular Editor 3's User Interface](xref:user-interface)
- [TOM Explorerビュー](xref:tom-explorer-view)
- [プロパティビュー](xref:properties-view)
- [DAXエディタ](xref:dax-editor)

## Tabular Editor3ハウツー

以下は、Tabular Editor 3で一般的なタスクを実行する方法を簡単に説明したものです。

### メジャーの追加方法

モデルに新しいメジャーを追加するには、**TOM Explorer**で新しいメジャーを配置するテーブルを右クリックし、**Create > Measure**（ショートカットALT+1）を選択します。メジャーが追加されると、すぐにメジャーの名前を入力ができます。

![小節の追加](https://docs.tabulareditor.com/images/add-measure.png)

### 小節の名前を変更する方法

小節（またはその他のオブジェクト）の名前を編集する必要がある場合は、単に小節を選択してF2を押す（または小節名をダブルクリックする）だけです。複数のオブジェクトが選択されている場合は、バッチリ名前変更ダイアログが表示され、複数のオブジェクトの名前を一度に簡単に変更できます。

![バッチリリネーム](https://docs.tabulareditor.com/images/batch-rename.png)

> [! WARNING] (警告)
> データモデルのオブジェクト名を変更すると、名前が変更されたオブジェクトの1つまたは複数に依存しているレポートビジュアルが動作しなくなる場合があります。外部ツールはPower BIのビジュアルに関する情報にアクセスできないため、Tabular Editorはビジュアルで使用されているオブジェクトの名前が変更されたり削除されたりする前に警告を出すことができません。

### メジャーのコピーを作成する方法

Tabular Editor 3では、おなじみの切り取り（CTRL+X）、コピー（CTRL+C）、貼り付け（CTRL+V）の操作でオブジェクトを素早く移動したりコピーを作成したりできます。また、「TOM Explorer」を使って、テーブルや表示フォルダー間でオブジェクトをドラッグすることもできます。途中でミスをした場合は、元に戻す (CTRL+Z) およびやり直し (CTRL+Y) オプションを (繰り返し) 使用して、適用した変更の履歴を前後に移動ーできます。

### メジャーのDAX式を変更する方法

小節のDAX式を変更するには、**TOM Explorer** で変更したい小節を探して選択します。TOM Explorerの上部にあるツールバーボタンで、隠しオブジェクトの表示(CTRL+6)とフォルダーの表示(CTRL+5)を切り替えることができます。また、検索ボックスにメジャーの部分名を入力して、**TOM Explorer**をフィルタリングすることもできます。

メジャーが選択されると、**Expression Editor**にメジャーのDAX式が表示され、**Properties**グリッドには`Description`、`Format String`、`Hidden`などの各種プロパティが表示されます。

![メジャーの修正](https://docs.tabulareditor.com/images/modify-measure.png)

DAX式を修正するには、**Expression Editor**にカーソルを置いて、DAXコードを更新するだけです。F6を押すと、コードが自動的にフォーマットされます。TOM Explorerで別のオブジェクトを選択するか、緑のチェックマークボタン **Expression > Accept** (F5)をクリックすると、式の変更がTabular Editorにローカル保存されます。また、赤い「X」を押して、**Expression > Cancel**とすることで、行った変更をキャンセルできます。誤って**Accept**を押してしまった場合は、**Edit > Undo**（CTRL+Z）オプションを使用して、いつでも変更を取り消すことができます。

変更した内容をPower BI Desktop、Power BI XMLAエンドポイント、またはモデルを読み込んだディスク上のファイルに保存するには、**File > Save**（CTRL+S）を実行します。

DAXコードを書く際のエクスプレッションエディターの機能については、<xref:dax-editor>を参照してください。

### メジャー間の依存関係を視覚化する方法

**TOM Explorer** でメジャーを選択した状態で、**メジャー > 依存関係の表示** (SHIFT+F12) オプションを使用します。これにより、新しいウィンドウがポップアップし、そのメジャーのDAX式の依存関係ツリーが可視化されます。上流と下流の両方の依存関係の表示を切り替えることができます。

![依存関係の表示](https://docs.tabulareditor.com/images/show-dependencies.png)

依存関係ビューの項目をダブルクリックすると、**TOM Explorer**でそのオブジェクトに移動します。

### メジャーの形式文字列を変更する方法

**TOM Explorer**で変更したいメジャーを探し、選択します。隠しオブジェクトの表示(CTRL+6)とフォルダの表示(CTRL+5)は、TOM Explorerの上部にあるツールバーボタンで切り替えることができます。また、検索ボックスにメジャーの部分名を入力して、**TOM Explorer**をフィルタリングすることもできます。

メジャーを選択したら、**Properties**グリッドで`Format String`プロパティを見つけて展開し、好みに応じてフォーマット・ストリング・プロパティを設定してください。Format」プロパティの右にあるドロップダウン・ボタンを注目してください。また、`Format String`プロパティ自体にフォーマット文字列を自由に入力することもできます。

![フォーマット文字列](https://docs.tabulareditor.com/images/format-string.png)

### 複数のメジャーのDAX式を修正する方法

Tabular Editor 3では、複数のメジャーを選択して **DAXスクリプト** を作成することができ、選択したすべてのメジャーのDAX式やさまざまなプロパティを一度に変更できます。

既存のメジャーに基づいてDAXスクリプトを作成するには、**TOM Explorer**でメジャーを選択します（CTLRキーを押しながら複数のオブジェクトを選択するか、SHIFTキーを押しながらオブジェクトの範囲を選択します）。次に、右クリックして **Script DAX** を実行します。

![スクリプトダックス](https://docs.tabulareditor.com/images/script-dax.png)

スクリプトの中で、`Description`、`FormatString`、`Visible`、`DetailRows`などのプロパティを直接追加・変更できます。

F5を押すと、スクリプトがデータモデルに適用されます。**式エディター**とは異なり、別のオブジェクトに移動しても、スクリプトに加えられた変更は自動的に適用されないことに注意してください。DAXスクリプトによって適用された変更を元に戻すには、**Edit > Undo** (CTRL+Z)オプションを使用します。

詳細については、@dax-script-introductionを参照してください。

### 表のデータをプレビューする方法

表の内容を表示するには (Power BI Desktopの [データ] タブと同様)、表の上で右クリックして [データのプレビュー] を選択するだけです。これにより、表の内容のプレビューを含む新しいタブが開きます。表のすべての行をスクロールしたり、列にソートやフィルタリングを適用したりすることができます。Power BI Desktopとは異なり、これらのプレビュータブはいくつでも開くことができ、ユーザーインターフェイス上で隣り合って配置することができます。プレビューは、[DirectQueryモード](https://docs.microsoft.com/en-us/power-bi/connect-data/desktop-use-directquery)のテーブルでも機能します（ただし、プレビューは最初の100レコードに限定されます）。

![プレビューデータ](https://docs.tabulareditor.com/images/preview-data.png)

> [!!! NOTE].
> **データのプレビュー**機能は、Tabular EditorがPower BI DesktopまたはPower BI XMLAエンドポイントのデータセットに接続されている場合にのみ使用できます。詳細は、@refresh-preview-queryを参照してください。

### Calculation Groupsを追加する方法

[Calculation Groups](https://docs.microsoft.com/en-us/analysis-services/tabular-models/calculation-groups?view=asallproducts-allversions)は、修正した DAX フィルタのコンテキストやその他のタイプのビジネスロジックを、すべてのモデルのメジャーに渡って定義し、再利用するのに便利です。タブラー・エディターを使用してCalculation Groupsを追加するには、単に **モデル > 新規Calculation Groups** (ALT+7)オプションを使用します。

![Calculation Groupsの追加](https://docs.tabulareditor.com/images/add-calc-group.png)

Calculation Groupsに名前を付け、**TOM Explorer**でCalculation Groupsを選択した状態で、**Calculation Groupsテーブル>作成>計算項目**オプションを使用して新しい計算項目を追加します。計算項目をコピー（CTRL+C）およびペースト（CTRL+V）することで、追加項目のためのこのプロセスを高速化することができます。

![計算項目の追加](https://docs.tabulareditor.com/images/add-calc-item.png)

### 新しいテーブルを追加する方法

モデルに新しいテーブルを追加するには、 **モデル > テーブルのインポート...** オプションを使用します。タブラルエディタの[テーブルのインポートウィザード](xref:importing-tables)で手順を説明します。

> [! 注意]
> Tabular Editor 3は、Power BIでサポートされていないすべてのデータソースをサポートしているわけではありません。モデルがTabular Editorでサポートされていないデータソースを使用している場合、同じソースから新しいテーブルをインポートする最も簡単な方法は、Tabular Editorで既存のテーブルをコピーし（CTRL+C / CTRL+V）、以下に示すようにパーティション式を修正してテーブルスキーマを更新することです。この作業を行うには、**Tools > Preferences > Schema Compare > Use Analysis Services for Change Detection**オプションが有効になっていることを確認してください。詳細については、<xref:importing-tables#updating-table-schema-through-analysis-services>を参照してください。
> [!!! IMPORTANT] (重要)
> このオプションは、Tabular Editorを外部ツールとして使用している場合、デフォルトでは使用できません。これは、外部ツールを介したテーブルの追加/編集が[Power BI Desktopではサポートされていない](xref:desktop-limitations)ためです。詳細は @importing-tables-data-modeling を参照してください。

### テーブル上のPower Query式を修正する方法

各テーブルに読み込まれる内容を定義するPower Query (M) 式は、対応するテーブルの **パーティション** に存在します。パーティションは **TOM Explorer** で確認できます。パーティションを選択すると、Tabular EditorはそのパーティションのM式を**Expression Editor**に表示し、編集できます。式を編集して変更を受け入れた後、**TOM Explorer**のパーティションを右クリックして **テーブルスキーマの更新...** オプションを選択すると、更新されたPower Query式に基づいてテーブルにインポートされた列が変更されるかどうかが検出されます。

![Power Query Update Schema](https://docs.tabulareditor.com/images/power-query-update-schema.png)

> [!!! NOTE].
> 現在、Tabular Editor 3ではパーティション式の検証は行われません。Power Query (M) 式については、Tabular Editor 3 の後のアップデートで計画されています。
> [!重要] > パーティション式は読み取り専用です。
> 外部ツールによるパーティションの編集は[Power BI Desktopではサポートされていない](xref:desktop-limitations)ため、Tabular Editorを外部ツールとして使用する場合、パーティション式はデフォルトで読み取り専用になります。
Power Queryの式の変更によって、インポートされたテーブルの列に変更が生じた場合、その変更を確認できるダイアログが表示されます。

![スキーマ変更の適用](https://docs.tabulareditor.com/images/combine-sourcecolumn-update.png)

### 共有Power Query式を修正する方法

共有式とは、テーブルへのデータの読み込みに直接使用されないMクエリのことです。例えば、Power BI DesktopでPower Queryのパラメータを作成すると、このパラメータのM式がShared Expressionとして保存されます。Tabular Editorでは、これらは**TOM Explorer**のShared Expressionsフォルダからアクセスでき、パーティション上のMクエリと同様に編集できます。

![Shared Expression](https://docs.tabulareditor.com/images/shared-expression.png)

> [! IMPORTANT] (重要)
> 外部ツールによるパーティションの編集は[Power BI Desktopではサポートされていない](xref:desktop-limitations)ため、Tabular Editorを外部ツールとして使用する場合、共有式はデフォルトで読み取り専用になります。

### テーブル間のリレーションシップを追加する方法

2つのテーブルの間にリレーションシップを追加する最も簡単な方法は、新しいダイアグラムを作成し、2つのテーブルをダイアグラムに追加し、一方のテーブルから他方のテーブルにカラムを視覚的にドラッグして、リレーションシップに参加すべきカラムを示すことです。これは、Power BI Desktopでリレーションシップを作成する方法と似ています。

1. 新しいダイアグラムを作成するには、**ファイル>新規>ダイアグラム**オプションを使用します。
2. ダイアグラムにテーブルを追加するには、**TOM Explorer**からテーブルをドラッグ＆ドロップするか、 **Diagram > Add tables...** オプションを使用します。
3. テーブルが追加されたら、（多面的な）ファクト・テーブルの列を探し、（片面的な）ディメンション・テーブルの対応する列にドラッグします。
4. 関係の設定を確認し、「OK」をクリックします。

![共有表現](https://docs.tabulareditor.com/images/create-relationship-through-diagram.gif)

詳しくは[Working with diagrams](xref:importing-tables-data-modeling#working-with-diagrams)を参照してください。

> [!重要なこと］
> 外部ツールによるリレーションシップの編集は[Power BI Desktopではサポートされていない](xref:desktop-limitations)ため、Tabular Editorを外部ツールとして使用する場合は、リレーションシップを修正することはできません。

### Power BI Serviceに公開する方法

Power BI Serviceでデータセットを公開または更新するには、**モデル > デプロイ...**オプションを使用し、データセットを公開したいワークスペースのXMLAエンドポイントを使用します。

XMLAエンドポイントからモデルのメタデータを直接読み込んだ場合は、**File > Save**（CTRL+S）を実行するだけで、Tabular Editorで読み込んだデータセットを更新できます。

> [!NOTE].
> Tabular Editor 3 Desktop Editionは、Power BI Desktopの外部ツールとしての使用のみを想定しているため、**Model > Deploy...**オプションは**利用できません。[詳細情報](xref:editions)を参照してください。

## 次のステップ

- <xref:ユーザーインターフェイス>
- 並行開発
- <xref:ユーザーインターフェイス> @並行開発 A生産性向上 B生産性向上 C生産性向上 D生産性向上 E生産性向上 E生産性向上
- <xref:新pbiモデル> (英語)