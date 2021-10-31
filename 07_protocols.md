### 198. WETH

`WETH` stands for **Wrapped Ether**.

For protocols that work with `ERC20` tokens but also need to handle Ether, `WETH` contracts allow for:
- converting Ether to its `ERC20` equivalent `WETH` (wrapping)
- converting `WETH` back to Ether (unwrapping)

WETH can be created by sending Ether to a WETH smart contract,  where the Ether is stored, and in turn receiving the WETH `ERC20` token at a 1:1 ratio.

This WETH can be sent back to the same smart contract to be "unwrapped" and redeemed for the original Ether at a 1:1 ratio.

The most widely used WETH contract is WETH9, which holds more than 7 million Ether for now.

### 199. Uniswap V2

Uniswap is an automated liquidity protocol powered by a constant product formula and implemented in a system of non-upgradable smart contracts on the Ethereum blockchain.

The Automated Market Making (AMM) algorithm used by Uniswap is `x * y = k`, where `x` and `y` represent a token pair that allows one token to be exchanged for the other as long as the "constant product" formula is preserved, i.e. trades must not change the product `k` of a pair's reserve balances (x and y)

Core concepts:
1. **Pools**: Each Uniswap liquidity pool is a trading venue for a **pair** of `ERC20` tokens.
    - when a pool contract is created, its balances of each token are `0`
    - in order for the pool to begin facilitating trades, someone must seed it with an initial deposit of each token
    - the first liquidity provider sets the initial price of the pool and are incentivized to deposit equal value of both tokens into the pool
    - whenever liquidity is deposited into a pool, unique tokens known as liquidity tokens are minted and sent to the provider's address. These tokens represent a given liquidity provider's contribution to a pool
2. **Swaps**: allows one to trade one `ERC20` token for another, where one token is withdrawn (purchased) and a proportional amount of the other deposited (sold), in order to maintain the constant `x * y = k`
3. **Flash Swaps**: allows one to withdraw up to the full reserves of any `ERC20` token on Uniswap and execute arbitrary logic at no upfront cost, provided that by the end of the transaction they either:
     1. pay for the withdrawn `ERC20` tokens with the corresponding pair tokens
     2. return the withdrawn `ERC20` tokens along with a small fee
4. **Oracles**: enables developers to build highly decentralized and manipulation resistant on-chain price Oracles
    - a price oracle is any tool used to view price information about a given asset
    - every pair measures (but does not store) the market price at the beginning of each block, before any trades take place (price at end of previous block is added to a single cumulative price variable weighted by the amount of time this price existed)
    - this variable can be used by external contracts to track accurate time-weighted average prices (TWAPs) accross any time interval

### 200. Uniswap V3

1. **Concentrated Liquidity**: giving individual LPs granular control over what price ranges their capital is allocated to.
    - Individual positions are aggregated together into a single pool, forming one combined curve for users to trade against.
2. **Multiple Fee Tiers**: allowing LPs to be appropriately compensated for taking on varying degrees of risk
3. **V3 oracles**: provide time-weighted average prices (TWAPs) on demand for any period within the last ~9 days
    - this removes the need to integrators to checkpoint historical values.

### 201. Chainlink Oracles & Price Feeds

Chainlink price feeds provide aggregated data (via its `AggregatorV3Interface` contract interface) from various high quality data providers, fed on-chain by decentralized oracles on the Chainlink Network.

To get price data into smart contract for an asset that isn't covered by an existing price feed, such as the price of a particular stock, one can customize Chainlink oracles to call any external API.
