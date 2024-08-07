# UNL (Unique Node List)

UNL(_Unique Node List_)は、サーバが共謀しないと信頼するバリデータのリストです。すべてのXRP Ledgerサーバは、UNLを設定しており、これにより、どのバリデータによる投票を監視対象とするか、どの投票をコンセンサスプロセス中に破棄するかが決まります。設計上、UNL内の各エントリは独立したエンティティを表す必要があります。これらのエンティティは、企業や大学、他の種類の組織、または個人である可能性があります。各エントリが別々のエンティティであることにより、誰もがネットワークを正常に維持するための最小限の責任しか負いません。

バリデータは公平であることが意図されており、技術の制約内で可能な限り早くすべてのトランザクションを処理することが求められます。バリデータは、任意の理由でいくつかのトランザクションをブロックしたり検閲したりしてはいけません。なぜなら、それはネットワークが合意に達するのを妨げるからです。さらに重要なのは、バリデータが可能な限りオンラインで稼働していることです。ただし、XRP Ledgerは、ネットワークやバリデータ自体の欠陥を許容するように設計されています。一部のバリデータがオフラインであったり誤った設定、バグが存在、または明らかに悪意のある場合でも、大多数のバリデータが正常に動作していればネットワークは進行できるように設計されており、超多数(80％超)が同意しない限りネットワークは過去の履歴やルールと矛盾するトランザクションを受け入れることはありません。これらの点が考慮され、UNL内のバリデータは、多くのバリデータが同じ方法で同時に失敗する可能性を最小限に抑えるために選択されます。

## UNLの重複

各サーバの運用者は、どのバリデータが自分のUNLに設定されるかを完全にコントロールできます。しかし、2つのサーバが全く異なるUNLで動作している場合、台帳（およびその中のトランザクション）がどのように検証されるかについて、異なる結論に達する可能性があります。フォークが起きると、異なる立場の当事者は何が起きたかについて相互に合意することができず、互いに取引することができなくなります。フォークを回避するために、XRP Ledgerのサーバは、互いに重複度の高いUNLで構成される必要があります。

当初は、2つのサーバ間に60％の重複があれば、それらのサーバがフォークするのを防ぐのに十分であると信じられていました。しかし、[さらなる研究](./consensus-research.md)では最悪の場合、フォークを防ぐには90％の重なりが必要であることを示しました。他の人が使っているUNLとの重複が少ないほど、フォークする可能性が高くなります。

## 推奨バリデータリスト

他のバリデータと重複の少ない、多様で信頼できるバリデータリストを簡単に入手するために、XRP Ledgerは推奨バリデータリストの方式を採用しています。あるサーバは、_発行者_ から推奨リストをダウンロードし、そのリストをUNLとして使用するように設定することができます。つまり、サーバのUNLは、公開されているリストのいずれかに登録されているすべてのバリデータで構成されます。同一バリデータが複数のリストに登録されている場合でも、サーバのUNLには一度しか登録されません。

推奨リストはその発行者の公開鍵によって証明されます。通常、推奨リストはダウンロード可能なウェブサイトに関連付けられていますが、ウェブサイトへのアクセスに問題がある場合に備えて、ピアツーピアネットワークを介してリストを中継することもできます。

現在、XRP Ledgerサーバのデフォルト設定では、XRP Ledger Foundationが公開するリストとRippleが公開するリストの2つを使用しています。通常、これらのリストは互いに非常に似ているか、あるいは同一です。_デフォルトUNL_ (dUNLと略されることもあります)という用語は、デフォルト設定で指定された単一または複数のリストに含まれるバリデータのセットを指します。

誰でも署名付きバリデータリストを適切な形式で公開することができます。これは、署名付きバイナリデータを含むJSONドキュメントです。推奨されるリストの形式についての詳細は、[Validator Listメソッド](/ja/docs/references/http-websocket-apis/peer-port-methods/validator-list/)をご覧ください。

推奨バリデータリストは時間の経過とともに更新する必要があり、より質の高いバリデータを追加したり、信頼性の低いバリデータや撤退するバリデータを削除したりします。通常、推奨バリデータリストには有効期限があり、その期限までに更新が行われることが期待されています。リストにはシーケンス番号もあり、最も高いシーケンス番号がそのリストの最新のバージョンとなり、古いバージョンは無効になります。また、サーバがいつ新しいバージョンに切り替えるかを指定し、更新されたリストがそれを使うすべてのサーバに伝搬する時間を確保できるように、有効日を設定することができます。

発行者は日常的な新しいトランザクションの検証には関与しませんが、どのバリデータが広く信頼されるかを選択する上で大きな力を行使します。サーバ運用者は、どのバリデータリスト発行者を選択するかを慎重に決めるべきです。発行者の不注意が、そのリストを使用するサーバの信頼性に影響を与える可能性があるからです。

## バリデーターを推奨リストに登録する方法

それぞれの発行者は掲載されるための独自の基準を定義できます。しかし、通常、バリデータが発行者の推奨リストに追加されるためには次のような基準があります。

- 少なくとも1年間の稼働率が高く、他のネットワークと高い整合性を持つ
- [ドメイン検証](/ja/docs/references/xrp-ledger-toml/#domain-verification)を行なっている
- 既にバリデータを運営している会社の従業員などではなく、XRP Ledgerのコミュニティにおいて独立し認知された存在である
- 他のバリデータと物理的に独立した場所で運用されている(例えば、1つのデータセンターで障害が発生した場合に、多くのバリデータがダウンすることが無いように)

バリデータリストの運用者は、推奨リストに掲載する候補者と面談を行い、その候補者がこれらの要件およびその他の要件を満たしていること、また、将来にわたってサーバの運用を継続することを保証できることを確認することができます。
