# Lecture 2 - May 15, 2018

## Jargon
- **Long position:** committed to buy something in the future.
  - Your going to fix the price/quantity that your going to pay in the future, in order to recieve a good.
  - **Economic Reason:** Speculation that the price is going to go up.
  - **Bullish**
- **Short position:** commitment to sell in the future, at a set price
  - economic view: think the price is going to go down
  - **Bearish**

## Forwards

Locks in a price today for a future transaction. Contract now, transaction later.
- binding agreement between a buyer and a seller to trade some commodity at a fixed price at a later date.
- **Forward Price**: set in a way such that the value is fair for the buyer and seller.
  - No money changes hands when the contract is established.
- **Delivery Date**
- contracts are created on demand, **zero net supply market**
  - one person's loss is another gain.

### Example: Forward Contract Lifecycle
- Ms. Long will buy gold at a fixed price mid-year (currently jan 1)
- OTC === Over the counter
- 50 ounces, delivery price of $1000 / ounce
  - This price is set by market forces
  - We will learn how to set this price in the future.
- Trade date June 30

#### Possibilities

When the contract is established, the contract has no value (no money has changed hands)

- The trade goes through fine, at the specified time and price.
- Exiting the market early
  - Market could change
  - close out their positions via a mutual agreement
  - One of the counterparties could sell their contract to a third-party, who will take on their position. Will need to calculate a new valuation.

#### On the delivery date
- Physical Delivery
  - exchanging the asset for cash. End up with the asset
  - Pay the amount and recieve the asset
  - recieve the 50 ounces of gold, for $1000 / ounce
- Cash settlement
  - Just a transfer of cash from one partner to another. End up with cash.
    - Avoid the inconvenice of holding the asset.
  - take the difference from the current market value
  - If lower than the spot price, the seller will pay the different

Spot higher than future price
- Good for the long holder
Spot lower than future
- Good for the short holder, bad for long

#### Future Price (Price at Maturity) Notation
![latex-b61dc3b2-d267-40d0-a024-d07b6789c361](data/lecture2/latex-b61dc3b2-d267-40d0-a024-d07b6789c361.png)

For Long: ![latex-9dde6911-2ac5-4aa9-bd17-101b08d2ff6c](data/lecture2/latex-9dde6911-2ac5-4aa9-bd17-101b08d2ff6c.png)
For Short: ![latex-aa79a4e4-398f-4d58-9c9e-465d00adbe2d](data/lecture2/latex-aa79a4e4-398f-4d58-9c9e-465d00adbe2d.png)

If ![latex-7a536a99-8a30-4cc7-80b1-95517fe91620](data/lecture2/latex-7a536a99-8a30-4cc7-80b1-95517fe91620.png) (for long):
- The long holder recieves the payout
- Note that, as this is a zero-sum game, the losses of one side are the gains of another.

Simple Linear relationship
- Short is just inverted version of long

#### Back to Example
- Suppose ![latex-f936d8cb-9baf-4c25-b34b-ac4e20717514](data/lecture2/latex-f936d8cb-9baf-4c25-b34b-ac4e20717514.png) at the time of maturity
  - Then ![latex-28fcc144-f5fb-428f-98e6-dfdfdc0ab5e2](data/lecture2/latex-28fcc144-f5fb-428f-98e6-dfdfdc0ab5e2.png) gains for the long holder
  - Then short holder has a loss of $5
- Suppose ![latex-acaafdab-011e-450a-a322-97b601df4b99](data/lecture2/latex-acaafdab-011e-450a-a322-97b601df4b99.png)
  - Then ![latex-b28770b4-8269-4099-b4b5-9ce7df7ef8bc](data/lecture2/latex-b28770b4-8269-4099-b4b5-9ce7df7ef8bc.png)
  - Loss for long holder, gain for short (they can sell an asset worth $990 for $1000)

Goal is to integrate these with your entire portfolio, hope that losses are cancelled out by gains.

If you we're to take a position which would be riskier?
- **short position is riskier**: as your losses are unbounded. Typically to take a short position, you have to prove that you have the money to cover your potential losses.
- long position losses are bounded by the contract/future price (ex. can loose up to $1000)

