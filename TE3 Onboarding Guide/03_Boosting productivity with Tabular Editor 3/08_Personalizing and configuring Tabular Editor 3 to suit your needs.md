---
uid: personalizing-te3
title: Personalizing and configuring Tabular Editor 3 to suit your needs
author: Daniel Otykier
updated: 2021-09-28
---

# ニーズに合わせてTabular Editor 3をカスタマイズ・設定する

Tabular Editor 3には、幅広い設定オプションが用意されており、特定のニーズや好みのワークフローに合わせてツールを微調整することができます。この記事では、個人のモデル開発者が最もよく調整する設定をご紹介します。

この記事で取り上げる設定のほとんどは、「ツール」→「環境設定」メニューオプションからアクセスできます。本記事では、各設定項目を以下のようなスタイルで記載していますので、参考にしてください。

***設定の名前* (デフォルト値)**<br/>設定の説明。

## 一般的な機能

[環境設定]ダイアログの最初のページは、[Tabular Editor 3]の[機能]ページです（下のスクリーンショットを参照）。以下に、このページにある機能と、それらの機能がどのように使われているかを簡単に説明します。

![Pref General Features](https://docs.tabulareditor.com/images/pref-general-features.png)

## Power BI

これらの設定は、主にTabular Editor 3を[External Tool for Power BI Desktop](https://docs.microsoft.com/en-us/power-bi/transform-model/desktop-external-tools)として使用している開発者にとって有用です。

##### *サポートされていないモデリング操作を許可する* (disabled)

Power BI Desktop用の外部ツールにはいくつかの[制限]（xref:desktop-limitations）があります。デフォルトでは、Tabular Editor 3は、ユーザーがデータモデルにサポートされていない変更を加えることを防ぎます。サポートされていないにもかかわらず、うまく機能する高度なモデリング機能があるかもしれません（前のリンク参照）。すべてのTabular Object Modelオブジェクトとプロパティのロックを解除するには、この設定を有効にします。

##### *Hide auto date/time warnings* (disabled)

Power BI Desktopの「自動日付/時刻」設定を有効にすると、多くの計算テーブルが自動的に作成されます。残念ながら、これらのテーブルには、Tabular Editor 3の内蔵DAXアナライザーによる警告メッセージを引き起こすDAXコードが含まれています。これらの警告を非表示にするには、この設定を有効にします。

##### *DAX の最初の行で改行する*（disabled）

Power BI Desktopでは、数式バーにDAXコードが表示されるため、DAX式の最初の行に改行を挿入するのが一般的です。Tabular EditorとPower BI Desktopを頻繁に行き来する場合は、このオプションを有効にして、Tabular Editor 3でDAX式が編集されるたびに、自動的に改行が挿入されるようにすることを検討してください。

## メタデータの同期

これらの設定は、Analysis Servicesのインスタンス上のデータベースからモデルのメタデータが読み込まれたときのTabular Editor 3の動作を制御します。他のユーザーがデータベースに変更を加えた場合や、Tabular Editor 3を外部ツールとして使用しているときにPower BI Desktopでモデルに変更を加えた場合など、アプリケーションの外部からデータベースに適用されたメタデータの変更をTabular Editor 3でどのように処理するかを指定します。

##### *Warn when local metadata is out-of-sync with deployed model* (有効) :ローカルのメタデータが配備されたモデルと同期していない場合に警告する



これをチェックすると、モデルのメタデータがTabular Editorのインスタンスに読み込まれてから、他のユーザーやプロセスがデータベースに変更を加えた状態で変更を保存しようとしたときに、Tabular Editorが警告メッセージを表示します。

##### *Track external model changes* (enabled):外部モデル変更の追跡

このオプションは、Analysis Servicesのローカルインスタンス（Tabular Editorと同じマシンで実行されているmsmdsrv.exeプロセス）にのみ関係します。チェックすると、Tabular EditorはAnalysis Servicesのトレースを開始し、外部からの変更があった場合に通知します。

##### *Refresh local Tabular Object Model metadata automatically* (enabled) :ローカルのTabular Object Modelのメタデータを自動的に更新する

上記のようなトレースメカニズムを有効にすると、このオプションにより、外部からの変更が検出されたときにTabular Editorがモデルのメタデータを自動的に更新できるようになります。Power BI Desktopで行った変更がTabular Editorに自動的に同期されるようになるため、Power BI DesktopとTabular Editor 3を頻繁に行き来する場合に便利です。

##### *Cleanup orphaned Tabular Editor traces* :使われなくなったTabular Editorの痕跡を消す

通常、Tabular Editor 3は、上記の設定により開始されたASトレースを自動的に停止して削除するはずです。しかし、アプリケーションが早期にシャットダウンされた場合、トレースは決して停止しないかもしれません。このボタンをクリックすると、Analysis Services の現在のインスタンス上で Tabular Editor の任意のインスタンスによって開始されたすべての AS トレースが削除されます。

> [!!! 注意]
> クリーンアップボタンは、Tabular EditorがAnalysis Servicesのインスタンスに接続されている場合にのみ使用できます。

## TOM Explorerの設定

以下の設定は、TOMエクスプローラーの様々な側面を制御します。これらの設定は、**Tabular Editor > TOM Explorer**で見つけることができます。

![TOMエクスプローラーの設定](https://docs.tabulareditor.com/images/tom-explorer-settings.png)

##### *Show full branch* (disabled)

TOM Explorerをフィルタリングする場合、デフォルトではTabular Editor 3はフィルター文字列にマッチする階層のすべてのアイテムを親も含めて表示します。すべての子アイテムを表示したい場合は、このオプションを有効にしてください（フィルター文字列にマッチしない場合もあります）。

##### *Always show delete warnings* (disabled)

Tabular Editor 3がすべてのオブジェクト削除を確認するプロンプトを表示したい場合は、この設定を有効にします。そうしないと、Tabular Editor 3は、マルチオブジェクトの削除、または他のオブジェクトによって参照されているオブジェクトの削除についてのみ確認を促します。

> [!Note] (注意)
> Tabular Editor 3のすべての削除操作は、CTRL+Zキーを押すことで取り消すことができます。

# DAXエディタの一般設定

Tabular Editor 3のDAXエディターは高度な設定が可能であり、多くの設定項目に圧倒されがちです。このセクションでは、もっとも一般的で重要な設定を紹介します。一般的な設定は、**テキストエディター > DAXエディター > 一般**にあります。

![Dax Editor General](https://docs.tabulareditor.com/images/dax-editor-general.png)

## 一般

行番号*、コードフォールディング*、Visible Whitespace*、インデントガイド*の設定は、エディタのさまざまな視覚的機能を切り替えるために使用できます。以下のスクリーンショットでは、4つのオプションがすべて有効になっています。

![Visible Whitespace](https://docs.tabulareditor.com/images/visible-whitespace.png)

##### *Use tabs* (無効)

これがチェックされていると、TABボタンが押されるたびに、タブ文字（``）が挿入されます。それ以外の場合は、*Indent width* の設定に応じた数のスペースが挿入されます。

##### *Comment style* (slashes)

DAXでは、スラッシュ(`//`)またはハイフン(`--`)を使ったラインコメントをサポートしています。この設定は、Tabular Editor 3がDAXスクリプト機能を使用する場合などにDAXコードを生成する際に、どちらのスタイルのコメントを使用するかを決定します。

## DAX設定

これらの設定は、DAXコードアナライザーの特定の動作を決定します。Locale*の設定は、単に好みの問題です。その他の設定は、Model.bimファイルが直接ロードされた場合など、Tabular Editor 3が使用するAnalysis Servicesのバージョンを判断できない場合にのみ関係します。この場合、Tabular Editorはモデルで指定された互換性レベルに基づいて、モデルがどのバージョンにデプロイされるかを推測しようとしますが、デプロイメント・ターゲットの実際のバージョンによっては、さまざまなDAX言語の違いがありTabular Editorでは判断できません。Tabular Editorが誤ったセマンティック/シンタックスエラーを報告する場合、以下の設定を微調整する必要があるかもしれません。

## オートフォーマット

テキストエディター > DAXエディター > 自動フォーマット**ページでは、DAXコードのフォーマットを制御するためのさまざまな設定を見つけることができます。

![自動フォーマットの設定](https://docs.tabulareditor.com/images/auto-formatting-settings.png)

##### *Auto format code as you type* (enabled)

このオプションは、特定のキーストロークが発生するたびに、特定のフォーマットルールを自動的に適用します。例えば、括弧が閉じられると、この機能により、その括弧内のすべてがこのページの他の設定に従ってフォーマットされます。

##### *Auto-format function calls* (enabled)

このオプションは、括弧が閉じられたときに、関数呼び出しの自動フォーマット（つまり、引数と括弧の間のスペース）を行うかどうかを制御します。

##### *Auto-indent* (enabled)

このオプションは、関数呼び出しの中で改行が挿入されると、関数の引数を自動的にインデントします。

##### *Auto-brace* (enabled)

このオプションは、開始波括弧や引用符が入力されると、自動的に終了波括弧や引用符を挿入します。

##### *Wrap selection* (enabled)

このオプションを有効にすると、開始波括弧が入力されたときに、現在の選択範囲を自動的に終了波括弧で囲みます。

## フォーマットルール

これらの設定は、DAXコードのホワイトスペースをどのようにフォーマットするかを制御します。これは、自動フォーマット時だけでなく、（**Format DAX**メニューオプションを使用して）コードを手動でフォーマットする場合にも適用されます。

##### *Space after Functions* (disabled)

###### [Enabled](#tab/tab1)

```DAX
SUM ( 'Sales'[Amount] )
```

###### [Disabled](#tab/tab2)

```DAX
SUM( 'Sales'[Amount] )
```

***

##### *関数の後に改行* (無効)

関数の呼び出しを複数行に分けて行う必要がある場合にのみ適用されます。

###### [Enabled](#tab/tab3)

```DAX
SUM
(
    'Sales'[Amount] (売上高)
)
```

###### [Disabled](#tab/tab4)

```DAX
SUM(
    'Sales'[Amount](売上高)
)
```

***

##### *演算子の前の改行* (有効)

二項演算を複数行に分けて行う必要がある場合にのみ適用されます。

###### [Enabled](#tab/tab5)

```DAX
[Internet Total Sales]
    + [Reseller Total Sales]
```

###### [Disabled](#tab/tab6)

```DAX
[Internet Total Sales] +
    [Reseller Total Sales]
```

***

##### *Pad括弧* (有効)

###### [Enabled](#tab/tab7)

```DAX
SUM( Sales[Amount] )
```

###### [Disabled](#tab/tab8)

```DAX
SUM(Sales[Amount])
```

***

##### *長いフォーマットの行数制限* (120)

DAXの書式(long lines)**オプションを使用した場合に、式が複数行に分断される前の1つの行に保持する最大文字数です。

##### *短いフォーマットの行数制限* (60)

Format DAX (short lines)**オプションを使用した場合に、複数行にまたがって式が改行されるまでに一行に保持する最大文字数です。

> [!注意].
> 上記のほとんどの設定は、(デフォルトの)組み込みDAXフォーマッタを使用する場合にのみ有効です。

## ケージングと引用符

DAXコードのホワイトスペースのフォーマットに加えて、Tabular Editor 3はオブジェクト参照や関数/キーワードのケーシングを修正することができます。

##### *Fix measure/column qualifiers* (enabled)

これをチェックすると、テーブルプレフィックスがメジャー参照から自動的に削除され、カラム参照に自動的に挿入されます。

##### *Preferred keyword casing* (デフォルト = UPPER)

この設定では、`ORDER BY`、`VAR`、`EVALUATE`などのキーワードに使用するケーシングを変更することができます。これはオートコンプリート機能でキーワードを挿入した場合にも適用されます。

##### *Preferred function casing* (default = UPPER)

この設定では、`CALCULATE(...)`や`SUM(...)`などの関数に使われるケーシングを変更ができます。これは、オートコンプリート機能で関数を挿入した場合にも適用されます。

##### *Fix keyword/function casing* (enabled)

これをチェックすると、コードが自動または手動でフォーマットされたときに、キーワードや関数のケーシングが自動的に修正されます。

##### *Fix object reference casing* (enabled)

DAXは、大文字と小文字を区別しない言語です。この機能を有効にすると、テーブル、列、メジャーへの参照が自動的に修正され、参照されるオブジェクトの物理名とケーシングが一致するようになります。この修正は、コードが自動フォーマットまたは手動フォーマットされるたびに行われます。

##### *Always quote tables* (disabled)

特定のテーブル名を参照する場合、DAXでは一重引用符を囲む必要はありません。ただし、テーブル名にかかわらず、テーブルの参照を常に引用符で囲むことを希望する場合は、このオプションをチェックができます。

##### *Always prefix extension columns* (disabled)

拡張列は、テーブル名を付けずに定義することができます。これをチェックすると、テーブル名が空白であっても、DAXエディタは常にテーブルのプレフィックスを拡張カラムに追加します。この場合、カラムの参照は `''[拡張カラム]` のようになります。

## 次のステップ

- ブースティング-生産性向上-TE3