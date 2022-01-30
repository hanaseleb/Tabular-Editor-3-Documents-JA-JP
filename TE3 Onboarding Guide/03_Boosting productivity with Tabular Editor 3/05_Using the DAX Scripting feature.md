---
uid: dax-script-introduction
title: Using the DAX Scripting feature
author: Daniel Otykier
updated: 2021-10-08
applies_to:
  editions:
    - edition: Desktop
    - edition: Business
    - edition: Enterprise
---

# DAXスクリプト機能の使用

[前回の記事](xref:creating-and-testing-dax)では、メジャーや計算列などの計算オブジェクトをモデルに追加・編集する方法を学びました。

モデルが複雑になってくると、ビジネス・ロジックをオーサリングおよびメンテナンスする際に、TOMエクスプローラをナビゲートしたり、メジャー間を行き来したりするのが面倒になってくることがあります。メジャー間の依存関係が長く続くことも珍しくありません。そのため、ビジネス・ロジックを構成するすべてのDAXコードを1つのドキュメントにまとめることが有効な場合があります。

これこそが、Tabular Editor 3で導入された新しい**DAXスクリプト**機能の目的です。

この機能を使用するには、TOMエクスプローラで単一のドキュメントを生成したいメジャーを探します。メジャーを複数選択し、右クリックして**Script DAX**を選択します。選択したすべてのメジャーの定義を含む新しいドキュメントが作成されます。また、テーブルまたはモデル・オブジェクトをそれぞれ選択することで、テーブル内のすべてのメジャーまたはモデル内のすべてのメジャーのDAXスクリプトを生成することができます。

