GitとSave to Folderを使った並行開発の実現
2021-09-30
Daniel Otykier
以下に適用されます。
❌ デスクトップ版
ビジネスエディション
エンタープライズ・エディション

この記事では、パラレルモデル開発（複数の開発者が同じデータモデルで並行して作業を行うこと）の原則と、この点におけるTabular Editorの役割について説明します。

前提条件
データモデルの保存先は、以下のいずれかである必要があります。
SQL Server 2016（またはそれ以降） Analysis Services Tabular
Azure 分析サービス
XMLAの読み書きを有効にしたPower BI Premium Capacity/Power BI Premium-per-user
チームメンバー全員がアクセス可能なGitリポジトリ（オンプレミスまたはAzure DevOps、GitHubなどでホストされているもの）
ソースコードとしてのTOM
並列開発は従来、Analysis Servicesの表形式モデルやPower BIのデータセットでは実装が困難でした（本記事では、簡潔にするために両タイプのモデルを「表形式モデル」と呼びます）。Tabular Object Model（TOM）で使用されているJSONベースのモデルメタデータが導入されたことで、モデルのメタデータをバージョン管理に統合することは確かに容易になりました。

テキストベースのファイルフォーマットを使用することで、バージョンコントロールシステムによく含まれている様々なdiffツールを使用して、矛盾した変更を優雅に処理することが可能になります。このような変更の衝突処理は、すべてのソースコードが多数の小さなテキストファイルに存在する従来のソフトウェア開発では非常に一般的です。このような理由から、一般的なバージョン管理システムは、変更の検出と（自動的な）競合解決を目的として、この種のファイルに最適化されています。

表計算モデルの開発では、JSONベースのTOMメタデータが「ソースコード」となります。以前のバージョンのVisual Studioで表形式モデルを開発する場合、Model.bim JSONファイルには、誰がいつ何を変更したかという情報が追加されていました。この情報は、単純にファイル内のJSONオブジェクトの追加プロパティとして保存されていました。これには問題がありました。というのも、情報が冗長であるだけでなく（ファイル自体にも、最後に編集した人や最後に編集した時間を記述したメタデータがあるため）、バージョン管理の観点からは、このメタデータは意味を持ちません。言い換えれば、ファイルから修正メタデータをすべて削除しても、モデルの機能やビジネスロジックに影響を与えることなく、分析サービスにデプロイしたり、Power BIにパブリッシュしたりすることができる、完全に有効なTOM JSONファイルができあがります。

従来のソフトウェア開発におけるソースコードのように、このような情報がモデルのメタデータを "汚染 "することは望ましくありません。実際、バージョン管理システムでは、誰がいつ、何のために行った変更かをより詳細に見ることができるので、バージョン管理されているファイルの一部として含める理由はありません。

Tabular Editorが作られた当初は、Visual Studioで作成されたModel.bimファイルからこの情報を取り除くオプションはありませんでしたが、幸いにも最近のバージョンでは変更されました。しかし、モデルを定義するすべての「ソースコード」を含む単一の一枚岩のファイル（Model.bimファイル）を処理する必要があることに変わりはありません。

Power BIのデータセット開発者は、モデルのメタデータを含むテキストベースのファイルにアクセスすることすらできないため、さらに不利です。彼らができることは、Power BIレポートをPower BIテンプレート（.pbit）ファイルとしてエクスポートすることだけです。このファイルは、基本的にレポートページ、データモデル定義、およびクエリ定義を含むZIPファイルです。バージョン管理システムの観点からすると、ZIPファイルはバイナリファイルであり、バイナリファイルはテキストファイルのように差分を取ったり、比較したり、マージしたりすることができません。このため、Power BIの開発者は、データモデルを適切にバージョン管理するために、サードパーティのツールを使用したり、手の込んだスクリプトやプロセスを考え出したりしなければなりません。

