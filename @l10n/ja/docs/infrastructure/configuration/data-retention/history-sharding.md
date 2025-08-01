---
html: history-sharding.html
parent: data-retention.html
seo:
    description: 履歴シャーディングは、履歴レジャーデータを保持する任務をrippledサーバ間で分担するようにします。
labels:
  - データ保持
  - コアサーバ
---
# 履歴シャーディング

{% badge href="https://github.com/XRPLF/rippled/releases/tag/0.90.0" %}導入: rippled 0.90.0{% /badge %}

稼働中のサーバは、ネットワーク実行時に検知または取得したレジャーに関するデータを格納したデータベースを作成します。各`rippled`サーバは、そのレジャーのデータをレジャーストアーに保存しますが、保存されたレジャー数が設定された容量制限を超えると、オンライン削除ロジックによりこれらのデータベースがローテーションされます。

履歴シャーディングは、XRP Ledgerのトランザクション履歴をシャードと呼ばれるセグメントに分割し、XRP Ledgerネットワークのサーバ全体に分散します。シャードは、一連のレジャーです。`rippled`サーバは、レジャーストアーとシャードストアーの両方にレジャーを同じ方法で保存します。

履歴シャーディング機能を使用すると、個々の`rippled`サーバが履歴データの保存する役割を担い、すべての履歴（数テラバイト）を保存する必要がなくなります。シャードストアーはレジャーストアーに代わるものではありませんが、XRP Ledgerネットワーク上の分散レジャー履歴への信頼性の高いパスを実現します。

[![XRP Ledgerネットワーク: レジャーストアーとシャードストアーの図](/docs/img/xrp-ledger-network-ledger-store-and-shard-store.ja.png)](/docs/img/xrp-ledger-network-ledger-store-and-shard-store.ja.png)

<!-- Diagram source: https://docs.google.com/presentation/d/1mg2jZQwgfLCIhOU8Mr5aOiYpIgbIgk3ymBoDb2hh7_s/edit#slide=id.g417450e8da_0_316 -->

## 履歴シャードの取得と共有

`rippled` サーバは履歴シャードを取得して保存します（この動作には設定が必要です）。このようなサーバでは、ネットワークとの同期を実行し、設定された数の最新レジャーへのレジャー履歴の埋め戻しが完了した後で、シャードの取得が開始されます。ネットワークアクティビティがあまり発生しないこの期間に、`shard_db`を維持するように設定されている`rippled`サーバ が、シャードストアーに追加するシャードをランダムに選択します。ネットワークレジャー履歴が均等に分散される確率を高めるため、取得対象のシャードはランダムに選択され、現行シャードが特に優先されることはありません。

シャードが選択されたら、レジャー取得プロセスが開始されます。最初にシャードの最後のレジャーのシーケンスが取得され、最初のシャードに向けて逆方向に処理が進められます。取得プロセスでは最初に、サーバがローカルでデータを確認します。取得できないデータについては、サーバはピア`rippled`サーバにデータをリクエストします。リクエストされた期間のデータを供給できるサーバは、履歴でレスポンスします。リクエスト側サーバはこれらのレスポンスを結合し、シャードを作成します。シャードに特定範囲のレジャーがすべて含まれた状態になれば、シャードが完成します。

`rippled`サーバが1つのシャードを完全に取得する前に容量不足になった場合、空き容量ができて処理を続行できるようになるまで取得プロセスを停止します。この後、古いシャードは完成された最新のシャードに置き換えられます。ディスク容量が十分にある場合は、`rippled`サーバはシャードに割り当てられている最大ディスク容量（`max_size_gb`）に達するまで、ランダムに選択された追加のシャードを取得し、シャードストアーに追加します。

## XRP Ledgerネットワークデータの整合性

すべてのレジャーの履歴は、特定範囲の履歴レジャーを維持することに同意したサーバ間で共有されます。これにより、各サーバは維持することに同意したデータがすべてあることを確認し、プルーフツリーまたはレジャーデルタを作成できるようになります。履歴シャーディングが設定されている`rippled`サーバは、保存するシャードをランダムに選択するため、すべての閉鎖済みレジャーの履歴全体が正規分布曲線で保存され、XRP Ledgerネットワークで履歴が均一に維持される確率が高くなります。

## 関連項目

- [履歴シャーディングの設定](configure-history-sharding.md)
- [download_shardメソッド][]
- [crawl_shardsメソッド][]

{% raw-partial file="/@l10n/ja/docs/_snippets/common-links.md" /%}
