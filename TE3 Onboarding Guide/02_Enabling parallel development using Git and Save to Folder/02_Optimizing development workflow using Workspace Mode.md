---
uid: optimizing-workflow-workspace-mode
title: Optimizing development workflow using Workspace Mode
author: Daniel Otykier
updated: 2021-10-05
applies_to:
  editions:
    - edition: Desktop
      none: x
    - edition: Business
    - edition: Enterprise
---


# ワークスペース・モードによる開発ワークフローの最適化

Tabular Editor 3では、ツール内で新しいモデルを作成するときや、既存モデルの`Model.bim`や`Database.json`ファイルを読み込むときに、ワークスペースモードの概念を導入しています。

ワークスペース・モードを使用すると、保存（Ctrl+S）を押すたびに、Tabular Editorはモデルのメタデータの変更をワークスペース・データベースに同期させ、メタデータの変更をディスク上のファイルに保存します。

開発中のコンフリクトを避けるために、モデル開発者はそれぞれ独自のワークスペース・データベースを使用するのが理想的です。

>注意
>互換性レベル1200、1400、1500のモデルでは、SQL Server Developer Edition 2019に含まれているような、Analysis Servicesのローカル インスタンスを使用してワークスペース データベースをホストすることをオススメします。

## 新しいモデルの作成

Tabular Editorで新しいモデルを作成すると、デフォルトで「ワークスペースデータベースを使用」オプションがチェックされます。

![new_model](https://docs.tabulareditor.com/images/new-model.png)

これをチェックしたまま、「OK」を押した後、Analysis Servicesのインスタンスに接続するよう促されます。これは、ワークスペース データベースがデプロイされるAnalysis Servicesのインスタンスです。

>重要事項
>ワークスペース データベースをPower BI Service XMLAエンドポイントに配置する場合は、上のダイアログで [互換性レベル1560（Power BIデータセット）] を選択してください。

Analysis Servicesサーバーの詳細と（オプションの）認証情報を入力すると、サーバーに現在再配置されているすべてのデータベースのリスト（Power BIワークスペースの場合は、ワークスペースに配置されているデータセットのリスト）が表示されます。

![..](https://docs.tabulareditor.com/images/select-workspace-database.png)

Tabular Editorは、Windowsのユーザー名と現在の日時に基づいて、ワークスペースのデータベースに新しい一意の名前を提案しますが、これをより意味のある名前に自由に変更することができます。

OKをクリックすると、新しいモデルが作成され、ワークスペース・データベースが展開されて接続されます。この時点で、保存（Ctrl+S）を押して、モデルを``Model.bim``ファイルとして保存します。モデルのメタデータをGitなどのバージョン管理システムに保存する場合は、[ファイル] > [フォルダに保存...] メニュー項目を選択することもできます。

![..](https://docs.tabulareditor.com/images/save-new-to-folder.png)

この時点で、データソースを定義し、モデルに新しいテーブルを追加する準備が整いました。続いて「保存」（Ctrl+S）を押すたびに、ワークスペース・データベースが変更内容で更新され、以前に選択したファイル/フォルダも更新されます。

このモデルに関連付けられたワークスペース・データベースに関する情報は、モデル・メタデータ・ファイルの隣にあるタブラー・モデル・ユーザー・オプション (.tmuo) ファイルに保存されます。詳細については、「ユーザー・オプション (.tmuo) ファイル」を参照してください。

`Model.bim` または `Database.json` ファイルを開く
既存の `Model.bim` または `Database.json` ファイルを開くと、Tabular Editor 3はそのファイルのワークスペース・データベースを開始するかどうかを尋ねます。

![..](https://docs.tabulareditor.com/images/connect-to-wsdb.png)

選択肢は次のとおりです。

はい：Analysis Servicesのインスタンスに接続し、既存のワークスペース データベースを選択するか、新しいものを作成します。次回、同じ`Model.bim`または`Database.json`ファイルをロードすると、Tabular Editorは同じワークスペースデータベースに接続します。

いいえ：Tabular Editorは、Analysis Servicesへの接続を行わずに、ファイル内のメタデータをオフラインで読み込みます。

もう一度聞かないでください。:上記と同じですが、同じ`Model.bim`または`Database.json`ファイルを次回開いたときに、Tabular Editorはワークスペースデータベースへの接続を要求しなくなります。

キャンセルする:ファイルはまったく読み込まれません。
特定のモデルでワークスペース・データベースに接続するかどうか、どのワークスペース・サーバーとデータベースを使用するかについての情報は、Tabular Model User Options (.tmuo)ファイルに保存されます。

## ワークスペース・モードの利点

ワークスペース・モードの主な利点は、Tabular EditorをAnalysis Servicesのインスタンスに接続したままにできることです。つまり、Tabular Editor 3の新しい接続機能が有効になります。しかし、これらの機能を使用しない場合でも、変更内容をテストする目的でAnalysis Servicesのインスタンスを同期させるのは非常に簡単です。やることは、Save（CTRL+S）を押すだけです。これは、Tabular EditorがAnalysis Servicesのインスタンスからモデルのメタデータを直接開く場合と似ていますが、ワークスペース・モードでは、モデルのメタデータが同時にディスクへ保存されます。

>注意
>リフレッシュ操作が進行中の場合、Tabular EditorはAnalysis Servicesインスタンスを同期することができません（リフレッシュ操作は他の書き込み操作をブロックします）。ただし、このような操作が行われているときに保存（CTRL+S）を押すと、ワークスペース モードを使用していても、モデルのメタデータがディスクに保存されます。

次のステップ

- Azure DevOpsとTabular EditorによるPower BIのCI/CD
- Azure DevOpsとTabular Editorを使ったAnalysis ServicesのCI/CD
- Tabular Editor 3による生産性の向上