Tabular Editorは、Analysis ServicesのタブラーモデルやPower BIのデータセットにかかわらず、タブラーオブジェクトモデルから意味のあるメタデータだけを抽出する簡単な方法を提供することで、このプロセスを簡素化することを目的としています。さらに、Tabular Editorは、Save to Folder機能を使用して、このメタデータを複数の小さなファイルに分割することができます。

Save to Folderとは？
前述したように、タブラーモデルのモデルメタデータは、従来はModel.bimという名前の単一のモノリシックなJSONファイルに保存されており、バージョン管理の統合には適していません。このファイルのJSONはTabular Object Model (TOM)を表しているので、このファイルをより小さなピースに分割する簡単な方法があることがわかりました。TOMには、モデル内のテーブルのリスト、テーブル内のメジャーのリスト、メジャー内のアノテーションのリストなど、ほぼすべてのレベルのオブジェクトの配列が含まれています。Tabular EditorのSave to Folder機能を使用すると、これらの配列は単純にJSONから削除され、代わりに元の配列の各オブジェクトに対応する1つのファイルを含むサブフォルダが生成されます。このプロセスは入れ子にすることができます。その結果、フォルダ構造になります。各フォルダには、より小さなJSONファイルとサブフォルダのセットが含まれ、意味的には元のModel.bimファイルとまったく同じ情報を含んでいます。

../images/save-to-folder.png

個々のTOMオブジェクトを表す各ファイルの名前は、オブジェクト自体のNameプロパティに基づいています。ルート」ファイルの名前がDatabase.jsonであることから、フォルダベースの保存形式を単にDatabase.jsonと呼ぶことがあります。

Save to Folderを使うメリット
表形式モデルのメタデータをフォルダベースのフォーマットで保存することの利点を以下に示します。

多くのバージョン管理システムでは、いくつかの大きなファイルよりも、複数の小さなファイルの方がうまく機能します。例えば、gitは変更されたファイルのスナップショットを保存します。この理由だけでも、モデルを1つの大きなファイルとして保存するよりも、複数の小さなファイルとして表現した方が良いことが理解できます。
配列の順序変更時の衝突を避ける。テーブル、メジャー、カラムなどのリストは、Model.bimのJSONでは配列として表現されます。しかし、配列内のオブジェクトの順序は重要ではありません。モデルの開発中に、カット・アンド・ペーストの操作などでオブジェクトの順序が入れ替わることは珍しくありません。Save to Folderでは、配列オブジェクトが個別のファイルとして保存されるため、配列の変更履歴が残らず、マージコンフリクトのリスクが軽減されます。
異なる開発者が同じファイルを変更することはほとんどありません。データモデルの別々の部分を開発する限り、開発者が同じファイルを変更することはほとんどないので、マージコンフリクトのリスクが軽減されます。
Save to Folderを使うことの欠点
現状では、タブラーモデルのメタデータをフォルダベースのフォーマットで保存することの唯一の欠点は、このフォーマットがTabular Editorでのみ使用されることです。つまり、フォルダベースのフォーマットからモデルのメタデータをVisual Studioに直接ロードすることはできません。代わりに、一時的にフォルダベースのフォーマットをModel.bimフォーマットに変換する必要がありますが、これはもちろんTabular Editorを使って行うことができます。

フォルダへの保存を設定する
1つのサイズがすべてに適合することはほとんどありません。Tabular Editorには、モデルをフォルダ構造にシリアライズする方法に影響を与えるいくつかの設定オプションがあります。Tabular Editor 3では、Tools > Preferences > Save-to-folderで一般的な設定を確認できます。モデルがTabular Editorに読み込まれると、そのモデルに適用される特定の設定は、Model > Serialization options....。特定のモデルに適用される設定は、モデル自体にアノテーションとして保存され、どのユーザがモデルを読み込んだり保存したりしても、同じ設定が使用されるようになっています。

.../images/configuring-save-to-folder.png

