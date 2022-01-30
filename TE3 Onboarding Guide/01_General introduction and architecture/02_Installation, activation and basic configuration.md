---
uid: installation-activation-basic
title: Installation, activation and basic configuration
author: Daniel Otykier
updated: 2021-09-30
---

# インストール

Tabular Editor 3をインストールするには、弊社の[downloads page](xref:downloads)から最新バージョンをダウンロードしてください。

ほとんどのシナリオに適したMSI 64-bitインストーラーをダウンロードすることをお勧めします。ダウンロードが完了したら、MSIファイルをダブルクリックして、インストールページに進みます。

![インストール](
https://docs.tabulareditor.com/images/install.png)

## インストールの有効化

新しいマシンでTabular Editor 3を初めて起動すると、製品をアクティベートするように促されます。

![製品のアクティベーション](https://docs.tabulareditor.com/images/product-activation.png)

### 既存のライセンスキーを使ったアクティベーション

Tabular Editor 3のライセンスを購入すると、ライセンスキーとなる25文字の文字列がメールで送られてきます。ライセンスキーを入力し、"Next >"を押して製品をアクティベートしてください。

![Enter License Key](https://docs.tabulareditor.com/images/enter-license-key.png)

> [!!! NOTE].
> マルチユーザーライセンスタイプの場合は、ライセンスキーに加えて電子メールアドレスを入力する必要があります。入力したライセンスキーがマルチユーザーライセンスである場合、Tabular Editor 3 はその入力を促します。

### ライセンスキーの変更

Tabular Editor 3を起動すると、ヘルプメニューから「About Tabular Editor」を選択してライセンスキーを変更することができます。

![About Te3](https://docs.tabulareditor.com/images/about-te3.png)

ダイアログで「Change license key」を選択します。このオプションは、Tabular Editorにモデルがロードされていない場合にのみ利用可能であることに注意してください。モデルをロードしている場合は、「ファイル」>「モデルを閉じる」でモデルを閉じることができます。

ライセンスキーの管理についての詳細は、[レジストリの詳細](xref:getting-started#registry-details)を参照してください。

## 基本的な設定

Tabular Editor 3を起動したら、数分かけて[基本インターフェース](xref:user-interface)に慣れることをお勧めします。さらに、Tabular Editor 3にはさまざまな設定オプションが用意されています。ほとんどの開発シナリオでは、デフォルトの設定で十分ですが、常に確認しておくべき重要な設定オプションがいくつかあります。

### 起動時にアップデートを確認する

デフォルトでは、Tabular Editor 3 を起動するたびに、より新しいバージョンが利用可能かどうか をオンラインでチェックします。このアップデートチェックをどのように行うかは、**Tools > Preferences > Updates and Feedback**で制御することができます。

> [!注意].
> 常に最新バージョンのTabular Editor 3を使用することをお勧めします。当社のサポートチームは、お客様が常に最新バージョンを使用していることを前提に、バグレポートを提出します。

### 遠隔測定収集からの脱退

Tabular Editor 3は匿名の使用状況データと遠隔測定を収集し、製品の改善に役立てています。Tabular Editor 3を起動し、**Tools > Preferences > Updates and Feedback**に移動することで、いつでもオプトアウトできます。Help improve Tabular Editor by collecting anonymous usage data**チェックボックスをオフにして、オプトアウトできます。

![Collect Telemetry](https://docs.tabulareditor.com/images/collect-telemetry.png)

### プロキシの設定

インターネットへの接続が制限されているネットワークにいる場合、**ツール > 環境設定 > プロキシ設定**でプロキシサーバーのアドレス、ユーザー名、パスワードを指定することができます。この設定は、Tabular Editor 3 でウェブリクエストの送信に依存する機能を使用する前に必要です。具体的には以下のようなものがあります。

- アップデートチェック
- 製品のアクティベーション
- DAXフォーマット
- ベストプラクティスルールの外部URLからのダウンロード

### その他の設定

上記の設定に加えて、Tabular Editor 3には、アプリケーションのさまざまな動作を制御するための多くの設定が含まれており、ツールをニーズに合わせて細かく調整できます。これらの設定の詳細については、@preferencesを参照してください。

## 次のステップ

- マイグレーション元バージョン
- デスクトップからの移行
- TE2からの移行