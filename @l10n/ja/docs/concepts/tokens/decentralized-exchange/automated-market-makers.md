---
seo:
    description: 自動マーケットメーカー（AMM）は、資産ペア間の流動性を提供し、分散型取引所のオーダーブックを補完すると同時に、流動性提供者に利益を提供します。
labels:
  - XRP
  - 分散型取引所
  - AMM
---
# 自動マーケットメーカー(AMM)とは?

_([AMM amendment][]により追加されました。)_

自動マーケットメーカー(AMM)は、XRP Ledgerの分散型取引所において流動性を提供します。個々のAMMは2つの資産のプールを保有します。ユーザは数式で定められた取引レートによりその2つの資産間でスワップが可能です。

![自動マーケットメーカー](/docs/img/cpt-amm.png)

資産をAMMに預ける人は、_流動性供給者(LP/Liquidity Provider)_ と呼ばれます。その対価として流動性供給者はAMMからLPトークンを受け取ります。

![流動性提供者によるLPトークンの受け取り](/docs/img/cpt-amm-lp-receiving-lpts.png)

流動性供給者はLPトークンを利用して以下のことが可能になります。

- LPトークンを、AMMのプール内の資産の一部（プールが得た手数料を含む）と交換する。
- AMMの手数料設定を変更するために投票する。票は、投票者が保有するLPトークンの数に基づいて重み付けされます。
- AMMの取引手数料の一時的な割引を得るために、LPトークンの一部を入札する。

## AMMの仕組み

AMMは2つの異なる資産を保有します。そのうちの最大1つはXRPとすることができ、そのうちの1つまたは両方が[トークン](../index.md)であることができます。
任意の資産ペアに対して、レジャーには最大1つのAMMが存在することができます。存在しない場合、誰でもその資産ペアのAMMを作成することができます。また、すでに存在している場合、AMMに預け入れることができます。

分散型取引所で取引を行う場合、[オファー](offers.md)と[クロスカレンシー支払い](../../payment-types/cross-currency-payments.md)は、自動的にAMMを使用して取引を成立させることができます。単一の取引は、オファー、AMM、またはその両方の組み合わせによって、よりコストが低い方法で実行されることになります。

![オファーやAMMまたはその両方を利用する1つの取引](/docs/img/cpt-amm-or-offer.png)

{% admonition type="info" name="注記" %}

`Payment`または`OfferCreate`トランザクションがAMMとやり取りしたかどうかは、トランザクションメタデータにある[`RippleState`](../../../references/protocol/ledger-data/ledger-entry-types/ripplestate.md)レジャーエントリを確認することで判断できます。`Flags`値が`16777216`の場合、AMMの流動性が消費されたことを示します。

{% /admonition %}

AMMは、プール内の資産残高に基づき取引レートを設定します。AMMに対して取引を行うと、AMMが保有する資産残高の変動に応じて、取引レートが調整されます。一方の資産の量が減れば、その資産の価格が上がり、他方の資産の量が増えれば、その資産の価格が下がります。

![資産残高が増えると価格が下がり、資産残高が減ると価格が上がる](/docs/img/cpt-amm-balance.png)

AMMは、プール内の資産残高が多いほど、一般的により良い取引レートを提供します。同一取引であればプール内の残高が大きい方がAMMの資産バランスに生じる変化は小さくなるからです。AMMの2つの資産のバランスが崩れれば崩れるほど、交換レートは極端に悪化します。

また、AMMは交換レートに加え、一定割合の取引手数料を徴収しています。

