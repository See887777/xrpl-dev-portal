---
date: "2017-03-27"
template: '../../@theme/templates/blogpost'
category: 2017
labels:
    - Amendments
markdown:
    editPage:
        hide: true
---
# Escrow, PayChan, and fix1368 Will Be Available in 3 Days

A majority of `rippled` validators voted to enable the [Escrow](/resources/known-amendments.md#escrow), [PayChan](/resources/known-amendments.md#paychan), and [fix1368](/resources/known-amendments.md#fix1368) Amendments, which are scheduled to become enabled on the network on Thursday, 2017-03-30.

* **Escrow** (previously called SusPay), designed for low volume, high value transactions, permits users to cryptographically escrow XRP on RCL with an expiration date using a [hashlock crypto-condition](https://interledgerjs.github.io/five-bells-condition/jsdoc/).

* **PayChan**, designed for high volume, low value transactions, flowing in a single direction. The scalability of these transactions is not limited by the Ripple Consensus ledger, and they do not incur the risks typically associated with delayed settlement.

* **fix1368** fixes a minor bug in transaction processing that causes some payments to fail when they should be valid.


## Action Required

**If you operate a `rippled` server**, then you should upgrade to version 0.60.0 by Thursday, 2017-03-30, for service continuity.

## Impact of Not Upgrading

If you operate a `rippled` server but don’t upgrade to version 0.60.0 by Thursday, 2017-03-30, when **Escrow, PayChan & fix1368** are expected to be activated via Amendment, then your server will become [amendment blocked](/docs/concepts/networks-and-servers/amendments#amendment-blocked-servers), meaning that your server:

* Cannot determine the validity of a ledger
* Cannot submit or process transactions
* Does not participate in the consensus process
* Does not vote on future amendments
* Could rely on potentially invalid data

If the **Escrow, PayChan & fix1368** amendments do not get approved, then your server will not become amendment blocked and should continue to operate.

For instructions on updating `rippled` on supported platforms, see [Updating `rippled` on supported platforms](/docs/infrastructure/installation/update-rippled-automatically-on-linux).

## Learn, ask questions, and discuss
Related documentation is available in the Ripple Developer Portal, including detailed example API calls and web tools for API testing.

Other resources:

* The Ripple Forum (_Disabled._ Formerly `forum.ripple.com`)
* The Ripple Dev Blog _(Replaced with [xrpl.org/blog](https://xrpl.org/blog/))_
* Ripple Technical Services: <support@ripple.com>
* XRP Chat _(Shut down. Formerly `www.xrpchat.com`)_
