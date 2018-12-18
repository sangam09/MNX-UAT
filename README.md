# MNX-UAT

## General API Information
<ul>
	<li>Base API endpoint is: https://10.48.1.138/api/v1</li>
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
GET /exchangeInfo
```
