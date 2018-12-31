# MNX-UAT

Jump to following sections (websockets):
1. [REST Api stream](https://github.com/sangam09/MNX-UAT#rest-api-information): Rest API for orders.
2. [Market Data stream](https://github.com/sangam09/MNX-UAT#market-data-streams) : Real Time Market Data over websocket.
3. [User data stream](https://github.com/sangam09/MNX-UAT#user-data-stream) : Real time execution updates (orders & trades) <b>Implementation Pending</b>

## REST API Information
<ul>
	<li>Base API endpoint is: https://10.48.1.138</li>
	<li>API returns JSON</li>
	<li>All timestamps are in milliseconds and follow UTC Timezone</li>
</ul>

```
{
  "code": -1121,
  "msg": "Error message"
}
```

<ul>
	<li>For <code>GET</code> <code>POST</code> <code>DELETE</code> <code>PUT</code> endpoints, the parameters must be sent as <code>query string</code>. Parameters are case-insensitive and can be sent in any order.
</ul>


## Secure Endpoints:

Secure endpoints need the following:

1. API keys need to be passed into REST Api via <code>X-MNX-APIID</code> in header.
2. API keys and secretKeys are <strong>case sensitive</strong>.



| Security Type | Description |
| :--- | :---: | :---: | :--- |
| NONE | EndPoint doesn't requires any API key and secretKey |
| PARTIAL | EndPoint requires API key and but doesn't require secretKey |
| SECURE | EndPoint requires both API key and secretKey |

## General Endpoints:


### Exchange Info (NONE)
```
GET /api/v1/exchangeInfo
```

Retrieve JSON object containing the currency pairs, trading status, rateLimits etc.

<b>Weight:</b> 1

<b>Parameters:</b> None

<b>Response:</b>

```javascript
{
    "timezone": "GMT", 				//Timezone
    "serverTime": 1545118368886, 		//milliseconds
    "rateLimits": [
        {
            "weight_type": "REQUEST_NEW_ORDER",
            "value": 5
        },
        {
            "weight_type": "REQUEST_CXL_ORDER",
            "value": 3
        },
        {
            "weight_type": "ORDER_STATUS",
            "value": 1
        },
        {
            "weight_type": "QUERY_OPEN_ORDERS",
            "value": 3
        },
	...
    ],
    "symbols": [
        {
            "symbol": "XRP/ETH",
            "trading_status": "TRADING",
            "base_asset": "ETH",
            "quote_asset": "XRP",
            "precision": 8,
            "min_price": "0.00002100",
            "max_price": "0.00022100",
            "tick_size": "0.00000001",
            "min_qty": "1.00000000",
            "max_qty": "90000000.00000000"
        },
	...
        {
            "symbol": "XRP/BTC",
            "trading_status": "TRADING",
            "base_asset": "BTC",
            "quote_asset": "XRP",
            "precision": 8,
            "min_price": "0.00000943",
            "max_price": "0.00094290",
            "tick_size": "0.00000001",
            "min_qty": "1.00000000",
            "max_qty": "90000000.00000000"
        },
	...
    ]
}

```


### New Order (SECURE)
```
POST /api/v1/order (TODO: HMAC SHA256 Implementation)
```

Send New Order to market.

<b>Weight:</b> 1

<b>Parameters:</b>

| Parameters | Type | Mandatory (Y/N) | Description |
| :--- | :---: | :---: | :--- |
| symbol | STRING | Y | Currency pair to send order |
| side | STRING | Y | `BUY` / `SELL` |
| price | DECIMAL | N | Mandatory if `orderType` is `LIMIT` |
| quantity | DECIMAL | Y | |
| timeInForce | STRING | N | `GTC` (currently supported) / `GTD` (implementation required) |
| orderType | STRING | Y | `LIMIT` / `MARKET` |
| clientOrderId | STRING | Y | Unique ID generated from client system per new order request |
| recvWindow | LONG | N | |
| timestamp | LONG | Y | |
| signature | STRING | Y | Generated from `HMAC` operation using apiSecretKey (Currently not implemented. Can be used any string) |

<b>Response:</b>

```javascript
{
    "Symbol": "XRP/ETH",
    "ExchangeOrderId": 18, 		//Unique id generated by exchange
    "Price": "0.0002",
    "Qty": "0.0001",
    "Side": "BUY",
    "ClOrdId": "19",
    "EntryTime": 1545121589361 		//Server time in milliseconds the order is accepted
}
```


### Cancel Order (SECURE)
```
DELETE /api/v1/order (TODO: HMAC SHA256 Implementation)
```

Cancel working order from the market.

<b>Weight:</b> 1

<b>Parameters:</b>

| Parameters | Type | Mandatory (Y/N) | Description |
| :--- | :---: | :---: | :--- |
| symbol | STRING | Y | Currency pair to send order |
| clientOrderId | STRING | Y | Unique ID generated from client system per new cancel request |
| exchangeOrderId | STRING | Y | Unique ID assigned by exchange for first time the order was accepted |
| signature | STRING | Y | Generated from `HMAC` operation using apiSecretKey (Currently not implemented. Can be used any string) |

<b>Response:</b>

```javascript
{
    "Symbol": "XRP/ETH",
    "ExchangeOrderId": 18, 		//Unique id generated by exchange
    "ClOrdId": "20",
    "EntryTime": 1545121594521 		//Server time in milliseconds the order is accepted
}
```


### Order Query (SECURE)
```
GET /api/v1/order (TODO: HMAC SHA256 Implementation)
```

Query order status.

<b>Weight:</b> 1

<b>Parameters:</b>

| Parameters | Type | Mandatory (Y/N) | Description |
| :--- | :---: | :---: | :--- |
| symbol | STRING | Y | Currency pair to send order |
| exchangeOrderId | STRING | Y | Unique ID assigned by exchange for first time the order was accepted |
| signature | STRING | Y | Generated from `HMAC` operation using apiSecretKey (Currently not implemented. Can be used any string) |

<b>Response:</b>

```javascript

{
    "Symbol": "XRP/ETH",
    "Price": "0.00022010",
    "Side": "BUY",
    "Qty": "1.00000000", 			//Original Order qty
    "LeavesQty": "0.50000000", 			//Remaining working qty in market
    "CummQty": "0.50000000", 			//Total Filled Qty
    "ClOrdId": "1", 				//Client order id provided by client system
    "OrigClOrdId": "1", 			//Base Client order id provided by client system
    "ExchangeOrderId": "1", 			//Unique order id generated by exchange system
    "OrderStatus": "PARTIALFILL",
    "OrderType": "LIMIT",
    "TimeInForce": "GTC",
    "EntryTime": 1546238850238, 		//Order entry time in milliseconds
    "LastUpdateTime": 1546238945635 		//Last Updated (CXLD/PARTIALFILL/FILLED) time in milliseconds
}

```

<br /><br /><br />

# Market Data Streams (PARTIAL)
1. Base API endpoint is: ws://10.48.1.138:81

## Depth & Trade Streams
Pushes latest Depth and Timeandsales.

> 1. Currently Depth and Trade streams are combined. Will be separated in future
> 2. Depth currently is fixed Snapshot refresh. There is no incremental updates. (This feature will be upgraded in api version v2)

<b>Stream Name:</b> /ws/v1/depth?stream=<pair>&<pair>....&<pair>
> <pair> values is currency pairs such as XRP/ETH, EOS/BTC, etc.
> <pair> value is case insensitive.
> No filters per symbol pair is available currently on above stream. Filters will be implemented shortly.

Payload:

```javascript

{
   "MsgType":"MarketDataSnapshot", 			// MsgType or Update Type
   "Timestamp":1545119596094, 				// Milliseconds (epoch)
   "Symbol":"XRP/ETH", 					// Currency pair
   "NoMDEntries":5, 					// Total bids + asks
   "Bids":[
        {
            "MDEntryType":"Bid", 			// Bid or Ask update
            "MDEntryPx":"0.00020000", 			// Bid price
            "MDEntrySize":"0.02510000" 			// Bid Volume
   },
   {
            "MDEntryType":"Bid",
            "MDEntryPx":"0.00019000",
            "MDEntrySize":"0.04340000"
   },
   {
            "MDEntryType":"Bid",
            "MDEntryPx":"0.00018900",
            "MDEntrySize":"0.00234000"
   }
   ],
   "Asks":[
   {
            "MDEntryType":"Ask", 			// Bid or Ask Update
            "MDEntryPx":"0.00020100", 			// Offer price
            "MDEntrySize":"0.00029800" 			// Offer volume
   },
   {
            "MDEntryType":"Ask",
            "MDEntryPx":"0.00020200",
            "MDEntrySize":"0.00635400"
   }
   ]
}

```

```javascript

{
   "MsgType":"MarketDataSnapshot", 			// MsgType or Update Type
   "Timestamp":1545120147260, 				// Milliseconds (epoch)
   "Symbol":"XRP/ETH",
   "NoMDEntries":1, 					//Number of entries in update
   "MDEntryType":"Trade", 				//Update Type
   "MDEntryPx":"0.00020000", 				//Last Traded Price
   "MDEntrySize":"0.00010000" 				//Last Traded Qty
}

```

<br /><br /><br />

# User Data Stream (PARTIAL)
1. Base API endpoint is: ws://10.48.1.138:81

## Execution Report Stream
Pushes realtime order and trade execution reports.

<b>Stream Name:</b> /ws/v1/userData?apiId=52C8BC17765FC0278C53089DBC3AACCF7091F529DBB17DAB7774493D269EB145
> apiId: Provide your api id as a request query string value to the end point.
> Supports multiple client connection for single apiId.

Payload:

```javascript


{
	"MsgType":"ExecutionReport",
	"TransactTime":1546243735685, 				//Timestamp in milliseconds
	"ExecType":"Partial Fill", 				//Execution Type
	"OrdStatus":"Partially Filled", 			//Order Status
	"Symbol":"XRP/ETH",
	"Side":"SELL",
	"Price":"0.00019000", 					
	"OrderQty":"0.00250000", 				//Order Qty
	"LeavesQty":"0.00150000", 				//Remaining Open qty in market
	"CumQty":"0.00100000", 					//Total executed / filled qty
	"OrdType":"LIMIT",
	"TimeInForce":"GTC",
	"ClOrdID":"1", 						//Client Order id provided by client system
	"OrigClOrdID":"1", 					//Base Client Order id provided by client system
	"OrderID":1 						//Exchange generated unique order id
}

```
