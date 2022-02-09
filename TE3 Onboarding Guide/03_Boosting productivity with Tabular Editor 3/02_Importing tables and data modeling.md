---
uid: importing-tables-data-modeling
title: Importing tables and data modeling
author: Daniel Otykier
updated: 2021-10-08
applies_to:
  editions:
    - edition: Desktop
      partial: TE3 Desktop Edition includes this feature. External tools adding/editing tables, columns and relationships against a Power BI Desktop model is not supported by Microsoft, however.
    - edition: Business
    - edition: Enterprise
---

# テーブルのインポートとデータモデリング

この記事では、Tabular Editor 3の[テーブルインポートウィザード](#table-import-wizard)を使って、モデルに新しいテーブルを追加する方法を説明します。また、既存のテーブルの[update the table schema](#updating-table-schema)の方法についても説明します。最後に、テーブル間の関係を定義・編集するための[ダイアグラムツール](#working-with-diagrams)の使い方を説明します。

## テーブルインポートウィザード

Tabular Editor 3にはテーブルインポートウィザードがあり、モデルにデータソースを作成したり、SQL Serverデータベースなどのリレーショナルデータソースからテーブル/ビューをインポートができます。

![importing-tables1](https://docs.tabulareditor.com/images/import-tables-wizard.png)

## TOMデータソースの種類

Analysis Servicesのバージョンによって、モデルのメタデータ内でデータソースを定義する方法が異なります。

- Provider (aka. Legacy): Analysis Servicesのすべてのバージョン、すべての互換性レベルで利用できます。限られた範囲のソースをサポートしており、主にOLE DB/ODBCライバによるリレーショナルデータをサポートしています。パーティションは通常、ソースに対してネイティブに実行されるSQLステートメントを使用して定義されます。認証情報は、Tabular Object ModelのProvider Data Sourceオブジェクトで管理され、サーバー側で保存および暗号化されます。
- Structured (aka. Power Query): SQL Server 2017（互換性レベル1400+）から利用可能。レガシープロバイダーよりも広範囲のデータソースをサポートします。パーティションは通常、M（Power Query）式を使って定義します。認証情報はTabular Object ModelのStructured Data Sourceオブジェクトで管理され、Analysis Servicesへの展開時に毎回指定する必要があります。
- Implicit data sources: Power BIデータセットでのみ使用されます。モデル内に明示的なデータソースオブジェクトは作成されません。代わりに、M（Power Query）式でデータソースを暗黙的に定義します。認証情報はTabular Object Modelには保存されず、Power BI DesktopまたはPower BI Serviceによって管理されます。

>注意
>Tabular Editor 2.x のテーブルインポートウィザードとテーブルスキーマの更新機能は、SQL パーティションを持つレガシーデータソースのみをサポートします。つまり、Power Query パーティションには対応していません。このため、開発者ツール間の相互運用性が最も高いレガシーデータソースを使用することをお勧めします。

## ダイアグラムの使用

Tabular Editor 3では、**ダイアグラム**は、モデル内のテーブル間の関係を視覚化したり編集したりするために使用できるドキュメントです。モデルの特定の領域を視覚化するために、必要な数のダイアグラムを作成できます。ダイアグラムは単独のファイルとして保存ができます。詳しくは、<xref:supported-files#diagram-file-te3diag>をご覧ください。

> [!Note]
> 大きなダイアグラムよりも小さなダイアグラムを複数作成することをお勧めします。1つのダイアグラムに20個以上のテーブルが含まれていると、すぐに圧倒され、理解するのが難しくなります。

Tabular Editor 3でモデルを読み込んだ後、**File > New > Diagram**メニューオプションを選択し、新しいダイアグラムを作成します。

## テーブルの追加

以下のいずれかの方法で、ダイアグラムに初期のテーブルを追加します。

- TOMエクスプローラーでテーブルを複数選択し、右クリックして**ダイアグラムに追加**を選択します。
- TOMエクスプローラーでテーブルを複数選択し、ダイアグラムにテーブルをドラッグする。
- **ダイアグラム＞テーブルの追加...** メニューオプションを使用し、ダイアログボックスで追加したいテーブルを(複数)選択します。

![ダイアグラム テーブルの追加](https://docs.tabulareditor.com/images/diagram-add-tables.png)

ダイアグラムにテーブルを追加するには、上記の手法を再度使用するか、ダイアグラム内の既存のテーブルを右クリックして、次のオプションのいずれかを選択します。
- **Add tables that filter this table**: 現在選択されているテーブルを直接または他のテーブルを介して間接的にフィルタリングする可能性のあるすべてのテーブルをダイアグラムに追加します。ファクトテーブルから始める場合に便利です。
- **すべての関連テーブルを追加**。現在選択されているテーブルに直接関連するすべてのテーブルをダイアグラムに追加します。ディメンション・テーブルから開始する場合に便利です。

![関連テーブルの追加](https://docs.tabulareditor.com/images/add-related-tables.png)

または、**Diagram > Auto-arrange**機能を使用して、Tabular Editor 3がテーブルを自動的にレイアウトします。

## ダイアグラムを使ったリレーションシップの変更

2つのテーブルの間に新しいリレーションシップを追加するには、リレーションシップのファクトテーブル（多面体）の列を探し、その列をディメンションテーブル（片面体）の対応する列にドラッグします。リレーションシップの設定を確認し、**OK**を押します。

![リレーションシップの作成](https://docs.tabulareditor.com/images/create-relationship.png)

既存のリレーションシップを編集するには、リレーションシップ上で右クリックして**Edit relationship**を選択します。右クリックメニューには、以下のスクリーンショットに示すように、リレーションシップを反転または削除するためのショートカットも含まれています。

![リレーションシップダイアグラムの編集](https://docs.tabulareditor.com/images/edit-relationship-diagram.png)

> [!!! NOTE]
> 図を使用せずに、TOMエクスプローラーでリレーションシップを作成することもできます。リレーションシップの始点となる列(多面体側/ファクトテーブル側)を探し、右クリックして**作成>リレーションシップの始点**を選択します。画面に表示される「リレーションシップの作成」ダイアログで目的の列を指定します。

## ダイアグラムの保存

図を保存するには、単に **ファイル > 保存** (CTRL+S) を使用します。図の変更が保存されていない状態でドキュメントやアプリケーションを閉じると、Tabular Editor 3は図の保存を促します。

> [! TIP].
> 同一のダイアグラム・ファイルを異なるデータ・モデル用に読み込むことができます。ダイアグラムでは、テーブルの名前を参照します。図のロード時にモデルに存在しないテーブルは、単に図から削除されます。

> [!!!注意]
> リレーションシップを追加・変更するたびに、データモデルに "calculate "リフレッシュを実行しなければ、モデルへのクエリでリレーションシップを使用できません。

## 次のステップ

- プレビュークエリのリフレッシュ
- テスト用データの作成とテスト