## Futures Trading
Differences
- Go through an exchange
- can only trade in multiples of the amounts specified by the contracts
- specific dates for delievery


Continuing from the previous example... Do the same transaction with futures
- Need to go to an exchange (can't do OTC)
- Futures contracts are standardized, you can only trade in amounts that are specified by the contract.
- Probably can't trade for June 30, there are specific dates available for delivery

### Order Placement
A futures trade must open a margin account with an FCM. In order to trade, you have to be allowed to.
- Margin account: must have margin / security deposits
- FCM: Futures Commission Merchant: one-stop service for futures trading
  - broker
  - sends the trade to the exchange

#### Different Order Types
- Market Order
  - Ask price, Bid price
  - BUY market order, use the ask price
  - SELL market order, use the bid price
  - the break between the ask and bid prices is how the broker makes money
    - buy for more, sell for less
- Limit order
  - You name the price, wait until the price reaches
- conditionals
  - wait until the price hits some level, make an order

### Terms
- Minimum Price Fluctuation: Largest delta between trades.
- Open Interest: The number of outstanding contracts in the market
  - Stays open until closed, then decrement.
- Volume Traded: Number of trades per day.
- Settlement Price: high correlation between all
  - this is because the common underlying asset is gold.
  - some contracts dissappear because they disappear from the market

## Exercise 1

### Question 1
- Time series of Volume traded, starts low, peaks as the date of maturity approaches. There seems to be a positive correlation with open interest
- Most traders will enter in the up-front contract, roll over your position to the next date as maturity approaches.
- Lost of liquidity on the up-front contract. Means a lower spread between the ASK and BID prices.

### Question 2
- As maturity approaches, the settlement price increases.
- Positive term structure of gold, we'll look at how prices are formed in commodities in a few weeks.

### Question 3
- The Differences should be close if we did a good job.
- The difference between the settlement and spot price should be close.
- Future price is an estimation of the spot price in the future (at maturity).

## Quick Quiz 1

Answer: D
- Volume is 2000
- out of 2000 buyers, 1400 are closing out their positions. 2000 sellers, 1200 closing out their positions.
- When you close out your position, the only way to erase the contract is to remove the buyer and the seller.
  - 1200 contracts erased from the market -> 1400 buyers, 1200 sellers -> 1200 contracts
- 800 new sellers, 600 new buyers. 200 new sellers which need to be assigned contracts.
- 600 contracts are erased

## Role of a Clearning House (futures)
- You will never be paired with a seller
- You are trading directly with the trading house
  - Clearing house is the seller for buyers, and the buyer for sellers.

### Daily settlement: Marking-To-Market
- Don't need to wait until maturity for profits and losses
- everyday determine the profits and losses.
- resets the delivery price at maturity for outstanding futures contracts.

#### Example
- Long buys 1000 ounces of gold from short in July for $1001 per ounce
- next days settlement price is 1004, in order to be fair, long will be comphensated for changing the terms of the contract ($3).
- If the settlement price decreases, they will take money (the difference) from long's account.
- You will trade at the spot price, but the accounting of losses and gains happen every day.

### Margin
- cash or marketable security that is deposited by the investor with their broker
- balance adjusted to reflect the daily settlement
- minimizes the likelihood of default
- experience gains, your margin increases.
- traders provide initial margin. When the balance falls below the maintenance level, they must provide variation margin, to bring the margin back up to the initial level.

## Exercise 2

### Question 1
- notice that the price drops by 10k over the course
- Long position will make lots of margin calls
- short position will be the better position

## Bilateral Clearning vs. Central Clearing
- clearing house manage their position with the clearing house
  - Everyone puts up some collateral
  - more and more markets are moving to this model.
  - reduces systemic risk, collapse of the system.
- bilateral clearing: everyone is trading with everyone else
  - ![latex-91c736bc-1ab1-4ee3-900f-af1563b9527d](data/lecture2/latex-91c736bc-1ab1-4ee3-900f-af1563b9527d.png) possible connections
  - have a fault, the entire system collapses

## Types of Orders
- The way that the trade is reported will effect the accounting principals used, therefore effecting the taxes paid.
- speculation: year where you obtain the gain is where you pay the taxes.

### Quick quiz 2

