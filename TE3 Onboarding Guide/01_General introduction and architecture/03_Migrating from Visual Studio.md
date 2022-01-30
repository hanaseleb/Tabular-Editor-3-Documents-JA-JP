---
uid: migrate-from-vs
title: Migrating from Visual Studio
author: Daniel Otykier
updated: 2021-09-30
---

# Visual Studio / SQL Server Data Toolsからの移行

この記事では、[Analysis Services Projects for Visual Studio](https://marketplace.visualstudio.com/items?itemName=ProBITools.MicrosoftAnalysisServicesModelingProjects)（旧称：SQL Server Data Tools）を使ったTabularモデルの開発に慣れていることを前提としています。これは、SQL Server Analysis Services（Tabular）またはAzure Analysis Servicesを使用する開発者に共通しています。

- Tabularモデルの開発にVisual Studioを使用したことがない場合は、このトピックをスキップしても問題ありません。
- Tabular Editor 2.xを使用してTabularモデルを開発したことがある場合は、@migrate-from-te2の記事に直接進むことをオススメします。

## 部分的な移行

Tabular Editor 3には、タブラーモデルの開発においてVisual Studioから完全に移行できる機能があります。これはTabular Editor 2.xとは対照的で、テーブルのインポート、リレーションシップの視覚化、データのプレビューなどのためにVisual Studioを使用することを好むユーザーもいました。

しかし、Tabular Editor 3に慣れてくると、ときどきVisual Studioでタブラーモデルを開くのが便利だと感じるようになるかもしれません。これは、Tabular Editor 3がVisual Studioで使用されている**Model.bim**ファイルフォーマット（別名[TOM JSON](https://docs.microsoft.com/en-us/analysis-services/tom/introduction-to-the-tabular-object-model-tom-in-analysis-services-amo?view=asallproducts-allversions)）を変更しないため、Visual Studioとの互換性が確保されているため、いつでも可能です。

唯一の例外は、Tabular Editorの[Save-to-folder](xref:parallel-development#what-is-save-to-folder)機能を使用する場合で、このファイル形式はVisual Studioではサポートされていません。しかし、Tabular Editorの**File > Save As...**オプションを使えば、Visual Studioで使用するModel.bimファイルを簡単に再作成できます。この逆の変換は、Tabular EditorでModel.bimファイルを読み込み、 **File > Save to Folder...** オプションを使用して行うこともできます。

## ファイル形式変換の自動化

Tabular Editorのフォルダベースのフォーマット（database.json）とVisual Studioのファイルフォーマット（model.bim）の間で頻繁に変換する必要がある場合は、[Tabular Editor 2.x CLI](xref:command-line-options)を使って小さなWindowsコマンドスクリプトを書き、変換プロセスを自動化することを検討してください。

### [モデル.bimをフォルダに変換](#tab/frombim)

model.bimからdatabase.json(フォルダベースのフォーマット)に変換する場合。

```cmd
tabulareditor.exe model.bim -F database.json
```

### [model.bimに変換するフォルダ](#tab/fromfolder)

database.json(フォルダベースのフォーマット)からmodel.bimに変換する場合。

```cmd
tabulareditor.exe データベース.json -B モデル.bim
```

***

> [!Note] (注意)
> 上記のコマンドラインスクリプトは、[Tabular Editor 2.x](xref:getting-started-te2)がインストールされていることを前提としています。Tabular Editor 2.xのインストール場所は、[PATH環境変数](https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/path)の一部として指定する必要があります。
## 統合ワークスペースサーバー

Visual Studioで新しいAnalysis Services（Tabular）プロジェクトを開始すると、Visual Studioの統合ワークスペースサーバーを使用するか、Analysis Servicesの独自のインスタンスを用意するかを選択するプロンプトが表示されます。さらに、表形式モデルの互換性レベルを決定する必要があります（以下のスクリーンショットを参照）。

![VS 新規プロジェクト](https://docs.tabulareditor.com/images/vs-new-project.png)

対照的に、Tabular Editorで新しいモデルを作成する場合、ワークスペースサーバの使用は完全にオプションです（ただし、推奨されています - [ワークスペースモード](xref:workspace-mode)を参照してください）。

以下は、Tabular Editor 3で新しいモデルを作成するときに表示されるダイアログボックスです。

![新規モデルダイアログ](https://docs.tabulareditor.com/images/new-model.png)

Use workspace database**オプションを有効にすると、Tabular Editorはモデルの作業中にワークスペース データベースとして使用するAnalysis Servicesインスタンスとデータベース名の入力を求めます。このオプションを有効にしないと、「オフライン」モードでモデルを作成して作業することができ、テーブルやリレーションシップの追加、DAX式の作成などを行うことができます。ただし、オフライン モデルをAnalysis Servicesのインスタンスにデプロイしてからでないと、モデル内のデータの更新、プレビュー、クエリを実行できません。

> [!重要]Tabular Editor 3は、Visual Studioの**統合ワークスペース**オプションと同等の機能を提供していません。基本的に、統合ワークスペースは、Visual Studioで管理されるAnalysis Servicesインスタンスです。Analysis ServicesはMicrosoftの独占的ソフトウェアであるため、Tabular Editor 3と一緒に出荷することはできません。Analysis Services は Microsoft 社の専有ソフトウェアであるため、Tabular Editor 3 と一緒に出荷することはできません。その代わり、Tabular Editor で使用する Analysis Services のローカルインスタンスを実行する場合は、[SQL Server Developer Edition](https://www.microsoft.com/en-us/sql-server/sql-server-downloads)をインストールすることをお勧めします。
> 
### 互換性レベルの要件

Tabular Editorでは、Analysis Servicesデータベースを作成する際に、以下の互換性レベルを選択できます。

- 1200 (Azure Analysis Services / SQL Server 2016+)
- 1400 (Azure Analysis Services / SQL Server 2017+)
- 1500 (Azure Analysis Services / SQL Server 2019+)

さらに、Tabular Editorでは、[XMLAエンドポイント](xref:powerbi-xmla)を通じてPower BIサービスにデプロイされるPower BIデータセットに適した互換性レベルを選択することができます。

> [!!! 注意]
> Tabular Editorは、[Tabular Object Model (TOM)](https://docs.microsoft.com/en-us/analysis-services/tom/introduction-to-the-tabular-object-model-tom-in-analysis-services-amo?view=asallproducts-allversions)メタデータフォーマットを使用していないため、1200以下の互換性レベルはサポートしていません。互換性レベル1100または1103のモデルの開発をVisual StudioからTabular Editorに移行する予定の場合、**Tabular Editorに移行する前に、互換性レベルを少なくとも1200**にアップグレードする必要があります。そうすることで、モデルをSQL Server 2014 Analysis Servicesにデプロイすることができなくなります。

## Visual Studioプロジェクト

Visual StudioでAnalysis Services（Tabular）プロジェクトを作成すると、プロジェクトフォルダ内でModel.bimファイルの隣にいくつかのファイルが作成されます。これらのファイルには、Tabularオブジェクトモデル（TOM）とは関係のない、プロジェクトやユーザー固有の情報が含まれています。以下のスクリーンショットは、Visual Studioで新しいタブラープロジェクトを作成した際に作成されるファイルを示しています。

![VSプロジェクトファイル構造](https://docs.tabulareditor.com/images/vs-file-structure.png)

Tabular Editorに移行する場合は、Model.bimファイルを持ってくるだけでよい。その代わり、Tabular EditorはモデルのメタデータをModel.bimファイルから直接読み込むだけです。場合によっては、Model.bimファイルの隣に[Tabular Model User Options (tmuo) file](xref:user-options)というファイルが作成されることがあります。このファイルはTabular Editorで使用され、ワークスペースデータベースを使用するかどうか、データソースの（暗号化された）ユーザー認証情報など、ユーザーやモデル固有の設定を保存します。

プロジェクト」ディレクトリをきれいに保つために、Visual Studioで作成したModel.bimファイルを新しいディレクトリにコピーしてから、Tabular Editorでファイルを読み込むことをオススメします。

![Teファイル構造](https://docs.tabulareditor.com/images/te-file-structure.png)

並行開発やバージョン管理システムとの統合に推奨されている[Save-to-folder](xref:parallel-development#what-is-save-to-folder)機能を使用したい場合は、Tabular Editor内からモデルをフォルダーとして保存します(**File > Save To Folder...**)。

![Teフォルダ構造](https://docs.tabulareditor.com/images/te-folder-structure.png)

## バージョン管理

Tabular Editorには、モデルのメタデータの統合されたバージョンコントロールはありません。しかし、すべてのモデルメタデータはディスク上のシンプルなテキスト（JSON）ファイルとして保存されているので、タブラーモデルのメタデータをあらゆる種類のバージョンコントロールシステムに含めることは簡単です。このため、ほとんどのTabular Editorユーザーは、[Visual Studio Team Explorer](https://docs.microsoft.com/en-us/azure/devops/user-guide/work-team-explorer?view=azure-devops)や、とくにgitに関しては、Visual Studio 2019の新しい[Git Changes window](https://docs.microsoft.com/en-us/visualstudio/version-control/git-with-visual-studio?view=vs-2019)にアクセスできるように、Visual Studioを引き続きインストールしておきたいと考えています。

> [!!! NOTE].
> 最近では、[git](https://git-scm.com/)がほとんどの開発者に好まれているバージョンコントロールシステムであると思われます。Tabular Editor 3におけるGitの統合は、将来のアップデートを予定しています。

Tabular Editorに移行すると、Visual Studioで作成したオリジナルのTabularモデルプロジェクトとサポートファイルを保持する必要はありません。これまで通り、Visual Studio Team ExplorerまたはGit Changesウィンドウを使用して、コードの変更点の確認、バージョンコントロールブランチの管理、コードのチェックイン、マージなどを行うことができます。

もちろん、ほとんどのバージョン管理システムには、Visual Studioに頼らず使用できる独自のツールセットがあります。たとえば、gitにはコマンドラインがあり、[TortoiseGit](https://tortoisegit.org/)のように、Windowsエクスプローラーと直接統合する多くの人気ツールがあります。

### フォルダーへの保存とバージョン管理

[Save-to-folder](xref:parallel-development#what-is-save-to-folder)オプションを使用する主な利点は、モデルのメタデータを大きなJSONドキュメントにすべて保存するのではなく、複数の小さなファイルに分割して保存することです。TOMの多くのプロパティは、オブジェクトの配列です（たとえば、テーブル、メジャー、カラムなど）。このようなオブジェクトはすべて明示的な名前を持っているので、配列内の順序は問題になりません。ときどき、JSONへのシリアライズ中に順序変更されることがあり、これによりほとんどのバージョンコントロールシステムは、ファイルに変更が加えられたことを示します。しかし、この順序には何の意味もないので、この種の変更から発生する可能性のあるマージ・コンフリクトをわざわざ処理する必要はありません。

Save-to-Folderシリアライゼーションでは、JSONファイルで使用される配列の数が大幅に削減されます。これは、通常は配列として保存されるオブジェクトが、サブフォルダ内に保存される個々のファイルに分割されるためです。Tabular Editorがモデルのメタデータをディスクからロードするとき、これらのサブフォルダーをすべて走査して、すべてのオブジェクトがTOMに正しくデシリアライズされるようにします。

このように、Save-to-Folderシリアライゼーションは、複数の開発者が同じタブラーモデルに並行して変更を加えた場合に、マージコンフリクトが発生する可能性を大幅に低減します。

## UIの違い

このセクションでは、タブラーモデルの開発におけるTabular Editor 3とVisual Studioのユーザーインターフェイスのもっとも重要な違いを示します。Visual Studioの熱心なユーザーであれば、Tabular Editor 3のユーザーインターフェイスに違和感はないはずです。より詳細なウォークスルーをご希望の方は、<xref:user-interface>をご覧ください。

### タブラーモデルエクスプローラーとTOMエクスプローラーの比較

Visual Studioでは、モデルのメタデータを階層的に概観するには、**Tabular Model Explorer**を使用します。

![Vs Tom Explorer](https://docs.tabulareditor.com/images/vs-tom-explorer.png)

Tabular Editorでは、これを **TOM Explorer** ビューと呼びます。Tabular Editorでは、すべてのデータモデリングは、TOM Explorerで関連するオブジェクトを見つけ、右クリックのコンテキストメニューを呼び出したり、メインメニューに移動したり、**Properties**ビューでオブジェクトのプロパティを編集したりすることで、特定のアクションを実行することを中心に展開されます。タブラエディタでは、マルチセレクト、ドラッグドロップ、コピーペースト、アンドゥ・リドゥなどの直感的な操作で、すべてのデータモデリング操作を行うことができます。

![Vs Tom Explorer](https://docs.tabulareditor.com/images/tom-explorer.png)

Tabular EditorのTOMエクスプローラーには、特定のタイプのオブジェクトの表示/非表示、隠しオブジェクト、表示フォルダー、メタデータ階層の迅速な検索とフィルタリングのためのショートカットオプションもあります。

詳しくは@tom-explorer-viewを参照してください。

### プロパティグリッド

Visual StudioとTabular Editorには、プロパティグリッドがあり、現在選択されているオブジェクトのほとんどのプロパティを編集できます。下の図は、同じメジャーのVisual Studioのプロパティグリッド(左)とTabular Editorのプロパティグリッド(右)の比較です。

![Visual StudioのプロパティグリッドとTabular Editorのプロパティグリッド](https://docs.tabulareditor.com/images/property-grid-vs-te.png)

この2つは概ね同じように動作しますが、Tabular EditorではTOMオブジェクトのプロパティに近いプロパティ名を使用しています。また、Tabular Editorは、特定のモデリング操作を容易にするため、TOMにはない多くのプロパティを追加します。たとえば、**Translated Names**プロパティを拡張することで、すべてのモデルカルチャでオブジェクト名の翻訳を比較、編集ができます。

### DAX式の編集

Visual Studioでは、数式バーを使用するか、Tabular Model Explorerでメジャーを右クリックして「数式の編集」を選択してDAXエディタウィンドウを開くことができます。

タブラー・エディターの動作もよく似ていますが、数式バーの代わりに **式エディター** ビューが表示されます。さらに、スタンドアロン・ドキュメント内の1つまたは複数のオブジェクトのDAX式を編集する場合は、それらのオブジェクト（メジャー、計算列、計算テーブル）を右クリックして、「**Script DAX**」を選択します。

Tabular Editor 3のDAXコードエディターは、このツールを使用する主な理由の1つです。詳しくは[こちら](xref:dax-editor)をご覧ください。

### エラーリストとメッセージビューの比較

Visual Studioでは、DAXの構文エラーは **エラーリスト** 内に警告として表示されます (以下のスクリーンショットを参照)。また、エラーが発生したメジャーは、メジャーグリッドに警告の三角マークが表示されます。

![Vsエラーリスト](https://docs.tabulareditor.com/images/vs-error-list.png)

Tabular Editorでは、Messages Viewを使用して、モデル開発中にさまざまなソースから投稿されたすべてのエラー、警告、および情報メッセージを統合します。とくにDAXの構文エラーについては、メッセージビューにエラーとして表示され、エラーが発生したメジャーはTOMエクスプローラーに赤いドットで表示されます（以下のスクリーンショットを参照）。

![テ・メッセージ](https://docs.tabulareditor.com/images/te-messages.png)

上のスクリーンショットでは、3つの異なるメッセージ投稿ソースがあることに注目してください。

- 分析サービス**Analysis Services**: メタデータの変更がAnalysis Servicesの接続されたインスタンスに保存されると、サーバーはTOMメタデータを更新し、オブジェクトが誤った状態になっているかどうかを示します。具体的には、[State](https://docs.microsoft.com/en-us/dotnet/api/microsoft.analysisservices.tabular.measure.state?view=analysisservices-dotnet#Microsoft_AnalysisServices_Tabular_Measure_State)および[ErrorMessage](https://docs.microsoft.com/en-us/dotnet/api/microsoft.analysisservices.tabular.measure.errormessage?view=analysisservices-dotnet#Microsoft_AnalysisServices_Tabular_Measure_ErrorMessage)プロパティが更新されます。Tabular Editorは、これらのエラーメッセージをメッセージビューに表示します。これらのメッセージは、Tabular Editorがオフライン（Analysis Servicesに接続していない状態）で使用されている場合は表示されません。
- **Tabular Editor Semantic Analysis**。さらに、Tabular Editor 3は、モデル内のすべてのDAX式に対して独自のセマンティック分析を行います。構文エラーやセマンティックエラーが発生した場合は、ここで報告されます。
- **エクスプレッションエディター**: 最後に、Tabular Editor 3でExpression Editorなどのドキュメントが開かれている場合、そのドキュメントで発生したDAXの構文エラーやセマンティックエラーがここで報告されます。

### テーブルデータのプレビュー

Visual Studioでは、Model.bimファイルを読み込むと、テーブルとその内容がタブビューに表示されます。Tabular Editor 3では、TOM Explorerでテーブルを右クリックして、**Preview Data**を選択することで、テーブルデータをプレビューできます。これにより、新しいドキュメントタブが開き、テーブルのすべての行をスクロールしたり、列をフィルタリングしたりソートしたりすることができます。これはDirectQueryを使ったモデルでも使えます。

また、ドキュメントを自由に並べ替えることで、複数のテーブルの内容を一度に見ることができます（下のスクリーンショットを参照）。

![Te3 テーブルプレビュー](https://docs.tabulareditor.com/images/te3-table-preview.png)

### テーブルのインポート

Tabular Editor 3で新しいテーブルをインポートするには、**Model > Import tables...** オプションを使用します。これにより、Tabular Editor 3のテーブルのインポートウィザードが起動し、データソースへの接続やインポートするテーブルの選択などのプロセスが案内されます。このプロセスは、Visual Studioの従来のテーブルインポートと比較的似ています。

重要な違いの1つは、Tabular Editor 3にはビジュアルなPower Query Editorが含まれていないことです。Power Query (M) 式をテキストとして編集することはできますが、Power Queryクエリとして表現される複雑なデータ変換にモデルが大きく依存している場合は、Power Queryクエリを編集する目的でVisual Studioの使用を継続することを検討する必要があります。

> [!!! 注意]
> Power Queryを使用して複雑なデータ変換を行うことは、データ更新操作のオーバーヘッドが増加するため、一般的にエンタープライズデータモデリングには推奨されません。代わりに、他のETLツールを使ってデータをスタースキーマに準備し、スタースキーマのデータをSQL ServerやAzure SQL Databaseなどのリレーショナルデータベースに保存します。そして、そのデータベースから表計算モデルにテーブルをインポートします。

#### パーティションの編集とテーブルスキーマの更新

Tabular Editor 3では、テーブルを強制的にリフレッシュすることなく、テーブルのスキーマを更新できます。パーティションは、TOMエクスプローラーに個別のオブジェクトとして表示されます。パーティションをクリックすると、エクスプレッションエディターで式(MまたはSQL)を編集できます。

パーティション式が更新されると、Tabular Editorは、更新された式から得られるテーブルスキーマがモデルで定義された列のセットと異なるかどうかを自動的に検出することができます。スキーマの更新を行うには、TOMエクスプローラーでパーティションまたはテーブルを右クリックし、**テーブルスキーマの更新...**を選択します。

テーブルのインポートとスキーマ更新の詳細については、@importing-tablesを参照してください。

### 関係性の視覚化

Visual Studioには、テーブル間のリレーションシップを視覚化して作成できるダイアグラムツールがあります。

Tabular Editor 3にもダイアグラムツールが含まれており、 **ファイル > 新規作成 > ダイアグラム** でアクセスできます。新しいダイアグラムドキュメントタブが作成されます。この時点で、TOMエクスプローラーからテーブルをドラッグ＆ドロップで追加するか、**Diagram > Add tables...**メニューから追加できます。

テーブルがダイアグラムに追加されると、ある列から別の列にドラッグするだけで、列間の関係を作ることができます。

![Te3 Diagram View](https://docs.tabulareditor.com/images/te3-diagram-view.png)

> [!!!注意]!
> パフォーマンス上の理由から、ダイアグラム・ツールはモデルのデータを検査せず、作成したリレーションの一意性や方向性も検証しません。リレーションシップが正しく作成されているかどうかは、ユーザーにかかっています。リレーションシップが正しく定義されていない場合、Analysis Services は **メッセージビュー** に表示されるエラー状態を返します。

### モデルのデプロイ

Tabular Editorを使用すると、モデルのメタデータをAnalysis Servicesの任意のインスタンスに簡単に展開できます。Tabular Editorのデプロイウィザードは、**Model > Deploy...**またはCTRL+SHIFT+Dを押すことで呼び出すことができます。

詳細については、<xref:deployment>を参照してください。

## 次のステップ

- 移行先
- 並行開発
- 生産性向上のための施策
