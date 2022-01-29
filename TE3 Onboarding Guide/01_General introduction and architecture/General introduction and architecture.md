一般的な紹介と建築
2021-09-30
Daniel Otykier
Tabular Editorは、タブラーモデルを開発するためのWindowsデスクトップアプリケーションです。具体的には、このツールでTabular Object Model（TOM）のメタデータを編集することができます。このツールは、ファイルまたは既存のAnalysis ServicesデータベースからTOMメタデータをロードすることができ、更新されたTOMメタデータをAnalysis Servicesにデプロイすることもできます。

注
Analysis Services TabularはPower BIで使用されるデータモデルエンジンであるため、ここでは、Analysis Services TabularモデルとPower BIデータセットの両方を表すために、タブラーモデルという用語を使用しています。同様に、Analysis Services という用語を使用する場合は、SQL Server Analysis Services、Power BI Desktop、Power BI Service XMLA Endpoint などの「Analysis Services のあらゆるインスタンス」を意味します。

Tabular Object Model (TOM) メタデータ
データモデルは、いくつかのテーブルによって構成されています。各テーブルには1つまたは複数の列があり、テーブルにはメジャーや階層が含まれることもあります。通常、データモデルには、テーブル間の関係、接続の詳細を含むデータソース、データを読み込むためのデータソース式（SQLまたはMクエリ）を含むテーブルパーティションなども定義されています。これらの情報はすべて、モデルのメタデータと呼ばれ、Tabular Object Model (TOM)と呼ばれるJSONベースのフォーマットで保存されます。

表計算モデルをVisual Studioで作成した場合、TOMメタデータを表すJSONはModel.bimというファイルに格納されます。
Power BI Desktopでデータモデルを作成した場合、TOMメタデータは.pbixまたは.pbitファイルに埋め込まれます（このファイル形式には、ビジュアルやブックマークの定義など、データモデル自体とは関係のない詳細情報も多く含まれているため）。
AMO/TOMと呼ばれるクライアントライブラリを使用することで、Tabular EditorはこのJSONベースのフォーマットとの間でメタデータをロードおよびセーブすることができます。さらに、このクライアントライブラリを使用すると、Tabular EditorをAnalysis Servicesの任意のインスタンスに直接接続して、既存のデータベースからモデルのメタデータを取得することができます。これを以下の図に示します。

.../イメージ/アーキテクチャー.png

注意
上の段落では、Analysis Services にデプロイされたモデルを表すために、データベースという用語を使用しました。Power BI Serviceでは、同じもの、つまり表形式のモデルを表すためにデータセットという用語を使用しています。

Tabular Editorは、以下のソースからモデルのメタデータを読み込むことができます。

