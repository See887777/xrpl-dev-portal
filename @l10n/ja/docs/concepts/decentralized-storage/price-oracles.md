# 価格オラクル

_([PriceOracle Amendment][])_

ブロックチェーンは、本質的にネットワーク外で何が起こっているのかを把握することはできませんが、分散型金融における多くのユースケースでは、この情報が必要となります。

価格オラクルは、この問題を解決します。オラクルとは、市場価格、為替レート、金利などの実世界の情報を収集し、それをブロックチェーンに伝達するサービスまたはテクノロジーです。ブロックチェーンと同様に、ほとんどのオラクルも分散型であり、複数のノードを通じてデータの検証を行います。

{% admonition type="info" name="注記" %}

一般的に、オラクルは金融情報に限らず、あらゆる種類の情報を提供することができます。例えば、どのスポーツチームが試合に勝ったか、あるいは天気などもです。しかし、XRP Ledgerの価格オラクル機能は、特に資産価格の提供を目的として設計されています。

{% /admonition %}


## オラクルの仕組み

ほとんどのオラクルブロックチェーンのやり取りは、次のような仕組みになっています。

1. データは分散型オラクルネットワークによってオフチェーンで検証されます。
2. データはブロックチェーンに送信されます。
3. ブロックチェーンはその情報を使用して、エスクローからの資金放出などのスマートコントラクトを実行します。

このプロセスは逆方向にも機能し、トランザクション情報を外部システムにプッシュすることも可能です。


## XRP Ledgerの価格オラクル

XRPLの価格オラクルはネイティブのオンチェーンオラクルであり、XRP LedgerのネイティブDeFi機能を強化します。オフチェーン価格オラクルは、そのデータをXRPLオラクルに送信し、XRPLオラクルはオンチェーンにその情報を保存します。これにより、分散型アプリケーションは価格データについてXRPLオラクルに問い合わせることが可能になります。複数のXRPLオラクルに問い合わせることで、リスクと不正確性を最小限に抑えることができます。

このように価格フィードを標準化することで、すべての XRPL アプリが信頼できる共有データソースにアクセスできるようになります。

## 関連項目

- **リファレンス:**
    - [get_aggregate_priceメソッド][]
    - [Oracleエントリ][]
    - [OracleDeleteトランザクション][]
    - [OracleSetトランザクション][]

{% raw-partial file="/@l10n/ja/docs/_snippets/common-links.md" /%}
