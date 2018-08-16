# types

Custom type definitions used by the Radar API and SDK

[![npm version](https://badge.fury.io/js/%40radarrelay%2Ftypes.svg)](https://badge.fury.io/js/%40radarrelay%2Ftypes)

## Enums

### UserOrderType
The order type specified by the user.

```javascript
enum UserOrderType {
  BUY = 'BUY',
  SELL = 'SELL'
}
```

### RadarOrderType
The order type used by Radar on the backend.

```javascript
enum RadarOrderType {
  BID = 'BID',
  ASK = 'ASK'
}
```

### RadarOrderState
Various order states used by Radar.

```javascript
enum RadarOrderState {
  OPEN = 'OPEN',
  FILLED = 'FILLED',
  CANCELED = 'CANCELED',
  EXPIRED = 'EXPIRED',
  UNFUNDED = 'UNFUNDED'
}
```

### WebsocketAction
The order book WebSocket action.

```javascript
enum WebsocketAction {
  FILL = 'FILL',
  NEW = 'NEW',
  CANCEL = 'CANCEL',
  REMOVE = 'REMOVE'
}
```

### WebsocketRequestTopic
The WebSocket request topic.

```javascript
enum WebsocketRequestTopic {
  BOOK = 'BOOK',
  TICKER = 'TICKER',
  CANDLE = 'CANDLE'
}
```

### WebsocketRequestType
The type of WebSocket request.

```javascript
enum WebsocketRequestType {
  SUBSCRIBE = 'SUBSCRIBE',
  UNSUBSCRIBE = 'UNSUBSCRIBE'
}
```

## Interfaces

### RadarToken
Information specific to a single token.

```javascript
interface RadarToken {
  address: string;
  symbol: string;
  name: string;
  decimals: number;
  zeroex_official: boolean;
  active: boolean;
  createdDate: string;
  quote: boolean;
}
```

### RadarTicker
Top-Level information about the most recent trade and best bid/ask for a given market.

```javascript
interface RadarTicker {
  transactionHash: string; // Last trade tx hash
  price: BigNumber; // Last trade price
  size: BigNumber; // Last trade size (in quote)
  timestamp: BigNumber; // Timestamp of last trade
  bestBid: BigNumber; // Best bid on the book
  bestAsk: BigNumber; // Best ask on the book
  spreadPercentage: BigNumber; // The bid-ask spread percentage
}
```

### RadarStats
Order book depth information and 24 hour volume statistics for a given market.

```javascript
interface RadarStats {
  numBidsWithinRange: number; // Number of bids within a defined range (Example: Within 20% of the best bid)
  numAsksWithinRange: number; // Number of asks within a defined range (Example: Within 20% of the best ask)
  baseTokenAvailable: BigNumber; // Amount of base token available on the book
  quoteTokenAvailable: BigNumber; // Amount of quote token available on the book
  volume24Hour: BigNumber; // 24 hour volume
  percentChange24Hour: BigNumber; // 24 hour price change percentage
}
```

### RadarHistory
Historial information for a given market.

```javascript
interface RadarHistory {
  price24Hour: BigNumber[]; // The 24 hour price history
}
```

### RadarMarketBase
General Market Information

```javascript
interface RadarMarketBase {
  displayName: string; // Example: ZRX/WETH
  baseTokenAddress: string;
  quoteTokenAddress: string;
  baseTokenDecimals: number;
  quoteTokenDecimals: number;
  quoteIncrement: number; // Maximum precision allowed for the market. Example: 7 (decimal places)
  minOrderSize: BigNumber; // Calculated min base token size based on last trade price
  maxOrderSize: BigNumber; // Calculated max base token size
  score: number; // A score used to rank most active markets
}
```

### RadarMarket
Market information for a base/quote token pair.

```javascript
interface RadarMarket {
  id: string; // Example: ZRX-WETH. (Mandatory)
  ticker?: RadarTicker; // (Optional)
  stats?: RadarStats; // (Optional)
  history?: RadarHistory; // (Optional)
}
```

### Order
ZeroEx Order

```javascript
interface Order {
  senderAddress: string;
  makerAddress: string;
  takerAddress: string;
  makerFee: BigNumber;
  takerFee: BigNumber;
  makerAssetAmount: BigNumber;
  takerAssetAmount: BigNumber;
  makerAssetData: string;
  takerAssetData: string;
  salt: BigNumber;
  exchangeAddress: string;
  feeRecipientAddress: string;
  expirationTimeSeconds: BigNumber;
}
```

### SignedOrder
ZeroEx Signed Order

```javascript
interface SignedOrder extends Order {
  signature: string;
}
```

### RadarSignedOrder
ZRX Signed Order with included order state.

```javascript
interface RadarSignedOrder {
  orderHash: string;
  type: RadarOrderType;
  state: RadarOrderState;
  baseTokenAddress: string;
  quoteTokenAddress: string;
  remainingBaseTokenAmount: BigNumber, // converted amount
  remainingQuoteTokenAmount: BigNumber // converted amount
  price: BigNumber;
  createdDate: BigNumber; // unix
  signedOrder: SignedOrder;
}
```

### RadarLimitOrderRequest
A request for an unsigned order at the specified quantity and price, which can then we signed and POSTed back.

```javascript
interface RadarLimitOrder {
  type: UserOrderType;
  quantity: BigNumber;
  price: BigNumber;
  expiration: BigNumber;
 }
```

### UnsignedOrder
An unsigned order at the specified quantity and price.

```javascript
interface UnsignedOrder {
  maker: 'SET';
  taker: string;
  makerFee: BigNumber;
  takerFee: BigNumber;
  makerTokenAmount: BigNumber;
  takerTokenAmount: BigNumber;
  makerTokenAddress: string;
  takerTokenAddress: string;
  salt: BigNumber;
  exchangeContractAddress: 'SET';
  feeRecipient: string;
  expirationUnixTimestampSec: 'SET';
}
```

### RadarMarketOrderRequest
A request for fillable orders, up to the specified quantity, at the best price.

```javascript
interface RadarMarketOrder {
  type: UserOrderType;
  quantity : BigNumber;
 }
```

### RadarMarketOrderResponse
A response with price information and fillable orders at the best price.

```javascript
interface RadarMarketOrderResponse {
   averagePrice: BigNumber;
   bestPrice: BigNumber;
   worstPrice: BigNumber;
   spread: BigNumber;
   orders: SignedOrder[];
}
```

### RadarOrderFeeResponse
Fee information for a given market.

```javascript
interface RadarOrderFeeResponse {
  makerFee: BigNumber;
  takerFee: BigNumber;
  feeRecipient: string;
  gasEstimate?: BigNumber;
 }
```

### RadarBook
The orderbook for a given market.

```javascript
interface RadarBook {
  baseTokenAddress: string;
  quoteTokenAddress: string;
  bids: RadarSignedOrder[];
  asks: RadarSignedOrder[];
}
```

### Radar Candle
Open-high-low-close chart data.

```javascript
interface Ohlc {
  open: BigNumber;
  high: BigNumber;
  low: BigNumber;
  close: BigNumber;
}

interface RadarCandle extends Ohlc {
  startBlock: number;
  startBlockTimestamp: number;
  endBlock: number; // the last block included in this candle (inclusive)
  endBlockTimestamp: number;
  baseTokenAddress: string;
  baseTokenVolume: BigNumber;
  quoteTokenAddress: string;
  quoteTokenVolume: BigNumber;
}
```

## Radar Websocket Events
Radar Events utilized by the Websocket Endpoint.

### RadarEvent
```javascript
interface RadarEvent {
  baseTokenAddress: string;
  quoteTokenAddress: string;
  order: RadarSignedOrder;
}
```

### OnChainEvent
An on-chain event (transaction).

```javascript
interface OnChainEvent {
  transactionHash: string;
}
```

### Order Events
```javascript
interface RadarNewOrder extends RadarEvent { }

interface RadarRemoveOrder extends RadarEvent {
  reason: string;
}

interface RadarCancelOrder extends OnChainEvent {
  orderType: RadarOrderType;
  orderHash: string;
}
```

### WebsocketEvent
```javascript
interface WebsocketEvent {
  action: WebsocketAction;
  event: RadarFill | RadarNewOrder | RadarCancelOrder | RadarRemoveOrder;
}
```

### RadarFill
```javascript
interface RadarFill extends RadarEvent, OnChainEvent {
  blockNumber: number;
  maker: string;
  taker: string;
  feeRecipient: string;
  paidMakerFee: BigNumber; // converted
  paidTakerFee: BigNumber; // converted
  filledBaseTokenAmount: BigNumber; // converted
  filledQuoteTokenAmount: BigNumber; // converted
  orderHash: string;
  timestamp: number;
}
```

## Radar Websocket Request Types
Radar Request Types utilized by the Websocket Endpoint.

### RadarWebsocketRequest
```javascript
interface RadarWebsocketRequest {
  type: WebsocketRequestType;
  requestId?: number;
}
```

### RadarSubscribeRequest
```javascript
interface RadarSubscribeRequest extends RadarWebsocketRequest {
  type: WebsocketRequestType.SUBSCRIBE;
  topic: WebsocketRequestTopic;
  market: string;
}
```

### RadarUnsubscribeRequest
```javascript
interface RadarUnsubscribeRequest extends RadarWebsocketRequest {
  type: WebsocketRequestType.UNSUBSCRIBE;
  topic: WebsocketRequestTopic;
  market: string;
}
```

## Radar Websocket Response Types
Radar Response Types utilized by the Websocket Endpoint.

### RadarWebsocketResponse
```javascript
interface RadarWebsocketResponse {
  type: WebsocketRequestType | 'ERROR';
  requestId?: number;
}
```

## Contributing

Please read [CONTRIBUTING.md](https://github.com/RadarRelay/types/blob/master/CONTRIBUTING.md) for details on our code of conduct and the process for submitting pull requests.

## License

This project is licensed under the MIT License - see the [LICENSE.md](https://github.com/RadarRelay/types/blob/master/LICENSE.md) file for details.
