# Nice Wallet

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

This is a blog post about a hypothetical Bitcoin wallet architecture. It is not
done.

The primary benefits of the architecture are:

- Wallet frontends communicate with wallet backends using a standard protocol.
  Backends can be non-custodial, custodial, or somewhere in between.

- Wallet backends do not know how many users they have, or what their balances
  are.

## Notes, To-Do, and Junk

### Prior Art

- https://en.bitcoin.it/wiki/Wallet_protocol
- https://en.wikipedia.org/wiki/Blind_signature
- https://en.wikipedia.org/wiki/Ecash
- https://github.com/btcontract/hosted-channels-rfc
- https://lightweight.money
- https://nopara73.medium.com/chaumian-e-cash-for-custodial-bitcoin-wallets-and-services-to-scale-bitcoin-8977d9a03064

## Disclaimer and Prolegomenon

I know enough about cryptography to be dangerous, i.e. I have heard of private
keys and public keys, understand that curves are somehow involved.

I have also heard of eCash and blind signatures, and think that they provide
the necessary properties to use as the basis for a wallet architecture with
very nice properties.

However, I don't actually know the nitty-gritty details, so I might be totally
wrong.

As an invocation of Cunningham's Law, here is my current understanding, that I
hope someone will correct or confirm, of two operations that eCash-like systems
can provide. For the sake of concreteness, I use Bitcoin as the hypothetical
currency, and interactions between a user and a custodian as the scenario.

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
