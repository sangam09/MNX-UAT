# MNX-UAT

Jump to following sections (websockets):
1. [Market Data stream](https://github.com/sangam09/MNX-UAT#websockets) : Real Time Market Data over websocket.
2. User data stream : Real time execution updates (orders & trades) <b>Implementation Pending</b>

## General API Information
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
	<li> for <code><i>GET</i></code> endpoints, parameters must be sent in request body with content type as `application/x-wwww-form-urlencoded`</li>
	<li>Parameters are case-insensitive and can be sent in any order.
</ul>


## General Endpoints:


### Exchange Info
```
GET /api/v1/exchangeInfo
```

Retrieve JSON object containing the currency pairs, trading status, rateLimits etc.
<b>Weight:</b> 1
<b>Parameters:</b> None
<b>Response:</b>

```
{
    "timezone": "GMT",
    "serverTime": 1545118368886,
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
            "instrument_id": 1,
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
            "instrument_id": 3,
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


# WS Market Data Streams
1. Base API endpoint is: ws://10.48.1.138:81

## Depth & Trade Streams
> 1. Currently Depth and Trade streams are combined. Will be separated in future
> 2. Depth currently is fixed Snapshot refresh. There is no incremental updates. (This feature will be upgraded in api version v2)
