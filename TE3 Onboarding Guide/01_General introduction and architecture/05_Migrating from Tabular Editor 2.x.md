---
uid: migrate-from-te2
title: Migrating from Tabular Editor 2.x
author: Daniel Otykier
updated: 2021-09-30
---

# Tabular Editor 2.xからの移行

この記事は、Power BI DatasetやAnalysis ServicesのTabular開発にTabular Editor 2.xを使用した経験がある開発者を対象としています。この記事では、Tabular Editor 3の類似点と重要な機能追加を紹介し、すぐに使いこなせるようにします。

## インストールを横並びにする

Tabular Editor 3の製品コードはTabular Editor 2.xとは異なります。つまり、両方のツールを問題なく並べてインストールできます。実際には、それぞれのツールは別のプログラムフォルダーにインストールされ、設定も別のフォルダーに保管されます。つまり、Tabular Editor 2.xとTabular Editor 3の間の「アップグレード」や「ダウングレード」という言葉は当てはまりません。タブラー・エディターー3は、まったく別の製品と考えたほうがいいでしょう。

## 機能比較

機能面では、いくつかの例外を除き、Tabular Editor 3は基本的にTabular Editor 2.xのスーパーセットです。以下の表は、2つのツールのすべての主要機能を比較したものです。

||Tabular Editor 2.x|Tabular Editor 3
|---|---|---|
|すべてのTOMオブジェクトとプロパティの編集|<span class="emoji">&#10004;</span>|<span class="emoji">&#10004;</span>|<span class="emoji"></span>。
|バッチ編集と名前の変更|<span class="emoji">&#10004;</span>|<span class="emoji">&#10004;</span>|
|コピー/ペーストとドラッグ/ドロップのサポート|<span class="emoji">&#10004;</span>|<span class="emoji">&#10004;</span>|。
|データモデリング操作の取り消し/やり直し|<span class="emoji">&#10004;</span>|<span class="emoji">&#10004;</span>|。
|モデルのメタデータをディスクにロード/セーブ|<span class="emoji">&#10004;</span>|<span class="emoji">&#10004;</span>*|。
|Save-to-folder|<span class="emoji">&#10004;</span>|<span class="emoji">&#10004;</span>*|
|[daxformatter.com](https://daxformatter.com)の統合|<span class="emoji">&#10004;</span>|<span class="emoji">&#10004;</span>|。
|高度なデータモデリング（OLS、Perspective、Calculation Groups、Metadata Translationsなど）|<span class="emoji">&#10004;</span>|<span class="emoji">&#10004;</span>*|。
|Syntax Highlighting and automatic formula fixup|<span class="emoji">&#10004;</span>|<span class="emoji">&#10004;</span>|。
|オブジェクト間のDAX依存関係の表示|<span class="emoji">&#10004;</span>|<span class="emoji">&#10004;</span>|。
|テーブルのインポートウィザード|<span class="emoji">&#10004;</span>|<span class="emoji">&#10004;</span>|
|デプロイメント・ウィザード|<span class="emoji">&#10004;</span>|<span class="emoji">&#10004;</span>*|
|ベストプラクティス・アナライザー|<span class="emoji">&#10004;</span>|<span class="emoji">&#10004;</span>|
|C# スクリプトとオートメーション|<span class="emoji">&#10004;</span>|<span class="emoji">&#10004;</span>|
|Power BI Desktopの外部ツールとして使用する|<span class="emoji">&#10004;</span>|<span class="emoji">&#10004;</span>|。
|SSAS/Azure AS/Power BI Premiumへの接続|<span class="emoji">&#10004;</span>|<span class="emoji">&#10004;</span>*|。
|コマンドラインインターフェイス|<span class="emoji">&#10004;</span>|*[Coming Soon](xref:roadmap)*|。
|プレミアム、カスタマイズ可能なユーザーインターフェイス、高DPI、マルチモニター、テーマサポート||<span class="emoji">&#10004;</span>|[Coming Soon](xref:roadmap)
|IntelliSense<sup>TM</sup>のような機能を備えたワールドクラスのDAXエディターー||<span class="emoji">&#10004;</span>|
|オフラインでのDAX構文チェックとカラム/データタイプの推論||<span class="emoji">&#10004;</span>|
|Power Queryに対応したテーブルインポートウィザードとテーブルスキーマアップデートのチェック機能の改善||<span class="emoji">&#10004;</span>|
|DAXクエリー、テーブルプレビュー、ピボットグリッド||<span class="emoji">&#10004;</span>|
|テーブルの関係を視覚化し、編集するためのダイアグラムの作成||<span class="emoji">&#10004;</span>|
|データのリフレッシュ操作をバックグラウンドで実行||<span class="emoji">&#10004;</span>*|
|C# マクロの記録||<span class="emoji">&#10004;</span>|
|DAXスクリプトを使って1つのドキュメントで複数のDAX式を編集||<span class="emoji">&#10004;</span>|
|[VertiPaq Analyzer](https://www.sqlbi.com/tools/vertipaq-analyzer/)の統合||<span class="emoji">&#10004;</span>|

\* Tabular Editor 3のどのエディションを使用しているかによって制限事項が異なります。

## 機能の違い

重要な機能の違いを以下にまとめました。

### ユーザーインターフェイス

Tabular Editor 3を起動して最初に気づくのは、Visual Studio Shellに似た新しいインターフェイスです。このインターフェイスは完全にカスタマイズ可能で、高dpi、マルチモニターに対応し、テーマの変更も可能です。すべてのインターフェイス要素は別の場所に移動することができるので、Tabular Editor 2.xのインターフェイスレイアウトが好きな人は、すぐに**Window**メニューから**Classic layout**を選択してください。

一般的に、Tabular Editor 2.xに存在するインターフェイス要素はTabular Editor 3でも同じ名前なので、新しいインターフェイスを操作するのは比較的簡単です。いくつかの重要な違いを以下に示します。

- Tabular Editor 2.xにあった**Advanced Scripting**タブがなくなりました。Tabular Editor 3では、代わりに**File > New**メニューを使用して*C# Scripts**を作成します。一度に1つのスクリプトしか扱えないという制限はありません。また、**カスタムアクション**は **マクロ**に名称変更されました。
- **Dynamic LINQフィルタリング**は、現在TOMエクスプローラーでは利用できません。代わりに、[Dynamic LINQ](https://dynamic-linq.net/expression-language)を使用してオブジェクトを検索したい場合は、CTRL+Fを押して**Find and replace**ダイアログを表示する必要があります。
- **エクスプレッション・エディター**を閉じた場合、**TOMエクスプローラー**のオブジェクトのアイコンをダブルクリックするか、**表示>エクスプレッション・エディター**のメニューオプションを選択することで、元に戻すことができます。
- Tabular Editor 3のデフォルトのレイアウトを使用する場合、**Best Practice Analyzer**は**TOM Explorer**の隣のタブとして配置されます。ここには、新しい**Data Refresh**ビュー（バックグラウンドのリフレッシュ操作のキューを表示）と**Macros**ビュー（C#スクリプトから保存されたマクロを管理）もあります。
- Tabular Editor 3では、すべてのDAX構文およびセマンティックエラーが新しい**メッセージビュー**に表示されます。デフォルトのレイアウトでは、このビューはインターフェイスの左下に配置されています。
- さらに、Tabular Editor 3には、**VertiPaq Analyzer**（[DAX Studio](https://daxstudio.org/)でおなじみ）が搭載されています。
- 最後になりますが、Tabular Editor 3では**documents**という概念が導入されており、これはC#スクリプト、DAXスクリプト、DAXクエリ、ダイアグラム、データプレビュー、ピボットグリッドなどの総称に過ぎません。

詳細については、<xref:user-interface>を参照してください。

### 新しいDAXエディターとセマンティック機能

Tabular Editor 3には独自のDAX解析エンジン（別名「セマンティック・アナライザー」）が搭載されており、モデル内のあらゆるDAXコードのセマンティックを理解できます。このエンジンは、DAXエディター（コードネーム "Daxscilla"）にも使用されており、シンタックスハイライギング、自動フォーマット、コード補完、コールチップ、リファクタリングなどの機能を実現しています。もちろん、このエディターは高度な設定が可能で、好みのDAXコーディングスタイルに合わせて微調整できます。

新しいDAXエディターの詳細については、<xref:dax-editor>を参照してください。

さらに、セマンティック アナライザーは、モデル内のすべてのオブジェクトのDAX構文やセマンティック エラーを継続的に報告します。これは、Analysis Servicesに接続されていなくても機能し、非常に高速です。セマンティック・アナライザーは、Tabular Editor 3がDAX式からデータ型を自動的に推論することも可能にしました。つまり、Tabular Editor 3は、計算されたテーブル式の結果としてどの列になるかを自動的に検出します。これは、計算されたテーブルの列を手動でマッピングするか、分析サービスが列のメタデータを返してくれるのを頼りにする必要があったTabular Editor 2.xと比べて、大きな改善です。

### テーブルのインポートとスキーマの更新とPower Queryのサポート

Tabular Editor 3がTabular Editor 2.xに比べてもう1つの大きな利点は、構造化されたデータソースとPower Query（M）パーティションをサポートしていることです。具体的には、「スキーマの更新」機能がこれらのタイプのデータソースやパーティションでも機能するようになり、テーブルのインポートウィザードでは、新しいテーブルをインポートする際に必要なMコードを生成できるようになりました。

スキーマ比較ダイアログ自体にもいくつかの改良が加えられており、たとえば、列削除+列挿入の操作を単一の列名変更の操作に簡単にマッピングできるようになりました（逆も同様）。また、浮動小数点データ型と10進数データ型をどのように扱うかを制御するオプションもあります（たとえば、データソースが浮動小数点データ型を使用していても、常に10進数データ型としてインポートしたい場合があります）。

詳しくは <xref:importing-tables> をご覧ください。

### ワークスペースモード

このモードでは、モデルのメタデータがディスク（Model.bimまたはDatabase.json）から読み込まれ、選択したAnalysis Servicesインスタンスに直ちにデプロイされます。保存（CTRL+S）を押すたびに、ワークスペース データベースが同期され、更新されたモデル メタデータがディスクに保存されます。この方法の利点は、Tabular EditorがAnalysis Servicesに接続されているため、以下の[接続された機能](#connected-features)が可能になるだけでなく、ディスク上のソースファイルの更新が容易になることです。Tabular Editor 2.xでは、データベースからモデルを開き、たまに手動でディスクに保存することを忘れないようにする必要がありました。

このアプローチは、[並行開発](xref:parallel-development)や、モデルのメタデータをバージョン管理システムと統合することを可能にするのに理想的です。

詳しくは <xref:workspace-mode> を参照してください。

### 接続された機能

Tabular Editor 3にはいくつかの新しい接続機能があり、Analysis Servicesのクライアントツールとして使用できます。これらの機能は、Tabular Editor 3がAnalysis Servicesに直接、または[ワークスペースモード](#workspace-mode)機能を使用して接続されている場合に有効となります。

接続された新機能は以下の通りです。

- テーブルデータのプレビュー
- ピボットグリッド(PivotGrids)
- DAXクエリー
- データのリフレッシュ操作
- VertiPaqアナライザー

### ダイアグラム

Tabular Editor 2.xの機能の中で要望が多かったのは、テーブル間の関係をよりよく視覚化できることでした。Tabular Editor 3では、モデル図を作成できるようになりました。各ダイアグラムは、ダイアグラムに含まれるテーブルの名前と座標を保持するシンプルなJSONファイルです。その後、Tabular Editor 3はテーブルと関係をレンダリングし、関係を簡単に編集したり、既存の関係に基づいて図に追加のテーブルを追加したりする機能を提供します。

![簡単に関連テーブルを追加](~/images/diagram-menu.png)

詳細は、[ダイアグラムでの作業](xref:importing-tables-data-modeling#working-with-diagrams)を参照してください。

### C# スクリプトとマクロレコーダー

Tabular Editor 2.xの**Advanced Scripting**機能は、**C# Scripts**としてTabular Editor 3に引き継がれています。タブラー・エディターー3の重要な違いの1つは、1つのスクリプトでの作業に制限されなくなったことです。代わりに、**ファイル > 新規作成 > C#スクリプト**オプションを使用して、必要な数のC#スクリプトを作成して作業することができます。タブラー・エディターー2.xと同様に、これらのスクリプトは再利用可能なアクションとして保存でき、TOMエクスプローラーの右クリックコンテキストメニューに直接統合されます。タブラエディター3では、これらのアクションを**マクロ**と呼んでおり、マクロを追加できる独自のメニューやツールバーを作成することもできます。

最も重要なのは、タビュラー・エディターー3には**マクロ・レコーダー**が搭載されており、ユーザーのインタラクションからC#コードを自動的に生成するのに使用できることです。

> [!!! 注意]
> この記事を書いている時点では、Tabular Editor 3のC#スクリプトエディターにはインテリセンスのような機能は含まれていません。これは後ほどリリースされる予定です。詳しくは、@cs-scripts-and-macrosをご覧ください。

### DAXスクリプティング

Tabular Editor 2.xの最後の重要な機能として、**DAX Scripting**があります。この機能を使うと、複数の計算オブジェクトのDAX式や基本的なプロパティを一度に編集できるドキュメントを作成することができます。計算されたオブジェクトとは、メジャー、計算された列、計算されたテーブルなどです。

この機能は、複数のオブジェクトにまたがる複雑なビジネスロジックをオーサリングする際に非常に便利です。TOMエクスプローラーでオブジェクトを（複数）選択し、右クリックして**Script DAX**オプションを選択すると、選択されたすべてのオブジェクトの定義を含む新しいDAXスクリプトが得られます。DAXスクリプトエディターは、もちろんエクスプレッションエディターやDAXクエリエディターと同じDAX機能を持っています。

DAXスクリプトは、**connected**または**workspace**モードで作業している場合、更新されたビジネスロジックを迅速に修正およびテストするための非常に強力なツールです。例えば、以下のスクリーンショットに示すように、ピボットグリッドと組み合わせて使用する場合などです。SHIFT+F5を押すだけで、スクリプト内のDAX式に基づいてデータベースが更新され、その後すぐにピボットグリッドが更新されます。

![Dax Scripting And Pivot](https://docs.tabulareditor.com/images/dax-scripting-and-pivot.png)

詳しくは、@dax-script-introductionをご覧ください。

## 次のステップ

- 移行元と移行先
- 並行開発
- 生産性向上のための取り組み