シリアル化の設定
推奨される設定を使用します。(デフォルト：チェック）これをチェックすると、Tabular Editorはモデルをフォルダ構造として初めて保存するときにデフォルトの設定を使用します。
from-tablesでリレーションシップをシリアライズします。(Default: unchecked) これをチェックすると、Tabular Editorはリレーションシップをモデルレベルで保存するのではなく、リレーションシップの「from-side」にあるテーブル（通常はファクトテーブル）のアノテーションとして保存します。これは、テーブル名が頻繁に変更されるようなモデルの初期開発段階で便利です。
オブジェクトのパースペクティブ・メンバーシップ情報をシリアライズする。(デフォルト: チェックなし) これをチェックすると、Tabular Editorは、オブジェクト（テーブル、列、階層、メジャー）がどのパースペクティブに属しているかという情報を、パースペクティブレベルで保存するのではなく、そのオブジェクトのアノテーションとして保存します。これは、オブジェクト名は変更される可能性があるが、パースペクティブ名は確定している場合に便利です。
翻訳されたオブジェクトに翻訳をシリアライズする。(デフォルト：チェックなし）これをチェックすると、Tabular Editorはメタデータの翻訳をカルチャレベルで保存するのではなく、翻訳可能な各オブジェクト（テーブル、カラム、階層、レベル、メジャーなど）のアノテーションとして保存します。これは、オブジェクト名が変更される可能性がある場合に便利です。
Prefix file names sequentially: (デフォルト：チェックなし）配列メンバーのメタデータの順序（テーブルの列の順序など）を保持したい場合、これをチェックすると、Tabular Editorがファイル名の前に配列内のオブジェクトのインデックスに基づく連番の整数を付けることができます。これは、Excelのデフォルトのドリルスルー機能を使用していて、ドリルスルーで列が特定の順序で表示されるようにしたい場合に便利です。
注意
上記の設定の主な目的は、モデルのメタデータをどこにどのように保存するかを調整することで、モデル開発時に発生するマージ・コンフリクトの数を減らすことにあります。モデル開発の初期段階では、オブジェクトの名前が頻繁に変更されることが珍しくありません。もしモデルに既にメタデータのトランスレーションが指定されている場合、オブジェクトの名前を変更するたびに、少なくとも2つの変更が発生します。1つは名前を変えられたオブジェクトの変更、もう1つはそのオブジェクトの翻訳を定義しているすべてのカルチャの変更です。翻訳されたオブジェクトの翻訳をシリアライズすることがチェックされている場合、そのオブジェクトは翻訳された値も含んでいるので（この情報はアノテーションとして保存されているので）、名前が変更されるオブジェクトにのみ変更があります。

シリアル化の深さ
チェックリストでは、どのオブジェクトを個々のファイルとしてシリアル化するかを指定できます。なお、上記の設定によっては、一部のオプション（パースペクティブ、トランスレーション、リレーションシップ）が利用できない場合があります。

ほとんどの場合、オブジェクトを常に最下位レベルまでシリアル化することが推奨されます。しかし、このレベルの詳細が必要でない特別なケースもあるでしょう。

Power BIとバージョン管理
前述したように、Power BIレポート(.pbix)やPower BIテンプレート(.pbit)のファイルをバージョンコントロールに統合しても、これらのファイルがバイナリファイル形式を使用しているため、並行開発やコンフリクトの解決ができません。同時に、Power BI DesktopやPower BI XMLAエンドポイントでTabular Editor（または他のサードパーティツール）をそれぞれ使用する際には、現状では制限があることを認識する必要があります。

これらの制限は

Power BI Desktopの外部ツールとしてTabular Editorを使用する場合、すべてのモデリング操作がサポートされているわけではない。
Tabular Editorは、Power BI Desktopで読み込まれた.pbixファイル、またはディスク上の.pbitファイルから直接モデルメタデータを抽出することができますが、Power BI Desktopの外部で.pbixまたは.pbitファイルのモデルメタデータを更新する方法はサポートされていません。
XMLAエンドポイントを通じてPower BIデータセットに変更を加えると、そのデータセットを.pbixファイルとしてダウンロードすることはできなくなります。
並行開発を可能にするためには、モデルのメタデータを、上記のテキストベース（JSON）のフォーマット（Model.bimまたはDatabase.json）のいずれかで保存できるようにする必要があります。テキストベースのフォーマットから.pbixや.pbitファイルを「再現」する方法はありませんので、このルートを選択すると、Power BI Desktopを使ってデータモデルを編集することができなくなります。代わりに、JSONベースのフォーマットを使用できるツールに頼ることになりますが、これこそがTabular Editorの目的です。

警告
Power BI Premium ワークスペース（Premium capacity または Premium-Per-User）にアクセスできない場合は、JSON ファイルに保存されたモデルのメタデータを公開することができません。

注意
レポートのビジュアル部分を作成するためには、Power BI Desktopが必要です。レポートとモデルは常に分離することがベストプラクティスです。両方を含む既存のPower BIファイルがある場合は、このブログ記事（ビデオ）で、モデルファイルとレポートファイルに分割する方法を説明しています。

タブラルエディタとgit
Gitはフリーでオープンソースの分散型バージョン管理システムで、小規模なプロジェクトから大規模なプロジェクトまで、迅速かつ効率的に処理できるように設計されています。現在最も普及しているバージョンコントロールシステムで、Azure DevOps、GitHub、GitLabなどの複数のホスト型オプションで利用できます。

gitの詳細な説明は、この記事の範囲外です。しかし、もっと詳しく知りたい方は、オンラインで多くの資料を入手することができます。参考までに、『Pro Git』という本をお勧めします。

注意
Tabular Editor 3は現在、gitや他のバージョンコントロールシステムとの統合を行っていません。gitリポジトリを管理したり、コードの変更をコミットしたり、ブランチを作成したりするには、gitコマンドラインか、Visual Studio Team ExplorerやTortoiseGitのような別のツールを使用する必要があります。

先に述べたように、モデルのメタデータをgitコードリポジトリに保存する際には、Tabular EditorのSave to Folderオプションを使用することをお勧めします。

分岐戦略
ここからは、タブラーモデルを開発する際に採用する分岐戦略について説明します。

分岐戦略は、日々の開発ワークフローを決定し、多くの場合、分岐はチームが使用するプロジェクトメソッドに直接結びつきます。例えば、Azure DevOpsのアジャイルプロセスを使用する場合、バックログは、エピック、フィーチャー、ユーザーストーリー、タスク、バグで構成されます。

アジャイルの用語では、ユーザーストーリーとは、納品可能でテスト可能な作品のことです。ユーザーストーリーは、いくつかのタスクで構成されている場合があります。タスクとは、ユーザーストーリーを納品する前に、通常は開発者が実行する必要のある小さな作業のことです。理想的な世界では、すべてのユーザーストーリーが管理可能なタスクに分割されており、それぞれが数時間で完了し、ユーザーストーリー全体では数日しかかからないことになります。このようなユーザーストーリーは、いわゆるトピックブランチの理想的な候補となり、開発者はユーザーストーリー内の各タスクに対して1つまたは複数のコミットを行うことができます。すべてのタスクが完了したら、ユーザーストーリーをクライアントに納品します。このとき、トピックブランチは納品用のブランチ (たとえば「Test」ブランチ) にマージされ、コードはテスト環境にデプロイされます。

適切なブランチ戦略を決定するには、さまざまな要因があります。一般的にMicrosoftは、アジャイルで小刻みな継続的デリバリーを実現するために、Trunk-based Development（ビデオ）戦略を推奨しています。主なアイデアは、新機能やバグフィックスごとに「Main」ブランチからブランチを作成することです（以下の画像を参照）。コードレビューのプロセスは、機能ブランチからMainへのプルリクエストを通じて実施され、Azure DevOpsのBranch Policy機能を使って、プルリクエストが完了する前にコードがクリーンにビルドされることを要求するルールを設定することができます。

.../images/trunk-based-development.png

トランクベースの開発
しかし、このような戦略は、いくつかの理由から、ビジネスインテリジェンスの開発チームでは実現できないかもしれません。

新機能の開発には、ビジネスユーザーによる長時間のテストと検証が必要になることが多く、完了までに数週間かかることもあります。新しい機能は、ビジネスユーザによる長期間のテストと検証を必要とすることが多く、完成までに数週間かかることもあります。
BIソリューションは多層構造になっており、通常、ETLを備えたデータウェアハウス層、マスターデータ管理層、セマンティック層、レポートなどで構成されています。これらの層の間には依存関係が存在し、テストと展開をさらに複雑にしています。
BIチームは、ビジネスのさまざまな分野（販売、在庫、物流、財務、人事など）に対応する、成熟度や開発ペースが異なる複数の異なるセマンティックモデルの開発と保守を担当する場合があります。
BIソリューションの最も重要な側面はデータです。BI開発者は、ソースコントロールからコードをチェックアウトしてF5キーを押すだけで、コードをコンパイルするのに必要な数分で完全なソリューションを実行できるような贅沢はできません。ソリューションにはデータが必要であり、そのデータをロードしたり、ETLしたり、複数のレイヤで処理したりして、エンドユーザに提供する必要があります。DevOpsのワークフローにデータを含めると、構築やデプロイにかかる時間が数分から数時間、さらには数日にまで短縮されます。場合によっては、リソースや経済的な制約のために、それができないこともあります。
BIチームが、BIソリューション全体のどのレイヤーでも並行して開発をサポートし、テスト準備が整った機能を組み合わせられるようなブランチ戦略から恩恵を受けることは間違いありません。しかし、特に上記の最後の箇条書きにより、データをどのように処理するかを注意深く考える必要があります。例えば、ディメンションに新しい属性を追加した場合、ビルドやデプロイメントのパイプラインの一部としてディメンションを自動的にロードするのか？そのようなディメンションのロードに数分しかかからないのであれば問題ないでしょうが、数十億行のファクトテーブルに新しいカラムを追加する場合はどうでしょうか。また、開発者が新機能の開発を並行して行っている場合、各開発者は独自の開発データベースを持つべきなのか、それとも共有データベースでお互いの足を踏まないようにするにはどうすればよいのか。

特に、BIソリューションのすべての階層や、地球上のBIチームの異なる配置や好みのワークフローを考慮すると、上記の質問に対する簡単な答えはありません。また、実際の構築、展開、テストの自動化については、主にAnalysis Servicesに焦点を当てることになります。ETL層やデータベース層には、DevOpsの観点から独自の課題がありますが、それについてはこの記事の範囲外です。しかし、先に進む前に、別のブランチ戦略と、それをBIワークフローに採用する可能性について見てみましょう。

GitFlowの分岐と導入環境
以下で説明する戦略は、Vincent Driessen氏によるGitFlowに基づいています。

.../images/gitflow.png

このようなブランチ戦略を導入することで、ブランチとデプロイ環境の関連性を考えれば、BIチームが一般的に遭遇するDevOpsの問題のいくつかを解決することができます。理想的な世界では、GitFlow を完全にサポートするためには少なくとも 4 つの異なる環境が必要です。

本番環境。masterブランチのHEADにあるコードが常に含まれている必要があります。
カナリア環境。常に develop ブランチの HEAD にコードが含まれている必要があります。ここでは通常、毎晩のデプロイメントをスケジュールしたり、統合テストを実行したりして、本番環境への次のリリースで使用する機能がうまく動作することを確認します。
1つまたは複数のUAT環境で、ユーザーとビジネスユーザーが新機能のテストと検証を行います。デプロイメントは、テストが必要なコードを含む機能ブランチから直接行われます。複数の新機能を並行してテストしたい場合は、複数のテスト環境が必要になります。BI階層間の依存関係を慎重に考慮すれば、多少の調整作業を経て、通常は1つのテスト環境で十分です。
上記の環境に影響を与えずに新機能を開発できる、1つ以上のサンドボックス環境。テスト環境と同様に、通常は1つの共有サンドボックス環境があれば十分です。
ここで強調しておきたいのは、これらの検討事項に対する「万能のソリューション」は実際には存在しないということです。例えば、クラウドでソリューションを構築していない場合、数秒から数分で新しいリソースを立ち上げるような拡張性や柔軟性はありません。あるいは、データボリュームが非常に大きく、リソース、経済性、時間の制約から環境を複製することが現実的ではない場合もあります。次の段階に進む前に、本当に並列開発やテストをサポートする必要があるのかどうかを自問してみてください。ステークホルダーが数人しかいない小規模なチームの場合は、CI/CD の恩恵を受けることはできても GitFlow のブランチ機能は過剰になる可能性があり、そのようなケースはほとんどありません。

並行開発をサポートする必要がある場合でも、複数の開発者が同じ開発環境やサンドボックス環境を共有するのは簡単で、それほど大きな問題は発生しないでしょう。しかし、特に表形式のモデルについては、「お互いに足を踏み入れる」ことを避けるために、開発者が個別のワークスペース・データベースを使用することをお勧めします。

共通のワークフロー
すでにgitリポジトリがセットアップされていて、ブランチ戦略に沿っているとすると、タブラーモデルの「ソースコード」をリポジトリに追加するには、Tabular Editorを使ってメタデータをローカルリポジトリの新しいブランチに保存するだけで済みます。次に、新しいファイルをステージングしてコミットし、ブランチをリモートリポジトリにプッシュし、プルリクエストを作成してブランチをメインブランチにマージします。

正確なワークフローは、あなたのブランチ戦略や git リポジトリの設定に依存します。一般的には、次のようなワークフローになります。

新しい機能の開発を始める前に、git で新しい機能ブランチを作成する。トランクベースの開発シナリオでは、次のような git コマンドでメインブランチをチェックアウトして最新版のコードを取得し、そこから機能ブランチを作成します。
git checkout main
git pull
git checkout -b "feature\AddTaxCalculation"
ローカルのgitリポジトリからモデルのメタデータをTabular Editorで開きます。DAXコードのテストやデバッグをしやすくするために、ワークスペースデータベースを使うのが理想的です。
Tabular Editorを使用して、モデルに必要な変更を加えます。変更を継続的に保存します（CTRL+S）。保存した後にコードの変更を定期的にgitにコミットすることで、作業のロスを防ぎ、変更したすべての履歴を残すことができます。
git add .
git commit -m "Description of what was changed and why since last commit"
git push
ワークスペースデータベースを使用していない場合は、Tabular EditorのModel > Deploy...オプションを使用してサンドボックス/開発環境にデプロイし、モデルのメタデータに加えられた変更をテストします。
すべてのコードがコミットされてリモートリポジトリにプッシュされたら、メインブランチにコードを統合するためにプルリクエストを送信します。マージの際にコンフリクトが発生した場合は、ローカルで解決しなければなりません。たとえば Visual Studio Team Explorer を使ったり、.json ファイルをテキストエディタで開いてコンフリクトを解決したりします (git は、コードのどの部分でコンフリクトが発生しているかを示すコンフリクトマーカーを挿入します)。
すべてのコンフリクトが解決されると、コードレビューやブランチポリシーに基づく自動ビルド/テストの実行などのプロセスを経てプルリクエストが完成します。ただし、これはブランチ戦略と全体的な設定に依存します。
以下の記事では、Azure DevOpsを使って、gitブランチポリシーの設定、自動ビルドやデプロイメントパイプラインの設定などの詳細を紹介しています。同様の手法は、TeamCityやGitHubなど、他の自動ビルド環境やgitホスティング環境でも使用できます。

次のステップ
Azure DevOpsとTabular EditorによるPower BIのCI/CD
Azure DevOpsとTabular Editorを使用したAnalysis ServicesのCI/CD
ワークスペースモードによる開発ワークフローの最適化