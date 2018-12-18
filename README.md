# MNX-UAT

Jump to following sections (websockets):
<ol> 
	<li>[Market Data stream](https://github.com/sangam09/MNX-UAT#websockets): Real Time Market Data over websocket.</li>
	<li>User data stream: Real time execution updates (orders & trades)</li>
</ol>

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


# Websockets
