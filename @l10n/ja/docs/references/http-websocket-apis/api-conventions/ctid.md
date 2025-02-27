---
html: ctid.html
parent: api-conventions.html
seo:
    description: CTID（Compact Transaction Identifier）は、検証済みトランザクションをチェーン全体で一意に識別する短い文字列です。
labels:
  - 開発
---
# トランザクション軽量識別子

CTID（トランザクション軽量識別子 / Compact Transaction Identifier）は、XRP Ledgerのメインネットを含む、あらゆる[ネットワーク](../../../concepts/networks-and-servers/parallel-networks.md)で利用可能な、検証済みトランザクションの一意な識別子です。

CTIDとトランザクションの[識別ハッシュ](../../../concepts/transactions/index.md#identifying-transactions)の違いは以下の通りです：

- CTIDは、ネットワークID、レジャーインデックス、レジャー内の位置に基づいて検証されたトランザクションを識別します。トランザクションがどのネットワークで検証されたかを特定するため、サイドチェーンへの接続など、複数のネットワークとやりとりする状況で使用できます。CTIDは64ビットで、通常は`C`で始まる16進数の大文字で、例えば`C005523E00000000`のように記述します。
- トランザクションの識別ハッシュは、そのトランザクションがどのチェーンで検証されたかに関係なく、その内容に基づいて署名されたトランザクションを識別します。これは暗号ハッシュであるため、トランザクションの内容が完全であることを証明するために使用することもできます。トランザクションハッシュは256ビットで、通常64文字の16進数で記述され、例えば`E08D6E9754025BA2534A78707605E0601F03ACE063687A0CA1BDDACFCD1698C7`となります。

{% admonition type="warning" name="注意" %}未検証のトランザクションにCTIDを使わないでください。トランザクションが最初に適用されたときと、コンセンサスプロセスによって検証されたときとで、トランザクションの正規順序が変わる可能性があります。{% /admonition %}

## 構造

CTIDは以下の要素を含みます(ビッグエンディアン順)。

1. 4ビット: CTIDであることを示す16進数の頭文字`C`
2. 28ビット: トランザクションが検証されたレジャーのインデックス
3. 16ビット: トランザクションのインデックス。これは[トランザクションのメタデータ](../../protocol/transactions/metadata.md)の`TransactionIndex`フィールドとして提供されます
4. 16ビット: トランザクションを検証したネットワークの[ネットワークID](../../protocol/transactions/common-fields.md#networkidフィールド)

{% admonition type="info" name="注記" %}レジャーインデックスは通常32ビットの符号なし整数として保存され、新しいレジャーが作成されるたびに1ずつ増加します。ネットワークのレジャーインデックスが268,435,455より大きい場合、28ビットに収まらないので、必要に応じて先頭の`C`を`D`、`E`、`F`にインクリメントする必要があります。これは少なくとも2043年までは必要ないと思われます。{% /admonition %}

## 関連項目

サンプルコードや背景などの詳細については、[XLS-0037 Standard](https://github.com/XRPLF/XRPL-Standards/tree/master/XLS-0037-concise-transaction-identifier-ctid)をご覧ください。