XRP Ledgerの実装は、重みパラメータを0.5とした _幾何平均_ AMMですので、_定積_ マーケットメーカーのように機能します。 _定積_ AMMの公式や一般的なAMMの経済学についての詳しい説明は、[Kris Machowski's Introduction to Automated Market Makers](https://www.machow.ski/posts/an_introduction_to_automated_market_makers/)をご覧ください。

### トークン発行者

発行者が異なるトークンは異なる資産と見なされます。つまり、同じ通貨コードで異なる発行者の2つのトークンに対してAMMを作成することができます。例えば、WayGateによって発行された _FOO_ は、StableFooによって発行された _FOO_ とは異なるトークンです。同様に、トークンは同じ発行者で異なる通貨コードを持つことができます。取引方向は関係ありません。FOO.WayGateからXRPへのAMMは、XRPからFOO.WayGateへのAMMと同じです。

### 資産の制限

資産間の資金の流れが比較的活発でバランスが取れている場合、手数料は流動性供給者に対する受動的な収入源となります。しかし、資産間の相対価格が変動すると、流動性供給者は[通貨リスク](https://www.investopedia.com/terms/c/currencyrisk.asp)による損失を被ることになります。

### DEXの相互性

XRP LedgerのAMMは、Central Limit Order Book(CLOB/オーダーブック)ベースの分散型取引所と統合することで分散型取引所全体の流動性を高めています。オファーと支払いは、流動性プール内でのスワップ、オーダーブックを通じたスワップ、またはその両方が最良のレートを提供し、それに応じて約定されるかを自動的に最適化します。この相互性により、これらの取引は、分散型取引所のオファーやAMMプールの一方またはその両方を通じた取引の最も効率的な経路を使用して約定されます。

以下の図は、オファーが分散型取引所の他のオファーとAMMの流動性との相互作用を示しています。

![DEXのオファーの経路](/docs/img/amm-clob-diagram.png)

### 資産の制限 <a id="restrictions-on-assets"></a>

不正利用を防ぐため、AMMで利用できる資産にはいくつかの制限があります。これらの制約を満たさない資産でAMMを作成しようとすると、トランザクションは失敗します。ルールは以下のとおりです。

- 他のAMMのLPTokenをAMMの資産として利用することはできません。
- 資産が、発行者が[認可トラストライン](../fungible-tokens/authorized-trust-lines.md)を使用しているトークンである場合、AMMの作成者はそれらのトークンを保有する権限がなければなりません。認可されているトラストラインだけが、そのトークンをAMMに預けたり引き出したりすることができます。
- [Clawback Amendment][] が有効な場合、Clawbackが有効なトークンでAMMを作成することはできません。

## LPトークン

AMMの作成者は、最初の流動性供給者となり、AMMのプール内の資産の100％の所有権を表すLPトークンを受け取ります。LPトークンの一部または全部を交換して、現在のプール残高に比例した資産をAMMから引き出せます。(この比率は、人々がAMMに対して取引を行うにつれて変化します）AMMは、同時に両方の資産を引き出す際に手数料はかかりません。

例えば、5ETHと5USDでAMMを作成し、その後誰かが1.26USDを1ETHに交換したとすると、現在プールには4ETHと6.26USDが入っています。LPトークンの半分を使用して、2ETHと3.13USDを引き出すことができます。

![AMMとの取引とLPトークンの引き出しの例](/docs/img/cpt-amm-lp-tokens.png)

誰でも既存のAMMに資産を預けることができます。預け入れると、その金額に応じて新しいLPトークンを受け取ります。流動性供給者がAMMから引き出すことができる金額は、発行済みのLPトークンの総数に対する流動性提供者のLPトークンの保有割合に基づきます。

LPトークンはXRP Ledgerの他のトークンと同様に、様々な種類の支払いで使用したり、分散型取引所で取引したり、新しいAMMの資産として預けることも可能です。(LPトークンを支払い(Payment)として受け取るには、AMMアカウントを発行元として、限度額が0でない[トラストライン](../fungible-tokens/index.md)を設定する必要があります)。ただし、LPトークンをAMMに直接送る(換金する)には[AMMWithdraw][]トランザクションタイプを使用し、他のタイプの支払いは使用できません。同様に、AMMのプールに資産を送るには、[AMMDeposit][]トランザクションタイプを使用する必要があります。

AMMは、発行済のLPトークンがない場合に限り、AMMの資産プールが空になるように設計されています。こうした状況は、[AMMWithdraw][]トランザクションの結果としてのみ発生し、発生した時点でAMMは自動的に削除されます。

### LPトークンの通貨コード

LPトークンは、160ビットの16進法["非標準"フォーマット](../../../references/protocol/data-types/currency-formats.md#非標準通貨コード)の特別なタイプの通貨コードを使用します。これらのコードの最初の8ビットは`0x03`です。残りのコードは、2つの資産の通貨コードとその発行者のSHA-512ハッシュで、最初の152ビットまで切り捨てたものです。(資産は、数値の低い通貨と発行者のペアを最初にする「正規化された順序」で配置されます。)その結果、LPトークンは、通貨と発行者のペアを最初にする「正規化された順序」で配置されます。その結果、ある資産ペアのAMMのLPトークンは、予測可能で一貫した通貨コードを持っています。


## 取引手数料

取引手数料は流動性供給者の収益源であり、プールの資産に対して他者に取引をさせることによる為替リスクを相殺するものであり、取引手数料は流動性提供者に直接支払われずにAMMに支払われます。流動性供給者は自分のLPトークンをAMMのプールの一定割合と交換することができるため、利益を得ることができます。

流動性供給者は、投票によって取引手数料を0％から1％まで、0.001％刻みで設定することが出来ます。流動性供給者は取引手数料を適切に設定するインセンティブがあり、手数料が高すぎる場合、トレーダーはより良いレートを得るために代わりにオーダーブックを使用することになります。手数料が低すぎる場合、流動性供給者はこのプールへの貢献に対してメリットが得られなくなります。

AMMは、流動性プロバイダに対して、保有するLPトークンの数に応じて、手数料に関する投票権を与えます。投票するには、流動性供給者が[AMMVote][]トランザクションを送信します。誰かが新しい票を入れるたびに、AMMは手数料を再計算し、直近の票の平均を、それらの投票者が保有するLPトークンの数で重み付けしたものにします。この方法では、最大8つの流動性供給者の投票がカウントされます。それ以上の流動性供給者が投票しようとすると、上位8つの投票（保有LPトークンの多い順）だけがカウントされます。流動性供給者のLPトークンのシェアは、様々な理由(例えば[オファー](offers.md)を使ったトークンの取引)で急速に変化しますが、取引手数料は誰かが新しい票を入れるたびに再計算されます（その票がトップ8に入っていない場合でも計算されます)。

{% admonition type="info" name="注記" %}

_AMMの取引手数料(Trading Fees)_ と _トークンの送金手数料(Transfer Fees)_ は異なるものです。

| 違い                         | AMMの取引手数料                            | トークンの送金手数料             |
| ---------------------------- | ------------------------------------------ | -------------------------------- |
| 誰が手数料を設定する?        | AMMの流動性提供者                          | トークンの発行者                 |
| 手数料が適用されるタイミング | AMMプールを利用した取引時                  | トークンの送金時                 |
| 手数料を回収できるか?        | はい、流動性提供者がLPトークンを引き出す時 | いいえ、手数料はバーンされます。 |

{% /admonition %}

### オークションスロット

XRP LedgerのAMMの設計には「オークションスロット」が含まれています。流動性プロバイダはLPトークンを落札に利用してオークションスロットを獲得し、24時間取引手数料の割引を受けることができます。落札に利用されたLPトークンはAMMに回収されます。

AMMの資産価格が外部市場で大きく変動すると、トレーダーはAMMから利益を得るために裁定取引を行うことができます。これは流動性プロバイダに損失をもたらします。オークションメカニズムは、その価値の一部を流動性プロバイダに返し、AMMの価格をより迅速に外部市場とバランスを取ることを意図しています。

一度に複数のアカウントがオークションスロットを保持することはできませんが、落札者は割引を受けるために追加で最大4つのアカウントを指定することができます。スロットが現在使用中の場合、現在のスロット保持者を追い出すために落札する必要があります。追い出された場合、残り時間に応じて一部の落札金額が返却されます。アクティブなオークションスロットを保持している間は、そのAMMに対して取引を行う際に、通常の取引手数料の1/10（10分の1）の割引が適用されます。

オークションスロットの最小入札額は、空または期限切れの場合、現在のLPトークンの総数に取引手数料を掛けたものを25で割ったものです。(擬似コードで表すと、`MinBid = LPTokens * TradingFee / 25`です。) オークションスロットが占有されている場合、現在のスロット保持者が支払った金額の105%までの最小入札額を支払う必要があります。


## 台帳上の表示

台帳の状態データでは、AMMは複数の[レジャーエントリのタイプ](../../../references/protocol/ledger-data/ledger-entry-types/index.md)で構成されています。

- 自動マーケットメーカー自体を記述した[AMMエントリ][]

- AMMのLPトークンを発行し、AMMのXRP（保有している場合）を保有する特別な[AccountRootエントリ][]

    このAccountRootのアドレスは、AMMの作成時にランダムに選ばれ、AMMを削除して再作成した場合にも異なるアドレスが選ばれます。これは、AMMのアカウントにユーザが事前にXRPで資金を供給することを防止するためです。

- AMMのプールにあるトークンのAMM専用アカウントへの[トラストライン](../fungible-tokens/index.md)

これらのレジャーエントリはどのアカウントにも所有されていないため、[準備金要件](../../accounts/reserves.md)は適用されません。ただし、スパムを防ぐため、AMMを作成するための取引には特別な[トランザクションコスト](../../transactions/transaction-cost.md)があり、通常よりも多くのXRPを消費する必要があります。


## 削除

AMMは、[AMMWithdrawトランザクション][]がそのプールから全てのアセットを引き出すと削除されます。これは、AMMのすべての発行済みLPトークンを償還することによってのみ発生します。AMMの削除には、以下のようなAMMに関連するすべてのレジャーエントリの削除も含まれます。

- AMM
- AccountRoot
- AMMのLPトークンのトラストライン。これらのトラストラインは残高が0ですが、限度額など他の詳細がデフォルト以外の値に設定されている可能性があります。
- AMMのプールに存在するトークンのトラストライン。

AMMアカウントが削除されるときに、512を超えるトラストラインが設定されていた場合、出金は成功し、可能な限り多くのトラストラインを削除します。

AMMのプールに資産がない間は、誰でも[AMMDeleteトランザクション][]を送信してAMMを削除することができます。別の方法として、誰でも[特別な入金](../../../references/protocol/transactions/types/ammdeposit.md#空のammの場合の特殊なケース)を行うことで、AMMにあたかも新規であるかのように入金することができます。資産プールが空のAMMに対しては、他の操作は無効です。

空のAMMを削除することによる払い戻しやインセンティブはありません。

{% raw-partial file="/@l10n/ja/docs/_snippets/common-links.md" /%}
