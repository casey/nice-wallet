# Nice Wallet

## Table of Contents

<!--ts-->
* [Nice Wallet](#nice-wallet)
   * [Notes, To-Do, and Junk](#notes-to-do-and-junk)
      * [Prior Art](#prior-art)
      * [Notes](#notes)
   * [Disclaimer and Prolegomenon](#disclaimer-and-prolegomenon)
      * [Deposits](#deposits)
      * [Withdrawals and Spends](#withdrawals-and-spends)
      * [Discussion](#discussion)
<!--te-->

## Introduction

This document introduces a hypothetical Bitcoin wallet architecture.

The two primary features of the architecture are:

- Wallet frontends communicate with wallet backends using a standard protocol.

- Wallet backends track user balances with an eCash-like blinded signature
  scheme.

In tandem, these features provide numerous benefits, including:

- Backends can be non-custodial, custodial, or somewhere in between.

- Wallet backends do not know how many users they have, or what their balances
  are.

- Many users may use a single wallet backend, making backends robust
  against external surveillance. This is especially true when off-chain
  payments are used, for example via the Lightning Network.

- Wallet frontends can be developed independently of wallet backends, increasing
  competition between wallet frontend developers.

- Launching a backend provider service becomes straightforward, as only
  standard infrastructure components need to be deployed and managed.

- Users may switch backend providers, without changing their wallet frontend
  and user interface, increasing competition between backend providers.

- Users may switch from a custodial backend provider to a self-hosted,
  non-custodial backend, improving user sovereignty.

## eCash

[eCash](https://en.wikipedia.org/wiki/Ecash) was an anonymous cryptographic
electronic cash system invent in 1983 by David Chaum which operated as a
micropayment system at a US bank between 1995 and 1998.

Although centralized, eCash had extremely attractive privacy properties, even
when compared with more recently developed privacy-preserving cryptocurrencies,
and, due to its centralization, eCash transactions can be performed quickly
enough to be used at scale.

Additionally, cryptographic techniques developed in the intervening years can
enhance an eCash-like system's properties

### Deposits

1. User tells the custodian that they would like to make a deposit.
2. Custodian gives the user a bitcoin address `A` that they control.
3. User sends `N` BTC to `A`.
4. Custodian gives user a token `T`, which commits to `N`, and which is
   unforgeable by anyone who is not the Custodian.

### Withdrawals and Spends

1. User takes `T` from an earlier deposit of `D` coins and blinds it to produce
   `Tb`.
2. User presents `Tb`, bitcoin withdrawal address `W`, and amount `N` and
   requests that the custodian transfer `N` coins to `W`.
3. Custodian checks that `Tb` is genuine, and that it commits to at least `N`
   coins. However, the custodian cannot tell what `D` is, or that `Tb` was
   derived from `T`.
4. Custodian transfers `N` bitcoin to to `W`, and remembers that they have seen
   `Tb`, so that it cannot be reused in the future.
5. Custodian provides the user with a new token, `S`, that commits to `N - D`.

### Discussion

In addition to the custodian not knowing user balances, if the users don't leak
information through another channel (like their network identity, avoided if
they connect over a network like Tor) the custodian can't tell how many users
there are. Every time a user shows up with a token, the custodian can tell that
the token is valid and that it commits to at least as many coins as the user is
requesting to withdraw, but can't link it back to a previously issued token.

Additionally, if users deposit and withdraw over the Lightning Network, privacy
is probably even better. Users can perform routing-client side, so the
custodian doesn't know the source or destination of funds, and can't make
inferences based on on-chain addresses.

### Prior Art and References

- [Bitcoin Wiki: Wallet Protocol](https://en.bitcoin.it/wiki/Wallet_protocol)
- [Wikipedia: Blind Signature](https://en.wikipedia.org/wiki/Blind_signature)
- [Wikipedia: eCash](https://en.wikipedia.org/wiki/Ecash)
- [Hosted Channels RFC](https://github.com/btcontract/hosted-channels-rfc)
- [Danake](https://lightweight.money)
- [Chaumian eCash for Custodial Bitcoin Wallets](https://nopara73.medium.com/chaumian-e-cash-for-custodial-bitcoin-wallets-and-services-to-scale-bitcoin-8977d9a03064)
