---
seo:
    description: A list of addresses for multi-signing transactions.
labels:
  - Security
---
# SignerList
[[Source]](https://github.com/XRPLF/rippled/blob/f64cf9187affd69650907d0d92e097eb29693945/include/xrpl/protocol/detail/ledger_entries.macro#L111-L118 "Source")

A `SignerList` entry represents a list of parties that, as a group, are authorized to sign a transaction in place of an individual account by [multi-signing](../../../../concepts/accounts/multi-signing.md). You can create, replace, or remove a signer list using a [SignerListSet transaction][].

_(Added by the [MultiSign amendment][].)_

## Example {% $frontmatter.seo.title %} JSON

```json
{
    "Flags": 0,
    "LedgerEntryType": "SignerList",
    "OwnerNode": "0000000000000000",
    "PreviousTxnID": "5904C0DC72C58A83AEFED2FFC5386356AA83FCA6A88C89D00646E51E687CDBE4",
    "PreviousTxnLgrSeq": 16061435,
    "SignerEntries": [
        {
            "SignerEntry": {
                "Account": "rsA2LpzuawewSBQXkiju3YQTMzW13pAAdW",
                "SignerWeight": 2
            }
        },
        {
            "SignerEntry": {
                "Account": "raKEEVSGnKSD9Zyvxu4z6Pqpm4ABH8FS6n",
                "SignerWeight": 1
            }
        },
        {
            "SignerEntry": {
                "Account": "rUpy3eEg8rqjqfUoLeBnZkscbKbFsKXC3v",
                "SignerWeight": 1
            }
        }
    ],
    "SignerListID": 0,
    "SignerQuorum": 3,
    "index": "A9C28A28B85CD533217F5C0A0C7767666B093FA58A0F2D80026FCC4CD932DDC7"
}
```

## {% $frontmatter.seo.title %} Fields

In addition to the [common fields](../common-fields.md), {% code-page-name /%} entries have the following fields:

| Name                | JSON Type | Internal Type | Required? | Description                |
|:--------------------|:----------|:--------------|:----------|:---------------------------|
| `LedgerEntryType`   | String    | UInt16        | Yes       | The value `0x0053`, mapped to the string `SignerList`, indicates that this is a SignerList ledger entry. |
| `OwnerNode`         | String    | UInt64        | Yes       | A hint indicating which page of the owner directory links to this object, in case the directory consists of multiple pages. |
| `PreviousTxnID`     | String    | UInt256       | Yes       | The identifying hash of the transaction that most recently modified this object. |
| `PreviousTxnLgrSeq` | Number    | UInt32        | Yes       | The [index of the ledger][Ledger Index] that contains the transaction that most recently modified this object. |
| `SignerEntries`     | Array     | Array         | Yes       | An array of Signer Entry objects representing the parties who are part of this signer list. |
| `SignerListID`      | Number    | UInt32        | Yes       | An ID for this signer list. Currently always set to `0`. If a future [amendment](../../../../concepts/networks-and-servers/amendments.md) allows multiple signer lists for an account, this may change. |
| `SignerQuorum`      | Number    | UInt32        | Yes       | A target number for signer weights. To produce a valid signature for the owner of this SignerList, the signers must provide valid signatures whose weights sum to this value or more. |

The `SignerEntries` may be any combination of funded and unfunded addresses that use either secp256k1 or ed25519 keys.

### Signer Entry Object

Each member of the `SignerEntries` field is an object that describes that signer in the list. A Signer Entry has the following fields:

| Name            | JSON Type | Internal Type | Description                    |
|:----------------|:----------|:--------------|:-------------------------------|
| `Account`       | String    | AccountID     | An XRP Ledger address whose signature contributes to the multi-signature. It does not need to be a funded address in the ledger. |
| `SignerWeight`  | Number    | UInt16        | The weight of a signature from this signer. A multi-signature is only valid if the sum weight of the signatures provided meets or exceeds the signer list's `SignerQuorum` value. |
| `WalletLocator` | String    | UInt256       | _(Optional)_ Arbitrary hexadecimal data. This can be used to identify the signer or for other, related purposes. _(Added by the [ExpandedSignerList amendment][].)_ |

When processing a multi-signed transaction, the server looks up the `Account` values with respect to the ledger at the time of transaction execution. If the address _does not_ correspond to a funded [AccountRoot ledger entry](accountroot.md), then only the [master private key](../../../../concepts/accounts/cryptographic-keys.md) associated with that address can be used to produce a valid signature. If the account _does_ exist in the ledger, then it depends on the state of that account. If the account has a Regular Key configured, the Regular Key can be used. The account's master key can only be used if it is not disabled. A multi-signature cannot be used as part of another multi-signature.

## {% $frontmatter.seo.title %} Flags

_(Added by the [MultiSignReserve amendment][].)_

SignerList entries can have the following value in the `Flags` field:

| Flag Name          | Hex Value    | Decimal Value | Description              |
|:-------------------|:-------------|:--------------|:-------------------------|
| `lsfOneOwnerCount` | `0x00010000` | 65536         | If this flag is enabled, this SignerList counts as one item for purposes of the [owner reserve](../../../../concepts/accounts/reserves.md#owner-reserves). Otherwise, this list counts as N+2 items, where N is the number of signers it contains. This flag is automatically enabled if you add or update a signer list after the [MultiSignReserve amendment][] is enabled. |


## Signer Lists and Reserves

A signer list contributes to its owner's [reserve requirement](../../../../concepts/accounts/reserves.md). Removing the signer list frees up the reserve.

The [MultiSignReserve amendment][] (enabled 2019-04-17) made it so each signer list counts as one item, regardless of how many members it has. As a result, the owner reserve for any signer list added or updated after this time is {% $env.PUBLIC_OWNER_RESERVE %}.

A signer list created before the [MultiSignReserve amendment][] itself counts as two items towards the owner reserve, and each member of the list counts as one. As a result, the total owner reserve associated with an old signer list is anywhere from 3 times to 10 times as much as a new signer list. To update a signer list to use the new, reduced reserve, update the signer list by sending a [SignerListSet transaction][].


## SignerList ID Format

The ID of a `SignerList` entry is the SHA-512Half of the following values, concatenated in order:

* The RippleState space key (`0x0053`)
* The AccountID of the owner of the signer list
* The `SignerListID` (currently always `0`)

{% raw-partial file="/docs/_snippets/common-links.md" /%}