[1] Model.bimファイル
[2] Database.jsonファイル（詳細はEnabling parallel development using Git and Save to Folderを参照）。
[3] .pbitファイル（Power BIテンプレート）
[4] SQL Server Analysis Services上のデータベース（Tabular）
[Azure Analysis Services上のデータベース
[6] Power BI Premium* ワークスペースのデータセット
[7] Import/DirectQueryモードのPower BI Desktopレポート
*XMLA エンドポイントを有効にするには、Power BI Premium/Embedded Capacity または Power BI Premium-Per-User が必要です。サードパーティツールがPower BIデータセットに接続するためには、XMLAエンドポイントを有効にする必要があります。

重要事項
Tabular Editor 2.xは、上記の1～7のすべてのソースをサポートしています。Tabular Editor 3では、使用しているTabular Editor 3のエディションに応じて、一部のソースのみをサポートしています。

モデルのメタデータがTabular Editorに読み込まれると、ユーザーは自由にオブジェクトの追加/編集/削除やオブジェクトのプロパティを変更することができます。変更内容は、ユーザーがFile > Saveを選択するか、CTRL+Sを押してモデルを明示的に保存するまで、ソースには保存されません。モデルのメタデータがファイル・ソース（上記のソース1～3）から読み込まれた場合は、そのファイルが更新されます。モデルのメタデータがAnalysis Servicesから読み込まれている場合（上記のソース4～7）、変更内容はAnalysis Servicesに保存されます。なお、変更によっては、オブジェクトがエンドユーザーからの問い合わせができない状態になることがあります。例えば、テーブルに列を追加した場合、ユーザーがテーブルのコンテンツやテーブルに依存するメジャーをクエリする前に、テーブルをリフレッシュする必要があります。

警告
モデルのメタデータの変更をPower BI Desktopに戻して保存する場合、一定の制限があります（上記ソース7）。詳細は、「Power BI Desktopの制限事項」を参照してください。

TOMオブジェクトとプロパティ
TOM メタデータは、オブジェクトとプロパティで構成されています。

TOMオブジェクトの例です。

データソース
テーブル
パーティション
メジャー
KPI
コラム
モデルロール
TOMオブジェクトのプロパティの例です。

名前(テキスト)
表示フォルダ(テキスト)
説明(テキスト)
隠し（true/false）
Summarize By (one of: None, Sum, Min, Max, ...)
ほとんどのプロパティは単純な値（テキスト、true/false、単一選択、別名enum）ですが、プロパティは他のオブジェクトを参照することもできます（例えば、Sort By Column プロパティは列を参照する必要があります）。プロパティは、モデルRoleオブジェクトのMembersプロパティのように、オブジェクトの配列にすることもできます。

Tabular Editorでは、Microsoft.AnalysisServices.Tabular名前空間で定義されているものと同じ名前をオブジェクトとプロパティに使用します。特定のTOMオブジェクトやプロパティの詳細を知りたい場合は、必ず名前空間のドキュメントを参照してください。例えば、「Summarize By」列プロパティの機能を知るには、まずMicrosoftのドキュメントで「Column」クラスを探し、「Properties」を展開して「SummarizeBy」までスクロールします。そして、この記事にたどり着くはずです。

.../images/asdocs-summarizyby.png

プロパティ値の編集
どちらのバージョンのTabular Editorでも、オブジェクトモデルのメタデータはTOM Explorerビューと呼ばれる階層型のビューで表示され、これはJSONメタデータの階層構造にほぼ対応しています。

../images/tom-explorer.png

一般的に、TABULAR EDITORでは、まずTOM Explorerでオブジェクトを選択し（SHIFTまたはCTRLを押しながら複数のオブジェクトを同時に選択することができます）、次にPropertiesビューでプロパティ値を編集することで、オブジェクトのプロパティを変更することができます（以下のスクリーンショットを参照）。

.../images/properties-view.png

Tabular Editorは、いくつかの基本的なルール（例えば、オブジェクト名は空にできない、メジャー名は一意でなければならないなど）を除いて、変更されたプロパティ値の明示的な検証を行いません。どのプロパティを設定し、どの値を使用すべきかを知ることは、表計算モデルの開発者の責任です。

プロパティ値の編集中にミスをした場合は、いつでもCTRL+Z（編集＞元に戻す）を押して、最後のプロパティ変更を元に戻すことができます。

アーキテクチャ
上でほのめかしたように、Tabular Editorには2つの異なる操作モードがあります。ファイルからのメタデータ（別名：ファイルモード）とAnalysis Servicesからのメタデータ（別名：接続モード）です。さらに、Tabular Editor 3では、ワークスペースモードと呼ばれるハイブリッドなアプローチが導入されています。

先に進む前に、これらのモードの違いを理解することが重要です。

ファイルモードでは、Tabular Editorはすべてのモデルのメタデータをディスク上のファイルからロードしたり、ファイルに保存したりします。このモードでは、Tabular Editorはモデルデータを操作することができません（つまり、テーブルのプレビュー、DAXクエリ、ピボットグリッド、データの更新操作ができません）。このモードは、Analysis Services のインスタンスが利用できない場合でも、完全にオフラインで使用できます。モデルのメタデータでサポートされているファイル形式は次のとおりです。
Model.bim（Visual Studioで使用されるフォーマットと同じ）
Database.json（Tabular Editorでのみ使用されるフォルダ構造）
.pbit (Power BI テンプレート)
接続モードでは、Tabular EditorはAnalysis Servicesとの間でモデルのメタデータをロードおよびセーブします。このモードでは、Tabular Editor 3を使用してモデルデータを操作することができます（テーブルのプレビュー、DAXクエリ、ピボットグリッド、データのリフレッシュ）。このモードでは、Analysis Servicesのインスタンスへの接続が必要です。
ワークスペース・モードでは、Tabular Editor 3はディスク上のファイルからモデルのメタデータをロードし、そのメタデータをAnalysis Servicesに展開します。その後の保存（CTRL+S）では、更新はディスクと接続されたAnalysis Servicesのインスタンスの両方に保存されます。接続モードと同様に、モデルデータを操作することができます。
メタデータの同期
標準的なツール（Visual Studio、Power BI Desktop）と比較したTabular Editorの大きな利点の1つは、モデルのメタデータが要求時にのみ保存されることです。つまり、オブジェクトやプロパティに複数の変更を加えても、変更のたびにAnalysis Servicesのインスタンスが同期されるのを待つ必要がありません。Analysis Servicesデータベースの同期は、データモデルのサイズや複雑さに応じて、完了するまでに数秒かかる操作です。Power BI Desktopでは、この同期は、悪評高い「作業中」のスピナーが画面に表示されるたびに行われます。Tabular Editorでは、変更を明示的に保存（CTRL+S）したときにのみ発生します。

もちろん、メタデータの変更の影響をテストする前に、変更を明示的に保存することを忘れないようにしなければならないというデメリットもあります。

次のステップ
インストール、アクティベーション、基本構成
Visual Studioからの移行
Power BI Desktopからの移行
Tabular Editor 2.xからの移行