---
seo:
    description: Create offers to buy or sell NFTs.
labels:
 - Non-fungible Tokens, NFTs
---
# NFTokenOffer
[[Source]](https://github.com/XRPLF/rippled/blob/f64cf9187affd69650907d0d92e097eb29693945/include/xrpl/protocol/detail/ledger_entries.macro#L34-L44 "Source")

An `NFTokenOffer` entry represents an offer to buy, sell or transfer an [NFT](../../../../concepts/tokens/nfts/index.md). You can create an NFT buy or sell offer by sending an [NFTokenCreateOffer transaction][].

_(Added by the [NonFungibleTokensV1_1 amendment][].)_

## Example {% $frontmatter.seo.title %} JSON

```json
{
    "Amount": "1000000",
    "Flags": 1,
    "LedgerEntryType": "NFTokenOffer",
    "NFTokenID": "00081B5825A08C22787716FA031B432EBBC1B101BB54875F0002D2A400000000",
    "NFTokenOfferNode": "0",
    "Owner": "rhRxL3MNvuKEjWjL7TBbZSDacb8PmzAd7m",
    "OwnerNode": "17",
    "PreviousTxnID": "BFA9BE27383FA315651E26FDE1FA30815C5A5D0544EE10EC33D3E92532993769",
    "PreviousTxnLgrSeq": 75443565,
    "index": "AEBABA4FAC212BF28E0F9A9C3788A47B085557EC5D1429E7A8266FB859C863B3"
}
```


### {% $frontmatter.seo.title %} Fields

| Name                |JSON Type             | [Internal Type][] | Required?   | Description |
|:--------------------|:---------------------|:------------------|:------------|:-----------|
| `Amount`            | [Currency Amount][]  | Amount            | Yes         | Amount expected or offered for the NFT. If the token has the `lsfOnlyXRP` flag set, the amount must be specified in XRP. Sell offers that specify assets other than XRP must specify a non-zero amount. Sell offers that specify XRP can be 'free' (that is, the Amount field can be equal to `"0"`). |
| `Destination`       | String - [Address][] | AccountID         | No          | The account for which this offer is intended. If present, only that account can accept the offer. |
| `Expiration`        | Number               | UInt32            | No          | The time after which the offer is no longer active. The value is the number of seconds since the Ripple Epoch. |
| `NFTokenID`         | String - Hexadecimal | UInt256           | Yes         | The `NFTokenID` of the NFT referenced by this offer. |
| `NFTokenOfferNode`  | String - Hexadecimal | UInt64            | No          | Internal bookkeeping, indicating the page inside the token buy or sell offer directory, as appropriate, where this token is being tracked. This field allows the efficient deletion of offers. |
| `Owner`             | String - [Address][] | AccountID         | Yes         | The account that created and owns this offer. Only the current owner of an NFT can create an offer to sell an NFToken, but offers from past owners can remain in the ledger after the NFT has been transferred. Any account can create an offer to buy an NFT. |
| `OwnerNode`         | String - Hexadecimal | UInt64            | No          | Internal bookkeeping, indicating the page inside the owner directory where this token is being tracked. This field allows the efficient deletion of offers. |
| `PreviousTxnID`     | String - [Hash][]    | UInt256           | Yes         | Identifying hash of the transaction that most recently modified this object. |
| `PreviousTxnLgrSeq` | Number               | UInt32            | Yes         | Index of the ledger that contains the transaction that most recently modified this object. |


## {% $frontmatter.seo.title %} Flags

{% $frontmatter.seo.title %} entries can have the following flags combined in the `Flags` field:

| Flag Name        | Hex Value    | Decimal Value | Description |
|------------------|--------------|---------------|-------------|
| `lsfSellNFToken` | `0x00000001` | 1             | If enabled, the offer is a sell offer. Otherwise, the offer is a buy offer. |


## NFTokenOffer Transactions

Unlike [Offers for fungible tokens](../../../../concepts/tokens/decentralized-exchange/offers.md), a `NFTokenOffer` is not stored in an order book and is never automatically matched or executed. A buyer must _explicitly_ choose to accept an `NFTokenOffer` that offers to sell a `NFToken`. Similarly, a seller must _explicitly_ choose to accept a specific `NFTokenOffer` that offers to buy a `NFToken` object that they own.

The transactions for `NFToken` trading are:

- [NFTokenCreateOffer][]
- [NFTokenCancelOffer][]
- [NFTokenAcceptOffer][]


## Locating NFTokenOffer entries

Each unique NFT has up to two [directories](directorynode.md): one contains offers to buy the token and the other contains offers to sell the token. (These two directories are created as necessary and deleted if empty.) Marketplaces or other client applications can use these directories to find and display offers to trade `NFToken` objects to users or even automatically match them and accept them.


### NFTokenOffer Reserve

{% code-page-name /%} entries each count as one item towards the owner reserve of the account placing the offer, as long as the entry is in the ledger. Accepting or canceling the offer frees up the reserve.


### NFTokenOffer ID Format

The unique ID (`NFTokenOfferID`) of a `NFTokenOffer` object is the result of the following values concatenated in order:

* The `NFTokenOffer` space key, `0x0037`;
* The `AccountID` of the account placing the offer; and
* The `Sequence` (or `Ticket`) of the `NFTokenCreateOffer` transaction that created the `NFTokenOffer`.

{% raw-partial file="/docs/_snippets/common-links.md" /%}