![DAXスクリプト](https://docs.tabulareditor.com/images/dax-script.png)

DAX スクリプトを使用したオブジェクトの編集は、**Expression Editor** を使用した編集とは若干異なります。DAXスクリプトでは、別のオブジェクトに移動したときに変更がすぐに適用されます。しかし、DAXスクリプトでは、**Script > Apply**（F5）オプションを使用して明示的に実行するまで、変更は適用されません。Analysis Servicesのインスタンスに接続している場合は、**Script > Apply & Sync**（SHIFT+F5）オプションを使用して、変更の適用と更新されたモデルのメタデータのAnalysis Servicesへの保存を同時に行うことができます。

## DAXスクリプトファイルの操作

DAXスクリプトはテキストファイルとして保存でき、ファイル拡張子は`.te3daxs`となります。DAXスクリプトをファイルとして保存するには、**File > Save**（Ctrl+S）オプションを使用します。DAXスクリプトをテキストファイルから開くには、**File > Open > File...** (Ctrl+O)オプションを使用します。

> [!］
> DAXスクリプトはモデルに依存しませんが、DAX式はモデルで定義されたメジャー、列、テーブルを指すことがあるため、どのDAXスクリプトもどのモデルにも適用できるという保証はありません。DAXスクリプトは、主に特定のデータモデルのコンテキストで、1つのドキュメント内の複数のDAXオブジェクトを操作する際に役立ちます。

## DAXスクリプトエディター

DAXスクリプトエディターは、Tabular Editor 3の他の部分で使用されているDAXエディターのすべての機能を備えています。具体的には、オートコンプリート、オートフォーマット、calltipsなどです。

さらに、大きなDAXスクリプトを簡単に管理するために、DAXスクリプトビューの上部に2つのドロップダウンが表示されます。左側のドロップダウンでは、スクリプト内で定義されたオブジェクト間を移動することができ、右側のドロップダウンでは、現在のオブジェクトのプロパティ間を移動できます。

![DAXスクリプトナビゲーション](https://docs.tabulareditor.com/images/dax-script-navigation.png)

## メジャーの定義

スクリプトで参照されていて、まだ定義されていないメジャーの定義を含めるには、メジャーの参照を右クリックして、「メジャーの定義」または「依存関係のあるメジャーの定義」オプションを選択します。

![依存性のあるメジャーの定義](https://docs.tabulareditor.com/images/define-measure-with-deps.png)

## ショートカット

スクリプトをモデルに適用するには、以下のショートカットを使用します。

- **F5**: スクリプト全体をローカルモデルのメタデータに適用する
- **Shift+F5**: Shift+F5**：スクリプト全体をローカルモデルのメタデータに適用し、モデルのメタデータをソースに保存する。
- **F8**: スクリプトの現在選択されている部分をローカルモデルのメタデータに適用します。
- **Shift+F8**: スクリプトの現在選択されている部分をローカルのモデルメタデータに適用し、そのモデルメタデータをソースに戻す

## DAXオブジェクトのサポート

Tabular Editor 3は、DAXスクリプトを使用した以下のタイプのオブジェクトの編集をサポートしています。

- メジャー(KPIを含む)
- 計算された列
- 計算されたテーブル
- 計算グループ (計算項目を含む)

## DAXスクリプトの構文

DAXスクリプトの構文は以下の通りです。

```dax
<DAX script>:
MEASURE 'Table name'[Measure name] = <DAX expression>
    [<Measure properties>]

COLUMN 'Table name'[Column name] = <DAX expression>
    [<Column properties>]

TABLE 'Table name' = <DAX expression>
    [<Table properties>]

CALCULATIONGROUP 'Table name'[Column name]
    [<Calculation Group properties>]
    CALCULATIONITEM "Item 1" = <DAX expression>
        [<Calculation Item properties>]
    CALCULATIONITEM "Item 2" = <DAX expression>
        [<Calculation Item properties>]
    ...

<Measure properties>:
    DetailRows = <DAX expression>
    DisplayFolder = "string"
    FormatString = "string"
    Description = "string"
    Visible = TRUE/FALSE
    KpiStatusExpression = <DAX expression>
    KpiStatusDescription = "string"
    KpiStatusGraphic = "string"
    KpiTrendExpression = <DAX expression>
    KpiTrendDescription = "string"
    KpiTrendGraphic = "string"
    KpiTargetExpression = <DAX expression>
    KpiTargetDescription = "string"
    KpiTargetFormatString = "string"

<Column properties>:
    DisplayFolder = "string"
    FormatString = "string"
    Description = "string"
    Visible = TRUE / FALSE
    Datatype = BOOLEAN / DOUBLE / INTEGER / DATETIME / CURRENCY / STRING

<Table properties>:
    Description = "string"
    Visible = TRUE / FALSE
    DetailRows = <DAX expression>

<Calculation Group properties>:
    Description = "string"
    Visible = TRUE / FALSE
    Precedence = <integer value>

<Calculation Item properties>
    Description = "string"
    Ordinal = <integer value>
    FormatString = <DAX expression> 
```

## 例1: メジャー

例として、以下のスクリプトでは、`'Internet Sales'テーブルに`[Internet Total Sales]`メジャーを定義しています。このスクリプトには、メジャーの DAX 式に加えて、メジャーの説明と形式文字列が含まれています。

```dax
----------------------------------
-- Measure: [Internet Total Sales]
----------------------------------
MEASURE 'Internet Sales'[Internet Total Sales] = SUM('Internet Sales'[Sales Amount])
    Description = "Returns the sum of all Internet Sales"
    FormatString = "\$#,0.00;(\$#,0.00);\$#,0.00"
```

## 例2: ステータスとターゲット KPI での計測

以下のDAXスクリプトでは、ステータス式とターゲット式を持つKPIを含む `[Internet Current Quarter Sales Performance]` メジャーを定義しています。ステータスKPIは、「Shapes」グラフィックを使用しています。

```dax
--------------------------------------------------------
-- Measure: [Internet Current Quarter Sales Performance]
--------------------------------------------------------
MEASURE 'Internet Sales'[Internet Current Quarter Sales Performance] =
    IFERROR(
        [Internet Current Quarter Sales] / [Internet Previous Quarter Sales Proportion to QTD],
        BLANK()
    )
    , KpiStatusExpression =
        VAR x = [Internet Current Quarter Sales Performance]
        RETURN
            IF(
                ISBLANK( x ),
                BLANK(),
                IF(x < 1, -1, IF(x < 1.07, 0, 1))
            )
    , KpiStatusGraphic = "Shapes"
    , KpiTargetExpression = 1.1
```

## 例3: Calculation Group

以下のDAXスクリプトは、`[Period]`列を持つ`'Time Intelligence'`計算グループを定義しています。この計算グループには、さまざまな時間計算を行う6つの計算項目が含まれています。YoY %"`アイテムが異なるフォーマット文字列を適用していることに注目してください。

```dax
-----------------------------------------
-- Calculation Group: 'Time Intelligence'
-----------------------------------------
CALCULATIONGROUP 'Time Intelligence'[Period]
    Description = "Use this table to perform time calculations"

    CALCULATIONITEM "Current" = SELECTEDMEASURE()
        Ordinal = 0

    CALCULATIONITEM "MTD" = TOTALMTD(SELECTEDMEASURE(), 'Calendar'[Date])
        Ordinal = 1

    CALCULATIONITEM "YTD" = TOTALYTD(SELECTEDMEASURE(), 'Calendar'[Date])
        Ordinal = 2

    CALCULATIONITEM "PY" = CALCULATE(SELECTEDMEASURE(), SAMEPERIODLASTYEAR('Calendar'[Date]))
        Ordinal = 3

    CALCULATIONITEM "YoY" = 
        SELECTEDMEASURE()
         - CALCULATE(SELECTEDMEASURE(), SAMEPERIODLASTYEAR('Calendar'[Date]))
        Ordinal = 4

    CALCULATIONITEM "YoY %" = 
        VAR lastYear = 
            CALCULATE(SELECTEDMEASURE(), SAMEPERIODLASTYEAR('Calendar'[Date]))
        RETURN
            DIVIDE(
                SELECTEDMEASURE() - lastYear,
                lastYear
            )
        FormatString = "Percent"
        Ordinal = 5
```

## 次のステップ

- ＠bpa
- cs-scripts-and-macros
- パーソナリゼーション