# Loopring Smart Contracts

The loopring smart contracts are a set of ethereum contracts that implement the loopring protocol. This document describes the functionnalities they provide and is structured as follows:

- [Management of orders](protocol.md#management-of-orders)
	- Anatomy of an order
	- Full or partial cancellation
	- Fill and cancellation amounts tracking
- [Verification of miner supplied data](protocol.md#verification-of-miner-supplied-data)
- [Computation of the current state of each order](protocol.md#computation-of-the-current-state-of-orders)
- Settlement of order rings
- Emission of events (`OrderFilled`, `OrderCancelled`, `RingMined`)

The code is open source and available on [github](https://github.com/Loopring/protocol).

The Loopring Smart Contracts will be refered as `LSC` in this document. You can read more about the calculations and formulas used in this page from the [whitepaper](https://github.com/Loopring/whitepaper/raw/master/en_whitepaper.pdf) and supersimmetry's {[da447m@yahoo.com](mailto:da447m@yahoo.com)} [Remarks on Loopring](../pdf/supersimmetry-loopring-remark.pdf). Please be noted that in the current protocol implementation, the pricing model is the same as in our whitepaper and the above document, but the fee model is different.

## Management of orders
### Anatomy of an order
To understand what the LSC expect to receive, we must take a look at the definition of an order in the loopring protocol.

>An order is a pack of data that describes the intent of the user on the market. To ensure the origin of the order, it is `signed against the hash of its parameters` with the user's private key. The signature is sent along the order on the network. This requires the order to stay `immutable` during its whole lifetime to verifiy the sender's address.<br/>
`Signature = ECDSA(SHA3(order_params))`

Even if the order never changes, the LSC has the possibility to [compute its current state](protocol.md#computation-of-the-current-state-of-orders).

Relevant variables of an order's parameters:

| Data    |     Comment |
|---------|------- |
| tokenS  | Token to sell |
| tokenB  | Token to buy  |
| amountS | Amount of tokenS to sell |
| amountB | Amount of tokenB to buy  |
| buyNoMoreThanAmountB | see below |
| ttl | (time to live) Seconds after wich the order will expire |
| lrcFee | Max amount of LRC to pay to the miner |

The exchange rate `r` of the order is determined using the following formula `r = amountS/amountB`. When a miner does the ring-matching there is a possibility that he finds you a better rate that gets you more `tokenB` than the `amountB` you specified. But, if the `buyNoMoreThanAmountB` flag is set, the LSC will make sure that you still get exactly `amountB` of `tokenB`.

> **Example**: with `amountS = 10` and `amountB = 2`, `r = 10/2 = 5`. This means that you are willing to sell `5 tokenS for each tokenB`. The miner does the ring-matching and `finds you a rate of 4`, topping the amount he could get you to `2.5 tokensB instead of 2`. You only wanted 2 tokensB and set the `buyNoMoreThanAmountB flag to true`. The LSC takes that into consideration and still makes the transaction at a rate of 4 and you ended up selling `4 tokenS for each tokenB`, effectively `saving 2 tokenS`. Keep in mind that this does not take into account the miner fees.

### Full or partial cancellation
An order can be fully or partially cancelled by its sender.
...

### Fill and cancellation amount tracking
The LSC keeps track of cancellation and fill amounts, it is also able to get the balance of the sender's account.
...

### Cancellation of an order

## Verification of miner supplied data
### Order Rings