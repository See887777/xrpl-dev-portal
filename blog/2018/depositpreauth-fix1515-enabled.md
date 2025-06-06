---
date: "2018-10-09"
template: '../../@theme/templates/blogpost'
category: 2018
labels:
    - Amendments
markdown:
    editPage:
        hide: true
---
# DepositPreauth is Now Available

The [DepositPreauth](https://xrpcharts.ripple.com/#/transactions/AD27403CB840AE67CADDB084BC54249D7BD1B403885819B39CCF723DC671F927) and [fix1515](https://xrpcharts.ripple.com/#/transactions/6DF60D9EC8AF3C39B173840F4D1C57F8A8AB51E7C6571483B4A5F1AA0A9AAEBF) amendments, [added in `rippled` v1.1.0](/blog/2018/rippled-1.1.0.md), became enabled on the XRP Ledger today, 2018-10-09.

## Action Required

- If you operate a `rippled` server, you should upgrade to version 1.1.0 (or higher) immediately.

For instructions on upgrading `rippled` on supported platforms, see [Updating `rippled` on supported platforms](/docs/infrastructure/installation/update-rippled-automatically-on-linux).

## Impact of Not Upgrading

If you operate a `rippled` server on a version older than 1.1.0, then your server is now amendment blocked, meaning that your server:

* Cannot determine the validity of a ledger
* Cannot submit or process transactions
* Does not participate in the consensus process
* Does not vote on future amendments
* Could rely on potentially invalid data

## DepositPreauth Summary

The DepositPreauth amendment provides users of [deposit authorization](/docs/concepts/accounts/depositauth) with a way to preauthorize specific senders so those senders are allowed to send payments directly.

The amendment adds:

- A new transaction type, DepositPreauth, for adding or removing preauthorization.
- A DepositPreauth ledger object type for tracking preauthorizations from one account to another.
- A JSON-RPC command, `deposit_authorized`, to query whether an account is authorized to send payments directly to another.

This amendment also changes the behavior of cross-currency Payments from an account to itself when that account requires deposit authorization. Without this amendment, those payments always fail with the code tecNO_PERMISSION. With this amendment, those payments succeed as they would with Deposit Authorization disabled.


## fix1515 Summary

The fix1515 amendment changes how Payment transactions consume offers to remove a minor difference in how payment processing and offer processing consume liquidity.

Without the amendment, payment processing gives up on using a particular path if the transaction would consume over 2000 offers from the same order book at the same exchange rate. In this case, the payment does not use the liquidity from those offers, and does not consider that order book's remaining liquidity when attempting to complete the payment.

With this amendment, if any transaction processes over 1000 offers at the same exchange rate, the transaction consumes the liquidity from the first 1000 offers, then does not consider that order book's remaining liquidity when attempting to complete the payment.

In both cases, transaction processing can still complete by using liquidity from other paths or exchange rates.


## Learn More
Related documentation is available in the [XRP Ledger Dev Portal](/docs/), including detailed example API calls and web tools for API testing.

Other resources:

* The Ripple Forum (_Disabled._ Formerly `forum.ripple.com`)
* The Ripple Dev Blog _(Replaced with [xrpl.org/blog](https://xrpl.org/blog/))_
* Ripple Technical Services: <support@ripple.com>
* XRP Chat _(Shut down. Formerly `www.xrpchat.com`)_
