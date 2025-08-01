---
html: commandline-usage.html
name: コマンドラインの使い方
parent: infrastructure.html
seo:
    description: rippledサーバのコマンドライン使用オプションです。
curated_anchors:
  - name: 使用できるモード
    anchor: "#使用できるモード"
  - name: デーモンモードのオプション
    anchor: "#デーモンモードのオプション"
  - name: スタンドアロンモードのオプション
    anchor: "#スタンドアロンモードのオプション"
  - name: クライアントモードのオプション
    anchor: "#クライアントモードのオプション"
  - name: 単体テスト
    anchor: "#単体テスト"
labels:
  - コアサーバ
---
# コマンドラインの使い方

`rippled`の実行可能ファイルは、通常はXRP Ledgerを処理するデーモンとして実行されますが、他のモードでも実行できます。このページでは、コマンドラインから実行する場合に`rippled`に渡すことができるすべてのオプションを説明します。

## 使用できるモード

- **デーモンモード** - デフォルトです。XRP Ledgerに接続して、トランザクションを処理し、レジャーデータベースを構築します。
- **スタンドアロンモード** - `-a`または`--standalone`オプションを使用します。他のサーバには接続できない以外は、デーモンモードと同様です。このモードは、トランザクション処理やその他の機能のテストに使用できます。
- **クライアントモード** - APIメソッドの名前を指定して、別の`rippled`サーバにJSON-RPCクライアントとして接続し、その後終了します。実行可能ファイルがすでに別のプロセスで実行中である場合に、このモードを使用してサーバのステータスとレジャーデータを確認できます。
- **その他の使用法** - 以下の各コマンドを実行すると、`rippled`実行可能ファイルが何らかの情報を出力し、その後終了します。
    - **ヘルプ** - 使用法の説明を出力するには、`-h`または`--help`を使用します。
    - **単体テスト** - 単体テストを実行し、結果の概要を出力するには、`-u`または`--unittest`を使用します。rippledが正しくコンパイルされていることを確認する場合に便利です。
    - **バージョンステートメント** - `rippled`のバージョン番号を出力し、その後終了するには、`--version`を使用します。

## 汎用オプション

ほとんどのモードに適用されるオプションは、以下の通りです。

| オプション          | 説明                                                |
|:----------------|:-----------------------------------------------------------|
| `--conf {FILE}` | デフォルトのロケーションで構成ファイルを検索する代わりに、構成ファイルとして`{FILE}`を使用します。指定されていない場合、`rippled`は最初にローカル作業ディレクトリで`rippled.cfg`ファイルがあるかどうかを調べます。Linuxでは、このファイルが見つからない場合`rippled`は次に`$XDG_CONFIG_HOME/ripple/ripple.cfg`を確認します。（一般的に`$XDG_CONFIG_HOME`の場所は`$HOME/.config`です。） |

### 詳細レベルのオプション

次の汎用オプションは、標準出力とログファイルに書き込まれる情報の量を制御します。

| オプション      | 短縮形 | 説明                                    |
|:------------|:--------------|:-----------------------------------------------|
| `--debug`   |               | **廃止予定** トレースレベルのデバッグを有効にします（`--verbose`のエイリアス）。代わりに[log_levelメソッド][]を使用してください。 |
| `--silent`  |               | 起動中にログを標準出力と標準エラー出力に書き込みません。冗長なログを削減するために`rippled`をsystemdユニットとして開始する場合に推奨されます。 |
| `--verbose` | `-v`          | **廃止予定** トレースレベルデバッグを有効にします。代わりに[log_levelメソッド][]を使用してください。 |



## デーモンモードのオプション

```bash
rippled [OPTIONS]
```

