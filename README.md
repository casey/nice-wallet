# Flex Wallet

<!--ts-->
* [Flex Wallet](#flex-wallet)
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

### Notes

- Do it in rust, so it's fast, secure, and can be used by mobile wallets
- abstract over providers with API (can move from custodial to non custodial
  and back easily)
- AMP so you can keep funds with multiple providers
- Can leave provider easily by just draining wallet to another provider
- Can use Chaumian cash system that hides: user account balances from provider,
  don't know which user is doing which transaction
- By separating frontend from backend, anyone can run a custodial wallet
  backend (for friends, etc)
- Liquid integration for lower fees
- Client side wallet
- Everything over tor
- Transfer between providers
- Use multiple providers
- Why not hosted channels?
- transition from self-hosting
- accept reality that many will use custodial. give them smooth transition to non-custodial
- competitive landscape and interoperability of email is its killer feature
- self-custody of on-chain funds, custodial lightning wallet
- why self-custody of lighting is hard: channels close randomly, lose funds due to fees, bad behavior in high fee environments (pay high fees at best, lose funds  at worst)
- for privacy, only thing that you can do with on-chain funds is open channel and loop out?
- Can you deposit with on-chain funds to provider A, then move to another provider?
- liquid interop?
- exchanges can implement the protocol to give customers wallet.
- get users by offering free transfer to/from exchanges
- charge small fee on transactions
- open source the wallet (desktop, ios, android,  web)
- web version how?
- state backup how? encrypted backup with provider(s)?
- no selective censorship. must censor all users or no users.
- could support non-bitcoin cryptocurrencies. However, would greatly complicate wallet, and make it harder to support bitcoin well. Writing as if I weren't a bitcoin maximalist. Different coins have different technical properties, which make it challenging to support them all well. RBF, script types, etc.
- AMP to make amounts smaller and more uniform
- internal transfers?
- POP3/IMAP API is very important
- bitcoin wallet protocol
- application layer internet standard protocol
- providing this service increases own anonymity set. You can run your own backend, and encourage others to use it
- Makes lightning network surveillance very hard, since single channel is shared by many users
- future of lightning is mostly custodial, this is okay, similar to email. Not as good as all self custody sovereign individuals aiming down their up-pulled bootstraps with their 3d printed guns, but still good. Diversity of software, balance is portable, no lock-in, much competition, can always transition to self-custody seamlessly, businesses can self custody when it makes sense.
- unrealistic for normies to understand balance decrementing due to channel close/open fees, balance unavailable due to channel open/close, concept of inbound liquidity, need to stay online/trust watchtowers
- service provider going out of business notice
- white label wallet just binds to particular service provider
- Flex wallet architecture / protocol / client

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