デーモンモードは、`rippled`のデフォルトの運用モードです。[汎用オプション](#汎用オプション)の他に、以下のいずれかのオプションを指定できます。

| オプション              | 説明                                            |
|:--------------------|:-------------------------------------------------------|
| `--fg`              | デーモンをフォアグラウンドでシングルプロセスとして実行します。このオプションを指定しない場合、`rippled`は1番目のプロセスがモニターとして実行されている間に、デーモンの2番目のプロセスをフォークします。 |
| `--import`          | 完全に起動する前に、別の`rippled`サーバのレジャーストアーからレジャーデータをインポートしてください。構成ファイルに有効な`[import_db]`スタンザが指定されている必要があります。 |
| `--net`             | **廃止予定** デバッグのためのオプションです。ネットワークからレジャーを取得できるようになるまで、ローカルレジャーを作成しません。 |
| `--nodetoshard`     | 完全に起動する前に、すべての完全な[履歴シャード](configuration/data-retention/history-sharding.md)をレジャーストアーからシャードストアーにコピーしてください（シャードストアーに設定されている最大ディスク容量まで）。CPUとI/Oを大量に使用します。注意: このコマンドは、データを（移動するのではなく）コピーするため、シャードストアーとレジャーストアーの両方にデータを保存するのに十分なディスク容量が必要です。 <!--{# Task for writing a tutorial to use this: DOC-1639 #}--> |
| `--quorum {QUORUM}` | これは[テストネットワーク](../concepts/networks-and-servers/parallel-networks.md)のブートストラップ用のオプションです。検証のための最小定数をオーバーライドするには、`{QUORUM}`の信頼できるバリデータの同意を必要とします。デフォルトでは、検証のための定数は、信頼できるバリデータの実際の数に基づき、安全な数に自動的に設定されます。一部のバリデータがオンラインではない場合、このオプションにより、標準定数よりも少ない数のバリデータで続行できるようになります。**警告:** 定数を手動で設定すると、設定した値が小さすぎるためにサーバがネットワークの他の部分から分岐することを防ぐことができない可能性があります。このオプションは、コンセンサスを十分に理解し、標準以外の設定を使用する必要がある場合にのみ使用してください。 |

次のフィールドは廃止されました： `--validateShards`。 {% badge href="https://github.com/XRPLF/rippled/releases/tag/1.7.0" %}削除: rippled 1.7.0{% /badge %}

## スタンドアロンモードのオプション

```bash
rippled --standalone [OPTIONS]
rippled -a [OPTIONS]
```
スタンドアロンモードで実行します。このモードでは、`rippled`はネットワークに接続しないか、またはコンセンサスを実行しません。（それ以外の場合、`rippled`はデーモンモードで実行されます。）

### 初期レジャーオプション

以下のオプションにより、起動時に最初に読み込むレジャーが判断されます。これらはのオプションは、履歴レジャーのリプレイまたはテストネットワークのブートストラップのためのものです。

| オプション                | 説明                                          |
|:----------------------|:-----------------------------------------------------|
| `--ledger {LEDGER}`   | `{LEDGER}`（レジャーハッシュまたはレジャーインデックス）により初期レジャーと識別されているレジャーバージョンを読み込みます。指定されたレジャーバージョンは、サーバのレジャーストアーに格納される必要があります。 |
| `--ledgerfile {FILE}` | 指定された`{FILE}`からレジャーバージョンを読み込みます（このファイルには完全なレジャーがJSONフォーマットで格納されている必要があります）。このようなファイルの例については、付属の{% repo-link path="_api-examples/rippled-cli/ledger-file.json" %}`ledger-file.json`{% /repo-link %}をご覧ください。 |
| `--load`              | **廃止予定** デバッグのためのオプションです。ディスク上のレジャーストアーから初期レジャーを読み込むだけです。 |
| `--replay`            | デバッグのためのオプションです。`--ledger`と組み合わせて使用し、レジャーの閉鎖をリプレイします。サーバのレジャーストアーには、当該レジャーとその直前のバージョンのレジャーがすでに格納されている必要があります。サーバでは、前のレジャーをベースとして使用して、指定されたレジャーのすべてのトランザクションが処理されます。その結果、指定されたレジャーが再作成されます。デバッガーを使用して、特定のトランザクションの処理ロジックを分析するためのブレークポイントを追加できます。 |
| `--start`             | デバッグのためのオプションです。既知のすべてのAmendment（反対票を投じるようにサーバに設定されているAmendmentを除く）が適用されている新しいジェネシスレジャーを使用して開始します。したがってこれらのAmendmentの機能は、2週間の[Amendmentプロセス](../concepts/networks-and-servers/amendments.md)期間ではなく、2番目のレジャーの開始時から使用可能になります。 |
| `--valid`            | **廃止予定** デバッグのためのオプションです。ネットワークとの完全同期の前であっても、初期レジャーを有効なネットワークレジャーと見なします。 |

## クライアントモードのオプション

```bash
rippled [OPTIONS] -- {COMMAND} {COMMAND_PARAMETERS}
```

クライアントモードでは、`rippled`実行可能ファイルが別の`rippled`サービスのクライアントとして動作します。（サービスは別のプロセスでローカルに実行されている同じ実行可能ファイルである場合や、別のサーバ上の`rippled`サーバである場合があります。）

クライアントモードで実行するには、いずれかの[`rippled` API](../references/http-websocket-apis/index.md)メソッドの[コマンドライン構文](../references/http-websocket-apis/api-conventions/request-formatting.md#コマンドライン形式)を指定します。

クライアントモードは、個別のコマンド構文の他に、[汎用オプション](#汎用オプション)と以下のオプションに対応します。

| オプション                  | 説明                                        |
|:------------------------|:---------------------------------------------------|
| `--rpc`                 | サーバをクライアントモードで実行することを明示的に指定します。必須ではありません。 |
| `--rpc_ip {IP_ADDRESS}` | 指定されたIPアドレスの`rippled`サーバに接続します。オプションでポート番号も指定します。 |
| `--rpc_port {PORT}`     | **廃止予定** 指定されたポートで`rippled`サーバに接続します。代わりに、`--rpc_ip`を使用してIPアドレスとともにポートを指定します。 |

{% admonition type="success" name="ヒント" %}一部の引数では、マイナスの値を指定できます。APIコマンドの引数がオプションとして解釈されないようにするには、コマンド名の前に`--`引数を渡します。{% /admonition %}

使用例（使用可能な最も古いレジャーバージョンから最新のレジャーバージョンまでのアカウントトランザクション履歴を取得）:

```bash
rippled -- account_tx r9cZA1mLK5R5Am25ArfXFmqgNwjZgnfk59 -1 -1
```


## 単体テスト

```bash
rippled --unittest [OPTIONS]
rippled -u [OPTIONS]
```

単体テストでは、`rippled`ソースコードに組み込まれているテストを実行し、実行可能ファイルが予期されているとおりに動作することを確認します。単体テストの実行が完了すると、結果の概要が表示され、終了します。単体テストでは、組み込みのデータ型やトランザクション処理ルーチンなどの機能がカバーされます。

単体テストから失敗が報告される場合、一般的に次のいずれかの状況が発生しています。

- `rippled`のコンパイル時に問題が発生し、意図したとおりに機能していない
- `rippled`のソースコードにバグがある
- 単体テストにバグがあるか、単体テストが新しい動作に対応して更新されていない

単体テストの実行時には、[汎用オプション](#汎用オプション)と以下のいずれかのオプションを指定できます。

| オプション                             | 短縮形 | 説明             |
|:-----------------------------------|:--------------|:------------------------|
| `--unittest-ipv6`                  |               | 単体テストの実行時に[IPv6](https://en.wikipedia.org/wiki/IPv6)を使用してローカルサーバに接続します。このオプションが指定されていない場合、単体テストではIPv4が代わりに使用されます。{% badge href="https://github.com/XRPLF/rippled/releases/tag/1.1.0" %}新規: rippled 1.1.0{% /badge %} |
| `--unittest-jobs {NUMBER_OF_JOBS}` |               | 指定された数のプロセスを使用して単体テストを実行します。これにより、マルチコアシステムの単体テストをより短時間で終了できます。`{NUMBER_OF_JOBS}`には、使用するプロセスの数を示すプラスの整数値を指定します。 |
| `--unittest-log`                   |               | `--quiet`が指定されている場合でも、単体テストにてログへの書き込みができるようにします。（それ以外の影響はありません。） |
| `--quiet`                          | `-q`          | 単体テストの実行時に出力される診断メッセージの数が減少します。 |


### 特定の単体テスト

```bash
rippled --unittest={TEST_OR_PACKAGE_NAME}
```

デフォルトでは、`rippled`は「手動」に分類されている単体テスト以外のすべての単体テストを実行します。テストの名前を指定してテストを個別に実行するか、またはパッケージ名を指定してテストのサブセットを実行することができます。

テストはパッケージの階層にまとめられます。パッケージは`.`文字で区切られ、テストケース名で終わります。

#### 単体テストの出力

```bash
rippled --unittest=print
```

`print`は、使用可能なテストとそのパッケージのリストを出力する特殊な単体テストです。

#### 手動の単体テスト

完了に時間を要する一部の単体テストは、「手動」に分類されています。このようなテストについては、`print`単体テストの出力に`|M|`と表示されます。すべての単体テストまたは単体テストのパッケージを実行するときには、手動テストはデフォルトで実行されません。手動テストを個別に実行するには、テスト名を指定します。例:

```bash
$ ./rippled --unittest=ripple.tx.OversizeMeta
ripple.tx.OversizeMeta
Longest suite times:
  60.9s ripple.tx.OversizeMeta
60.9s, 1 suite, 1 case, 9016 tests total, 0 failures
```

#### 単体テストの引数の指定

特定の手動単体テストでは引数を指定できます。以下のオプションを使用して引数を指定します。

| オプション                  | 説明                                        |
|:------------------------|:---------------------------------------------------|
| `--unittest-arg {ARG}`  | 実行される単体テストに引数`{ARG}`を指定します。引数を受け入れる単体テストはそれぞれ、固有の引数形式を定義しています。  |

{% raw-partial file="/@l10n/ja/docs/_snippets/common-links.md" /%}
