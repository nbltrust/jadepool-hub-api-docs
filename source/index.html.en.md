---
title: API Reference

language_tabs: # must be one of https://git.io/vQNgJ
  - Example

toc_footers:
  - <a href='https://github.com/nbltrust/jadepool-doc'>Jadepool Documentation</a>

includes:
  - errors

search: true
---

# Introduction

Welcome to the Jadepool API! 

We provide two versions of API: V1 and V2. We recommend you to use V2 which includes significant improvements comparing to V1. However, there will still be small changes
to V2 APIs along our development cycle. Check out our [releases](https://github.com/nbltrust/jadepool-doc/releases) for details.

Feel free to check out our [NodeJs SDK](https://github.com/nbltrust/jadepool-sdk-nodejs) and [Java SDK](https://github.com/nbltrust/jadepool-sdk-java). SDK only supports
V1 for now.

# General Structure

## POST Request

> "Request withdrawal" example:

```json
{  
   "sig":"e365e9f0d43e0dd2b2c0d6ff0fa90c473791bdc532c67fe290f60229633f6c4a4839fb10245c5d29b82a5330788ffd504aa81149a66484dac48e5b9ac8f8881c",
   "data":{  
      "sequence":32132,
      "to":"mg2bfYdfii2GG13HK94jXBYPPCSWRmSiAS",
      "type":"BTC",
      "value":"0.01"
   },
   "encode":"hex",
   "appid":"testa",
   "hash":"sha256",
   "timestamp":1551907163440
}
```

This is the general request structure for POST requests.

*Request Parameters*

Parameter | Required | Type | Description
--------- | ------- | --------- | -----------
data | yes | object | data block that main parameters should be wrapped in
appid | yes | string | application ID, required since 0.11.22 version
timestamp | yes | number | the timestamp client used for signing
sig | yes | string | client's ECC signature
encode | no | string | signature encoding format, support hex and base64, default is base64
hash | no | string | support sha3 and sha256 and md5, default value is sha3
lang | no | string | the language of errors and messages in response. support en, cn, ja, default is cn

## GET Request

> "Fetch Wallet Status" example:

```
http://jadepool.example/api/v1/wallet/btc/status?appid=testa&timestamp=1548406480765&encode=hex&hash=sha256
&sig=355776c4b127a8db996032b2b0b3e5618ac83f1b2d394bada3af6b27fa9631f036dd3b46067b274d0e985453bac0f6783da262a41f7f920d84d0514b47b58937&lang=en
```

This is the general request structure for GET requests.

*Query Parameters*

Parameter | Required | Type | Description
--------- | ------- | --------- | -----------
appid | yes | string | application ID, required since 0.11.22 version
timestamp | yes | number | the timestamp client used for signing
sig | yes | string | client's ECC signature
encode | no | string | signature encoding format, support hex and base64, default is base64
hash | no | string | support sha3 and sha256 and md5, default value is sha3
lang | no | string | the language of errors and messages in response. support en, cn, ja, default is cn

## Response 

> Successful Response Example:

```json
{
    "code": 0,
    "status": 0,
    "message": "OK",
    "crypto": "ecc",
    "timestamp": 1557144244145,
    "sig": {
        "r": "a6pPfsRpp7NVnjtCcoqDyqp5nOS971LG34uECKDBsj4=",
        "s": "csbcZjx+LycfCZoYPa5ba324TUnFIEmy0TzX4vNUPqI=",
        "v": 27
    },
    "result": {
        "address": "0x352c3d587080ae186c6037ccf786ea1c71ed87be",
        "valid": true,
        "namespace": "ETH",
        "sid": "HcEYvElHYswB47SDAAAC"
    }
}
```

> Failed Response Example:

```json
{
    "code": 20000,
    "status": 20000,
    "category": "invalid-parameter",
    "message": "unsupported coin type",
    "result": {
        "info": "field(type) cointype - wrong"
    }
}
```

These are the regular fields in response body.

*Response Fields*

Field | Type | Description
--------- | ------- | --------- 
code | number | 0 if succeeds. Otherwise it shows corresponding error code in [Errors](#errors)
status | number | 0 if succeeds. Otherwise it shows corresponding error code in [Errors](#errors)
message | string | "OK" if succeeds. Otherwise it shows corresponding error message in [Errors](#errors)
crypto | string | Only "ecc" is supported for now
timestamp | number | the timestamp Jadepool used for signing
sig | object | Jadepool's ECC signature
result | object | response contents

# V1 API

<aside class="notice">
All timestamps are in millisecond.
</aside>

<aside class="notice">
All main parameters in POST API should be wraped in "data" object
</aside>

## Address

### Create Multiple Addresses

> Request "Create Multiple Addresses" API returns JSON structured like this:

```json
{
    "code": 0,
    "status": 0,
    "message": "OK",
    "crypto": "ecc",
    "timestamp": 1556006943412,
    "sig": {
        "r": "1fdgXdwlQDNoC8qJdJubZXLT5jrtwm3ttX/q2qIsIus=",
        "s": "HBC5Z8QmQDp0twfGE4k9ywK4p1VuTfIPNC7hFTXOut0=",
        "v": 28
    },
    "result": [
        {
            "id": 277,
            "appid": "pri",
            "coinName": "ETH",
            "address": "0xfeb3cd94cd4e1ca90514d589cf7b344e9e8cb4b1",
            "state": "used",
            "mode": "deposit",
            "create_at": 1556006943104,
            "update_at": 1556006943346,
            "namespace": "ETH"
        },
        {
            "id": 278,
            "appid": "pri",
            "type": "ETH",
            "address": "0x18cee689f40be2b1539ed875c0be57a768e078c5",
            "state": "used",
            "mode": "deposit",
            "create_at": 1556006943381,
            "update_at": 1556006943408,
            "namespace": "ETH"
        }
    ]
}
```

This endpoint enables you to request multiple new addresses at once.

*HTTP Request*

`POST http://jadepool.example/api/v1/addresses/batchnew`

*Main Parameters*

Parameter | Required | Type | Description
--------- | ------- | --------- | -----------
data.type | yes | string | coin name
data.amount | yes | number | number of addresses, range from 1 to 500.
data.mode | no | string | address mode: 'auto', 'deposit', 'deposit_memo', 'normal'
data.callback | no | string | callback url

*Response Result*

Value | Type | Description
--------- | ------- | ---------
id | number | unique ID in Jadepool's database
appid | string | application ID, configurable on admin
coinName | string | token type of the new address
address | string | the new address
state | string | this value does not mean anything for now
mode | string | address mode

### Create Single Address

> Request "Create Single Address" API returns JSON structured like this:

```json
{
    "code": 0,
    "status": 0,
    "message": "OK",
    "crypto": "ecc",
    "timestamp": 1556007795024,
    "sig": {
        "r": "NgoqsdWEbqED8nfFR8arTE5psEbIPDRn1qCN6YOTaTI=",
        "s": "QVoftyErHN+VPlnCn1hoLqi8S6UAe2zJrjemWNQB/NM=",
        "v": 27
    },
    "result": {
        "id": 281,
        "appid": "pri",
        "type": "ETH",
        "address": "0xbf0aa791e34d3af906050fcb713305c1d92880af",
        "state": "used",
        "mode": "deposit",
        "create_at": 1556007795000,
        "update_at": 1556007795020,
        "namespace": "ETH",
        "sid": "j5D28yAyhO02C-_6AAAA"
    }
}
```

This endpoint enables you to request single new address.

*HTTP Request*

`POST http://jadepool.example/api/v1/addresses/new`

*Main Parameters*

Parameter | Required | Type | Description
--------- | ------- | --------- | -----------
data.type | yes | string | coin name
data.mode | no | string | address mode: 'auto', 'deposit', 'deposit_memo', 'normal'
data.callback | no | string | callback url

*Response Result*

Value | Type | Description
--------- | ------- | ---------
id | number | unique ID in Jadepool's database
appid | string | application ID, configurable on admin
coinName | string | token type of the new address
address | string | the new address
state | string | this value does not mean anything for now
mode | string | address mode

### Validate Address

> Request "Validate address" API returns JSON structured like this:

```json
{
    "code": 0,
    "status": 0,
    "message": "OK",
    "crypto": "ecc",
    "timestamp": 1556008291521,
    "sig": {
        "r": "/9lfiJms7SiZZDFpjVc4+phMIl1G3WN3fxKuwAE0xjo=",
        "s": "JZSHP/h0Pf1C7SsZinvL3lN1uO1BvaWvD+pHWQMVGuo=",
        "v": 27
    },
    "result": {
        "address": "0xbf0aa791e34d3af906050fcb713305c1d92880af",
        "valid": true,
        "namespace": "ETH",
        "sid": "j5D28yAyhO02C-_6AAAA"
    }
}
```

This endpoint enables you to verify if an address is valid for specified token.

*HTTP Request*

`POST http://jadepool.example/api/v1/addresses/verify`

*Main Parameters*

Parameter | Required | Type | Description
--------- | ------- | --------- | -----------
data.type | yes | string | coin name
data.address | yes | string | address to validate

*Response Result*

Value | Type | Description
--------- | ------- | ---------
address | string | the address to validate
valid | boolean | the address is valid or not

## Audit

### Fetch Audit Order

> Request "Fetch Audit Order" API returns JSON structured like this:

```json
{
    "code": 0,
    "status": 0,
    "message": "OK",
    "crypto": "ecc",
    "timestamp": 1546425032000,
    "sig": {
        "r": "1S3Uk1gTOwnbshQLVsG9KHnAvQbmRm2Z0C/WcYN1z7w=",
        "s": "Z9I/bjAiica0RQJHHWEow7GzJi61MjoQnA4tT/VlDic=",
        "v": 28
    },
    "result": {
        "calculated": true,
        "deposit_total": "0",
        "deposit_num": 0,
        "withdraw_total": "0",
        "withdraw_num": 0,
        "sweep_total": "0",
        "sweep_num": 0,
        "sweep_internal_total": "0",
        "sweep_internal_num": 0,
        "airdrop_total": "0",
        "airdrop_num": 0,
        "recharge_total": "0",
        "recharge_num": 0,
        "recharge_internal_total": "0",
        "recharge_internal_num": 0,
        "recharge_unknown_total": "0",
        "recharge_unknown_num": 0,
        "recharge_special_total": "0",
        "recharge_special_num": 0,
        "failed_withdraw_num": 0,
        "failed_sweep_num": 0,
        "failed_sweep_internal_num": 0,
        "fees": [],
        "type": "ETH",
        "timestamp": 1546425032000,
        "blocknumber": 9921056,
        "appid": "test",
        "create_at": "2019-04-23T08:32:51.859Z",
        "update_at": "2019-04-23T08:32:58.666Z",
        "__v": 0,
        "calc_order_num": 0,
        "last": "5cbecdba8d1607c10ddddb4b",
        "id": "5cbecdb38d1607c10ddddb49"
    }
}
```

This endpoint enables you to fetch audit order.

*HTTP Request*

`GET http://jadepool.example/api/v1/audits/:id`

*Query Parameters*

Parameter | Description
--------- | ------- 
id | audit order ID

*Response Result*

Value | Type | Description
--------- | ------- | ---------
calculated | boolean | if the auditing is finished
deposit_total | string | total deposit token value
deposit_num | number | total deposit times
withdraw_total | string | total withdraw token value
withdraw_num | number | total withdraw times
sweep_total | string | total sweep token value
sweep_num | number | total sweep times
sweep_internal_total | string | total sweep_internal token value
sweep_internal_num | number | total sweep_internal times
airdrop_total | string | total airdrop token value
airdrop_num | number | total airdrop times
recharge_total | string | total recharge token value
recharge_num | number | total recharge times
recharge_internal_total | string | total recharge_internal token value
recharge_internal_num | number | total recharge_internal times
recharge_unknown_total | string | total recharge_unknown token value
recharge_unknown_num | number | total recharge_unknown times
recharge_special_total | string | total recharge_special token value
recharge_special_num | number | total recharge_special times
failed_withdraw_num | number | total failed withdraw times
failed_sweep_num | number | total failed sweep times
failed_sweep_internal_num | number | total failed sweep_internal times
fees | array | all type of fees burned
type | string | the audited token type
timestamp | number | the audited timestamp
blocknumber | number | corresponding block near the audited timestamp
appid | string | application ID
calc_order_num | number | audited order number
last | string | audit order ID of the previous audit
id | string | audit order ID of the current audit


### Request audit

> Request "Request audit" API returns JSON structured like this:

```json
{
    "code": 0,
    "status": 0,
    "message": "OK",
    "crypto": "ecc",
    "timestamp": 1557298936621,
    "sig": {
        "r": "Ul3ss7f53UfspW90IgdUXTouhVi9c2Z4tyd9JpWoF9E=",
        "s": "Umm40vdnKymEaUrOw6zxZ6c8g/8L8ERO9TYRgdUEIdQ=",
        "v": 27
    },
    "result": {
        "type": "BTC",
        "current": {
            "id": "5cd27ef846d3a66f7c08d851",
            "type": "BTC",
            "blocknumber": 1513223,
            "timestamp": 1557298927000
        },
        "last": {
            "id": "5cc4a6b074a1172be751343e",
            "type": "BTC",
            "blocknumber": 1512837,
            "timestamp": 1556380800000
        },
        "namespace": "BTC",
        "sid": "uJK8Pk6LUltIu4epAAAB"
    }
}
```

This endpoint enables you to request audit with timestamp of your choice.

*HTTP Request*

`POST http://jadepool.example/api/v1/audits`

*Main Parameters*

Parameter | Required | Type | Description
--------- | ------- | --------- | -----------
data.type | yes | string | coin name for auditing
data.audittime | yes | number | audit timestamp
data.callback | no | string | callback url

*Response Result*

Value | Type | Description
--------- | ------- | ---------
type | string | the audited token type
current | object | info of the current audit order
last | object | info of the previous audit order
id | string | audit order ID of the current/last audit
type | string | the audited token type
timestamp | number | the audited timestamp
blocknumber | number | corresponding block near the audited timestamp

### Audited Orders

> Request "Audited Orders" API returns JSON structured like this:

```json
{
    "code": 0,
    "status": 0,
    "message": "OK",
    "crypto": "ecc",
    "timestamp": 1556008499840,
    "sig": {
        "r": "MDQ7ytbVqn2csvSRIlCK169YP+6Fw4npwCZrOyg7kR8=",
        "s": "Z2Qf7nB45dcH6TfjpDT1hH+eaef2ep2ARYJKF0t7iss=",
        "v": 28
    },
    "result": [
        {
            "data": {
                "timestampHandle" : 1548316657572.0,
                "timestampBegin" : 1548316657778.0,
                "timestampFinish" : 1548320514245.0
            },
            "_id": "5c49829fdddecb563c50f389",
            "id": "6413",
            "coinName": "BTC",
            "txid": "4427227cb68165f190342f857ba1a1871654d170e732aa731fbd184990be058a",
            "meta": "1548320257779",
            "state": "done",
            "bizType": "WITHDRAW",
            "type": "BTC",
            "from": "myKRmYjEzPUtsqSUzy6yMEmpMT491Rj6Va",
            "to": "mfvLYmxGYgwL5NMBH7pHtBA9QzjpbapNNb",
            "value": "0.08",
            "confirmations": 6,
            "create_at": 1548316656499.0,
            "update_at": 1548320514267.0,
            "actionArgs": [],
            "actionResults": [],
            "n": 0,
            "fee": "0.00109375",
            "fees": [],
            "hash": "4427227cb68165f190342f857ba1a1871654d170e732aa731fbd184990be058a",
            "block": 1453798,
            "extraData": "",
            "memo": "",
            "sendAgain": false,
            "namespace": "BTC",
            "sid": "j5D28yAyhO02C-_6AAAA"
        }
    ]
}

```

This endpoint enables you to fetch orders in specified audit.

*HTTP Request*

`GET http://jadepool.example/api/v1/audits/:id/orders`

*Query Parameters*

Parameter | Description
--------- | ------- 
id | audit order ID

*Response Result*

Value | Type | Description
--------- | ------- | ---------
id | string | the order ID
coinName | string | unique token name
txid | string | transaction hash
meta | any | supplementary data
state | string | order state
bizType | string | order type
type | string | token type
from | string | transaction input
to | string | transaction output
value | string | transaction value
confirmations | number | number of transaction confirmations
fee | string | fee burnt for the transaction
fees | array | all types of fee burnt for the transaction
hash | string | transaction hash
block | number | the block transaction mined in
extraData | string | same as the extraData in request
memo | string | order note, editable on admin
sendAgain | boolean | if we suggest client to send the same request again
data | object | latest info of the transaction

## Transaction

### Request Withdrawal

> Request "Request Withdrawal" API returns JSON structured like this:

```json
{
    "code": 0,
    "status": 0,
    "message": "OK",
    "crypto": "ecc",
    "timestamp": 1556008499840,
    "sig": {
        "r": "MDQ7ytbVqn2csvSRIlCK169YP+6Fw4npwCZrOyg7kR8=",
        "s": "Z2Qf7nB45dcH6TfjpDT1hH+eaef2ep2ARYJKF0t7iss=",
        "v": 28
    },
    "result": {
        "data": {
            "timestampBegin": 0,
            "timestampFinish": 0
        },
        "_id": "5cbece338d1607c10ddddbcd",
        "id": "6513",
        "coinName": "ETH",
        "txid": null,
        "meta": null,
        "state": "init",
        "bizType": "WITHDRAW",
        "type": "ETH",
        "coinType": "ETH",
        "from": "0xa268f4ae690ac1421775568d783439c7c1151749",
        "to": "0xbf0aa791e34d3af906050fcb713305c1d92880af",
        "value": "0.001",
        "confirmations": 0,
        "create_at": 1556008499740,
        "update_at": 1556008499742,
        "actionArgs": [],
        "actionResults": [],
        "n": 0,
        "fee": "0",
        "fees": [],
        "hash": "",
        "block": -1,
        "extraData": "",
        "memo": "",
        "sendAgain": false,
        "namespace": "ETH",
        "sid": "j5D28yAyhO02C-_6AAAA"
    }
}
```

This endpoint enables you to request withdrawal.

*HTTP Request*

`POST http://jadepool.example/api/v1/transactions`

*Main Parameters*

Parameter | Required | Type | Description
--------- | ------- | --------- | -----------
data.type | yes | string | coin name for auditing
data.to | yes | string | withdrawal address
data.value | yes | string | value to withdraw
data.sequence | no | number | unique API nonce for each app ID
data.auth | no | string | authentication for coin, only for HSM 
data.extraData | no | string | some data to save in Jadepool database and will be returned the same in response
data.callback | no | string | callback url

*Response Result*

Value | Type | Description
--------- | ------- | ---------
id | string | the order ID
coinName | string | unique token name
txid | string | transaction hash
meta | any | supplementary data
state | string | order state
bizType | string | order type
type | string | token type
from | string | transaction input
to | string | transaction output
value | string | transaction value
confirmations | number | number of transaction confirmations
fee | string | fee burnt for the transaction
fees | array | all types of fee burnt for the transaction
hash | string | transaction hash
block | number | the block transaction mined in
extraData | string | same as the extraData in request
memo | string | order note, editable on admin
sendAgain | boolean | if we suggest client to send the same request again
data | object | latest info of the transaction

### Fetch Order

> Request "fetch Order" API returns JSON structured like this:

```json
{
    "code": 0,
    "status": 0,
    "message": "OK",
    "crypto": "ecc",
    "timestamp": 1556008528904,
    "sig": {
        "r": "cJyoM1mnhdSrKECuXmWGGx5gD1ZVrEn0Fjb5xqmcJrM=",
        "s": "O9n6pLATkYJ5TA2vU/nPwCXsthtni41b1JYr/B9L1sw=",
        "v": 28
    },
    "result": {
        "_id": "5cbece338d1607c10ddddbcd",
        "id": "6513",
        "coinName": "ETH",
        "txid": null,
        "meta": null,
        "state": "init",
        "bizType": "WITHDRAW",
        "type": "ETH",
        "coinType": "ETH",
        "from": "",
        "to": "0xbf0aa791e34d3af906050fcb713305c1d92880af",
        "value": "0.001",
        "create_at": 1556008499740,
        "update_at": 1556008499742,
        "actionArgs": [],
        "actionResults": [],
        "n": 0,
        "fee": "0",
        "fees": [],
        "data": {
            "timestampBegin": 0,
            "timestampFinish": 0,
            "type": "Ethereum",
            "hash": "",
            "blockNumber": -1,
            "blockHash": "",
            "from": [],
            "to": [],
            "state": "init"
        },
        "hash": "",
        "block": -1,
        "extraData": "",
        "memo": "",
        "sendAgain": false,
        "namespace": "ETH",
        "sid": "j5D28yAyhO02C-_6AAAA"
    }
}
```

This endpoint enables you to fetch any order details from Jadepool database.

*HTTP Request*

`GET http://jadepool.example/api/v1/transactions/:id`

*Query Parameters*

Parameter | Description
--------- | ------- 
id | order ID

*Response Result*

Value | Type | Description
--------- | ------- | ---------
id | string | the order ID
coinName | string | unique token name
txid | string | transaction hash
meta | any | supplementary data
state | string | order state
bizType | string | order type
type | string | token type
from | string | transaction input
to | string | transaction output
value | string | transaction value
confirmations | number | number of transaction confirmations
fee | string | fee burnt for the transaction
fees | array | all types of fee burnt for the transaction
hash | string | transaction hash
block | number | the block transaction mined in
extraData | string | same as the extraData in request
memo | string | order note, editable on admin
sendAgain | boolean | if we suggest client to send the same request again
data | object | latest info of the transaction


## Wallet

### Fetch Wallet Status

> Request "Fetch Wallet Status" API returns JSON structured like this:

```json
{
    "code": 0,
    "status": 0,
    "message": "OK",
    "crypto": "ecc",
    "timestamp": 1556008547809,
    "sig": {
        "r": "9Eld2EfJfsIxTv7GRbg41TsXos2ntvmOmcutXHh5yKg=",
        "s": "DgGgelZ0UWc01iDOfYixCG3P0rv59xKPUqgImKrAd4w=",
        "v": 28
    },
    "result": {
        "balance": "0.01",
        "balanceAvailable": "0",
        "balanceUnavailable": "0.01",
        "update_at": 1556008547806,
        "addressesWithBalance": 1,
        "namespace": "ETH",
        "sid": "j5D28yAyhO02C-_6AAAA"
    }
}
```

This endpoint enables you to fetch balance details of any enabled token.

*HTTP Request*

`GET http://jadepool.example/api/v1/wallet/:coinName/status`

*Query Parameters*

Parameter | Description
--------- | -------
coinName | coin name

*Response Result*

Value | Type | Description
--------- | ------- | ---------
balance | string | total balance
balanceAvailable | string | balance available for outgoing transfer
balanceUnavailable | string | balance unavailable for outgoing transfer
update_at | number | the balance update time
addressesWithBalance | number | number of addresses that have balance

## Delegation

<aside class="warning">
Query parameter "coinName" in every delegation API must be the main token that can be delegated to validators. Otherwise
request will be rejected.
</aside>

### Fetch All Validators

> Request "Fetch All Validators" API returns JSON structured like this:

```json
{
    "code": 0,
    "status": 0,
    "message": "OK",
    "crypto": "ecc",
    "timestamp": 1556009186429,
    "sig": {
        "r": "kfHfp+ql5OGMYKhLORY1ri7mzP3uv1FGNL5TtK8XYLw=",
        "s": "WBhcqM6Jn4BJFHxQicO9uR9T7t6aFYCnGVzPqHfB5dw=",
        "v": 27
    },
    "result": [
        {
            "name": "node0",
            "address": "cosmosvaloper1z7jw2deueg37nd6v3flmn4wy2v3nhz55tsx4y9",
            "pubkey": "cosmosvalconspub1zcjduepqmp75ajy7jm0mqttc2d23ks54dn83yfshxd3038r4ejldcjcd0atsnwxnyr",
            "jailed": false,
            "status": 2,
            "shares": "101010000.000000000000000000",
            "delegationAmount": "101010000",
            "minSelfDelegation": "1",
            "commission": {
                "rate": 0.1,
                "maxRate": 0.2,
                "maxChangeRate": 0.01,
                "updateAt": "2019-04-23T04:41:34.202Z"
            }
        },
        {
            "name": "node1",
            "address": "cosmosvaloper1wpw2nrq850mwgrhteh3jwc2g7w9led2hervrfu",
            "pubkey": "cosmosvalconspub1zcjduepqrjklsgkc8jeusfkjmrltlvz0s9cxe7vxr7uajpx2v0rfn0zvgtxqw5mwhj",
            "jailed": true,
            "status": 0,
            "shares": "2524242.424242424242424241",
            "delegationAmount": "2499000",
            "minSelfDelegation": "100000",
            "commission": {
                "rate": 0.1,
                "maxRate": 0.2,
                "maxChangeRate": 0.01,
                "updateAt": "2019-04-23T04:41:44.782Z"
            }
        }
    ]
}
```

This endpoint enables you to fetch all validators of specified chain.

*HTTP Request*

`GET http://jadepool.example/api/v1/stake/:coinName/validators`

*Query Parameters*

Parameter | Description
--------- | ------- 
coinName | coin name

*Response Result*

Value | Type | Description
--------- | ------- | ---------
name | string | validator name
address | string | validator's address
pubkey | string | validator's public key
jailed | boolean | if the validator is jailed
status | number | validator's status
shares | string | validator's shares
delegationAmount | string | amount delegated to validator
minSelfDelegation | string | amount delegated to validator by itself
commission | object | validator's commission
rate | float | validator's commission rate
maxRate | float | validator's commission maxRate
maxChangeRate | float | validator's commission maxChangeRate
updateAt | string | validator info update time

### Fetch Specified Validator

> Request "Fetch Specified Validator" API returns JSON structured like this:

```json
{
    "code": 0,
    "status": 0,
    "message": "OK",
    "crypto": "ecc",
    "timestamp": 1554801539663,
    "sig": {
        "r": "9H9TTNLQhYwre7yKsfd6dmt8iLAzqvVRxlt1O7++dZE=",
        "s": "Qcn6m5ZwqKnuBLHyRtFuB+UNhfJAz+/Ny+kA+8SzVKQ=",
        "v": 28
    },
    "result": {
        "name": "node1",
        "address": "cosmosvaloper1wpw2nrq850mwgrhteh3jwc2g7w9led2hervrfu",
        "pubkey": "cosmosvalconspub1zcjduepqfr9zx5yrnfg7y8zwhqvm8yywtvhuzg0qn7auwygkkp4zlfd36pusx4xgn3",
        "jailed": false,
        "status": 2,
        "shares": "1040000.000000000000000000",
        "delegationAmount": "1040000",
        "minSelfDelegation": "100000",
        "commission": {
            "rate": 0.1,
            "maxRate": 0.2,
            "maxChangeRate": 0.01,
            "updateAt": "2019-04-03T08:19:29.921Z"
        },
        "namespace": "Cosmos",
        "sid": "h-02Y6BZGFDuZyWzAAAF"
    }
}
```

This endpoint enables you to fetch details of a specified validator.

*HTTP Request*

`GET http://jadepool.example/api/v1/stake/:coinName/validators/:validator`

*Query Parameters*

Parameter | Description
--------- | ------- 
coinName | coin name
validator | validator name

*Response Result*

Value | Type | Description
--------- | ------- | ---------
name | string | validator name
address | string | validator's address
pubkey | string | validator's public key
jailed | boolean | if the validator is jailed
status | number | validator's status
shares | string | validator's shares
delegationAmount | string | amount delegated to validator
minSelfDelegation | string | amount delegated to validator by itself
commission | object | validator's commission
rate | float | validator's commission rate
maxRate | float | validator's commission maxRate
maxChangeRate | float | validator's commission maxChangeRate
updateAt | string | validator info update time

### Outstanding Reward At Validator

> Request "Fetch Reward With Validator" API returns JSON structured like this:

```json
{
    "code": 0,
    "status": 0,
    "message": "OK",
    "crypto": "ecc",
    "timestamp": 1554796799490,
    "sig": {
        "r": "AD40uPwf5iJkuTXuhuKfRnt3+/imo8PNBgryOHxYhHE=",
        "s": "KN2Dk9BHCyucq291X8wFeFE9xYcnL3ziYBjdbeTfKaY=",
        "v": 28
    },
    "result": [
        {
            "validator": "cosmosvaloper1wpw2nrq850mwgrhteh3jwc2g7w9led2hervrfu",
            "nativeName": "stake",
            "nativeAmount": "40863.901112837984093534",
            "coinName": "ATOM",
            "amount": "0.04086390111283798409"
        }
    ]
}
```

This endpoint enables you to fetch everyone's total reward amount at a specified validator.

*HTTP Request*

`GET http://jadepool.example/api/v1/stake/:coinName/validators/:validator/outstanding_rewards`

*Query Parameters*

Parameter | Description
--------- | ------- 
coinName | coin name
validator | validator name

*Response Result*

Value | Type | Description
--------- | ------- | ---------
validator | string | validator name
nativeName | string | token
nativeAmount | string | total reward amount in token unit
coinName | string | main token
amount | string | total reward amount in main token unit

### Request Delegation

> Request "Request Delegation" API returns JSON structured like this:

```json
{
    "code": 0,
    "status": 0,
    "message": "OK",
    "crypto": "ecc",
    "timestamp": 1557309326894,
    "sig": {
        "r": "kGvfjb52iroMPZP/UH7Gq0ngzzRwc/sNrzvvxXF0Ro0=",
        "s": "JMAThiBR+c4lBmu/nC0xNFLiMRemA9wCyZ82bTqnhKQ=",
        "v": 28
    },
    "result": {
        "data": {
            "timestampBegin": 0,
            "timestampFinish": 0
        },
        "_id": "5cd2a78ee7fa5b57ab9ddaec",
        "id": "2428",
        "coinName": "ATOM",
        "txid": null,
        "meta": null,
        "state": "init",
        "bizType": "DELEGATE",
        "type": "ATOM",
        "coinType": "ATOM",
        "from": "cosmos1hrnl5pugag20gnu8zy3604jetqrjaf60czkl8u",
        "to": "cosmosvaloper1wpw2nrq850mwgrhteh3jwc2g7w9led2hervrfu",
        "value": "0.1",
        "sequence": 1557309326848,
        "confirmations": 0,
        "create_at": 1557309326881,
        "update_at": 1557309326884,
        "action": "delegate",
        "actionArgs": [
            "cosmos1hrnl5pugag20gnu8zy3604jetqrjaf60czkl8u",
            "cosmosvaloper1wpw2nrq850mwgrhteh3jwc2g7w9led2hervrfu",
            "0.1"
        ],
        "actionResults": [],
        "n": 0,
        "fee": "0",
        "fees": [],
        "hash": "",
        "block": -1,
        "extraData": "",
        "memo": "",
        "sendAgain": false,
        "namespace": "Cosmos",
        "sid": "V0-ORASYpnLqodqtAAAG"
    }
}
```

This endpoint enables you to request delegation.

*HTTP Request*

`POST http://jadepool.example/api/v1/stake/:coinName/delegators/:delegator/delegate`

*Query Parameters*

Parameter | Description
--------- | ------- 
coinName | coin name
delegator | delegate from address

*Main Parameters*

Parameter | Required | Type | Description
--------- | ------- | --------- | -----------
data.amount | yes | string | delegate value
data.validator | yes | string | validator to delegate
data.sequence | yes | number | unique sequence

*Response Result*

Value | Type | Description
--------- | ------- | ---------
id | string | the order ID
coinName | string | unique token name
txid | string | transaction hash
meta | any | supplementary data
state | string | order state
bizType | string | order type
type | string | token type
from | string | transaction input
to | string | transaction output
value | string | transaction value
confirmations | number | number of transaction confirmations
fee | string | fee burnt for the transaction
fees | array | all types of fee burnt for the transaction
hash | string | transaction hash
block | number | the block transaction mined in
extraData | string | same as the extraData in request
memo | string | order note, editable on admin
sendAgain | boolean | if we suggest client to send the same request again
data | object | latest info of the transaction

### Request Re-delegation

> Request "Request Re-delegation" API returns JSON structured like this:

```json
{
    "code": 0,
    "status": 0,
    "message": "OK",
    "crypto": "ecc",
    "timestamp": 1556008441494,
    "sig": {
        "r": "bPJroemxN8K+y1eDoAv/L37FjmzUfdjoUdjl9NyNSbs=",
        "s": "bIVR73xizsqwCANBfl+ZoJ6QeLm9Qc/AlAvMTg0YK7c=",
        "v": 28
    },
    "result": {
        "data": {
            "timestampBegin": 0,
            "timestampFinish": 0
        },
        "id": "23",
        "coinName": "ATOM",
        "txid": null,
        "meta": null,
        "state": "init",
        "bizType": "SYSTEM_CALL",
        "type": "ATOM",
        "coinType": "ATOM",
        "to": "cosmosvaloper1z7jw2deueg37nd6v3flmn4wy2v3nhz55tsx4y9",
        "value": "0",
        "sequence": 1556008441401,
        "confirmations": 0,
        "create_at": 1556008441486,
        "update_at": 1556008441488,
        "action": "re-delegate",
        "actionArgs": [
            "cosmos1hrnl5pugag20gnu8zy3604jetqrjaf60czkl8u",
            "cosmosvaloper1wpw2nrq850mwgrhteh3jwc2g7w9led2hervrfu",
            "cosmosvaloper1z7jw2deueg37nd6v3flmn4wy2v3nhz55tsx4y9",
            "0.001"
        ],
        "actionResults": [],
        "from": "cosmos1hrnl5pugag20gnu8zy3604jetqrjaf60czkl8u",
        "n": 0,
        "fee": "0",
        "fees": [],
        "hash": "",
        "block": -1,
        "extraData": "",
        "memo": "",
        "sendAgain": false,
        "namespace": "Cosmos",
        "sid": "RPamVnMkXe2aIgElAAAB"
    }
}
```

This endpoint enables you to request delegation switch from one validator to another.

*HTTP Request*

`POST http://jadepool.example/api/v1/stake/:coinName/delegators/:delegator/redelegate`

*Query Parameters*

Parameter | Description
--------- | ------- 
coinName | coin name
delegator | delegate from address

*Main Parameters*

Parameter | Required | Type | Description
--------- | ------- | --------- | -----------
data.amount | yes | string | delegate value
data.sequence | yes | number | unique sequence
data.src_validator | yes | string | original validator
data.dst_validator | yes | string | validator to re-delegate

*Response Result*

Value | Type | Description
--------- | ------- | ---------
id | string | the order ID
coinName | string | unique token name
txid | string | transaction hash
meta | any | supplementary data
state | string | order state
bizType | string | order type
type | string | token type
from | string | transaction input
to | string | transaction output
value | string | transaction value
confirmations | number | number of transaction confirmations
fee | string | fee burnt for the transaction
fees | array | all types of fee burnt for the transaction
hash | string | transaction hash
block | number | the block transaction mined in
extraData | string | same as the extraData in request
memo | string | order note, editable on admin
sendAgain | boolean | if we suggest client to send the same request again
data | object | latest info of the transaction

### Request Undelegation

> Request "Request Undelegation" API returns JSON structured like this:

```json
{
    "code": 0,
    "status": 0,
    "message": "OK",
    "crypto": "ecc",
    "timestamp": 1556018549519,
    "sig": {
        "r": "vxHm68qJIL6I3rl8E55Z219QZiv00GMBwZtvTrOaEyA=",
        "s": "HUN+YVLlQH/WLhywVllNbJ0RcXHsi9yImhRwZb0QaBc=",
        "v": 28
    },
    "result": {
        "data": {
            "timestampBegin": 0,
            "timestampFinish": 0
        },
        "id": "38",
        "coinName": "ATOM",
        "txid": null,
        "meta": null,
        "state": "init",
        "bizType": "UNDELEGATE",
        "type": "ATOM",
        "coinType": "ATOM",
        "to": "cosmosvaloper1z7jw2deueg37nd6v3flmn4wy2v3nhz55tsx4y9",
        "value": "0.001",
        "sequence": 1556018549426,
        "confirmations": 0,
        "create_at": 1556018549489,
        "update_at": 1556018549490,
        "action": "un-delegate",
        "actionArgs": [
            "cosmos1hrnl5pugag20gnu8zy3604jetqrjaf60czkl8u",
            "cosmosvaloper1z7jw2deueg37nd6v3flmn4wy2v3nhz55tsx4y9",
            "0.001"
        ],
        "actionResults": [],
        "from": "cosmos1hrnl5pugag20gnu8zy3604jetqrjaf60czkl8u",
        "n": 0,
        "fee": "0",
        "fees": [],
        "hash": "",
        "block": -1,
        "extraData": "",
        "memo": "",
        "sendAgain": false,
        "namespace": "Cosmos",
        "sid": "h2112M6bFQZImpdUAAAB"
    }
}
```

This endpoint enables you to request undelegation.

*HTTP Request*

`POST http://jadepool.example/api/v1/stake/:coinName/delegators/:delegator/undelegate`

*Query Parameters*

Parameter | Description
--------- | ------- 
coinName | coin name
delegator | address requests undelegation

*Main Parameters*

Parameter | Required | Type | Description
--------- | ------- | --------- | -----------
data.amount | yes | string | undelegate value
data.sequence | yes | number | unique sequence
data.validator | yes | string | validator undelegate from

*Response Result*

Value | Type | Description
--------- | ------- | ---------
id | string | the order ID
coinName | string | unique token name
txid | string | transaction hash
meta | any | supplementary data
state | string | order state
bizType | string | order type
type | string | token type
from | string | transaction input
to | string | transaction output
value | string | transaction value
confirmations | number | number of transaction confirmations
fee | string | fee burnt for the transaction
fees | array | all types of fee burnt for the transaction
hash | string | transaction hash
block | number | the block transaction mined in
extraData | string | same as the extraData in request
memo | string | order note, editable on admin
sendAgain | boolean | if we suggest client to send the same request again
data | object | latest info of the transaction

### Overall Delegation Status

> Request "Overall Delegation Status" API returns JSON structured like this:

```json
{
    "code": 0,
    "status": 0,
    "message": "OK",
    "crypto": "ecc",
    "timestamp": 1554884132672,
    "sig": {
        "r": "cgQV3td0/uPse5oMyBjcfbXJ6lw9hO3hJAovdxwW2DQ=",
        "s": "RorA0lqY4t0kCNyezW46SlDflGpc19k35KBhhfoq7K4=",
        "v": 27
    },
    "result": [
        {
            "delegator": "cosmos1hrnl5pugag20gnu8zy3604jetqrjaf60czkl8u",
            "validator": "cosmosvaloper1z7jw2deueg37nd6v3flmn4wy2v3nhz55tsx4y9",
            "amount": "0.03",
            "nativeAmount": "30000.000000000000000000"
        },
        {
            "delegator": "cosmos1hrnl5pugag20gnu8zy3604jetqrjaf60czkl8u",
            "validator": "cosmosvaloper1wpw2nrq850mwgrhteh3jwc2g7w9led2hervrfu",
            "amount": "0.011",
            "nativeAmount": "11000.000000000000000000"
        }
    ]
}
```

This endpoint enables you to fetch overall delegation status of a specified address.

*HTTP Request*

`GET http://jadepool.example/api/v1/stake/:coinName/delegators/:delegator/delegations`

*Query Parameters*

Parameter | Description
--------- | ------- 
coinName | coin name
delegator | delegate from address

*Response Result*

Value | Type | Description
--------- | ------- | ---------
delegator | string | the address that performs delegation
validator | string | validator to delegate to
amount | string | delegated amount in main token unit
nativeAmount | string | delegated amount in token unit

### Delegation Status At Validator

> Request "Delegation Status At Validator" API returns JSON structured like this:

```json
{
    "code": 0,
    "status": 0,
    "message": "OK",
    "crypto": "ecc",
    "timestamp": 1554796803335,
    "sig": {
        "r": "/v1N1WplY/d4Mses22jsHrb2MKkYuCEjFI82OumUyYE=",
        "s": "db9UeNag67gkF4RRgIcx9SZBkOwsyzQd6vnL9pIrg/Y=",
        "v": 27
    },
    "result": {
        "delegator": "cosmos1hrnl5pugag20gnu8zy3604jetqrjaf60czkl8u",
        "validator": "cosmosvaloper1z7jw2deueg37nd6v3flmn4wy2v3nhz55tsx4y9",
        "amount": "0.02",
        "nativeAmount": "20000.000000000000000000",
        "namespace": "Cosmos",
        "sid": "V2569qy2CYUbK6PHAAAH"
    }
}
```

This endpoint enables you to fetch delegation status of a specified address at a specified validator.

*HTTP Request*

`GET http://jadepool.example/api/v1/stake/:coinName/delegators/:delegator/delegations/:validator`

*Query Parameters*

Parameter | Description
--------- | ------- 
coinName | coin name
delegator | delegate from address
validator | validator

*Response Result*

Value | Type | Description
--------- | ------- | ---------
delegator | string | the address that performs delegation
validator | string | validator to delegate to
amount | string | delegated amount in main token unit
nativeAmount | string | delegated amount in token unit

### Overall Undelegation Status

> Request "Overall Undelegation Status" API returns JSON structured like this:

```json
{
    "code": 0,
    "status": 0,
    "message": "OK",
    "crypto": "ecc",
    "timestamp": 1556018572656,
    "sig": {
        "r": "WWOEITPtz0GLLeVk0fvJd57kFdnTvmma1FSY/FVo13M=",
        "s": "Ka3Kv8vwpMRYjDJaQDki39fGGy1+9vy677hpWOygLTc=",
        "v": 28
    },
    "result": [
        {
            "delegator": "cosmos1hrnl5pugag20gnu8zy3604jetqrjaf60czkl8u",
            "validator": "cosmosvaloper1z7jw2deueg37nd6v3flmn4wy2v3nhz55tsx4y9",
            "entries": [
                {
                    "creationHeight": 4779,
                    "completionTime": 1556018619654,
                    "initialNativeAmount": "1000",
                    "nativeAmount": "1000",
                    "initialAmount": "0.001",
                    "amount": "0.001"
                }
            ]
        }
    ]
}
```

This endpoint enables you to fetch overall unstaking status of a specified address.

*HTTP Request*

`GET http://jadepool.example/api/v1/stake/:coinName/delegators/:delegator/unstaking_delegations`

*Query Parameters*

Parameter | Description
--------- | ------- 
coinName | coin name
delegator | delegate from address

*Response Result*

Value | Type | Description
--------- | ------- | ---------
delegator | string | the address that performs delegation
validator | string | validator delegated to
creationHeight | string | the block height while undelegation requested
completionTime | string | undelegation completion time 
initialNativeAmount | string | reuqested amount to undelegate in token unit
nativeAmount | string | real-time actual amount that will be undelegated in token unit
initialAmount | string | reuqested amount to undelegate in main token unit
amount | string | real-time actual amount that will be undelegated in main token unit

### Undelegation At Validator

> Request "Undelegation At Validator" API returns JSON structured like this:

```json
{
    "code": 0,
    "status": 0,
    "message": "OK",
    "crypto": "ecc",
    "timestamp": 1556018590623,
    "sig": {
        "r": "Oaww0WvkGyus5IFxbYdCW76STqsk5Phb7+2rbSTTbP0=",
        "s": "Q128OllQ+BPB1qbhz8a2DzNh+zd8THHmtDtcnqydV6c=",
        "v": 28
    },
    "result": {
        "delegator": "cosmos1hrnl5pugag20gnu8zy3604jetqrjaf60czkl8u",
        "validator": "cosmosvaloper1z7jw2deueg37nd6v3flmn4wy2v3nhz55tsx4y9",
        "entries": [
            {
                "creationHeight": 4779,
                "completionTime": 1556018619654,
                "initialNativeAmount": "1000",
                "nativeAmount": "1000",
                "initialAmount": "0.001",
                "amount": "0.001"
            }
        ],
        "namespace": "Cosmos",
        "sid": "h2112M6bFQZImpdUAAAB"
    }
}
```

This endpoint enables you to fetch unstaking status of a specified address at a specified validator.

*HTTP Request*

`GET http://jadepool.example/api/v1/stake/:coinName/delegators/:delegator/unstaking_delegations/:validator`

*Query Parameters*

Parameter | Description
--------- | ------- 
coinName | coin name
delegator | delegate from address
validator | validator

*Response Result*

Value | Type | Description
--------- | ------- | ---------
delegator | string | the address that performs delegation
validator | string | validator delegated to
creationHeight | string | the block height while undelegation requested
completionTime | string | undelegation completion time
initialNativeAmount | string | reuqested amount to undelegate in token unit
nativeAmount | string | real-time actual amount that will be undelegated in token unit
initialAmount | string | reuqested amount to undelegate in main token unit
amount | string | real-time actual amount that will be undelegated in main token unit

### Fetch Address Validators

> Request "Fetch Address Validators" API returns JSON structured like this:

```json
{
    "code": 0,
    "status": 0,
    "message": "OK",
    "crypto": "ecc",
    "timestamp": 1556018616498,
    "sig": {
        "r": "xtLflM34H+li5bMTktfKGXMLUXdNlboDAaYc/D4Oafg=",
        "s": "SKIwC0dGZihYvY56jJPCz1BIItF9ClVSxAU9XQmE/t0=",
        "v": 27
    },
    "result": [
        {
            "name": "node0",
            "address": "cosmosvaloper1z7jw2deueg37nd6v3flmn4wy2v3nhz55tsx4y9",
            "pubkey": "cosmosvalconspub1zcjduepqmp75ajy7jm0mqttc2d23ks54dn83yfshxd3038r4ejldcjcd0atsnwxnyr",
            "jailed": false,
            "status": 2,
            "shares": "102009000.000000000000000000",
            "delegationAmount": "102009000",
            "minSelfDelegation": "1",
            "commission": {
                "rate": 0.1,
                "maxRate": 0.2,
                "maxChangeRate": 0.01,
                "updateAt": "2019-04-23T04:41:34.202Z"
            }
        },
        {
            "name": "node1",
            "address": "cosmosvaloper1wpw2nrq850mwgrhteh3jwc2g7w9led2hervrfu",
            "pubkey": "cosmosvalconspub1zcjduepqrjklsgkc8jeusfkjmrltlvz0s9cxe7vxr7uajpx2v0rfn0zvgtxqw5mwhj",
            "jailed": true,
            "status": 0,
            "shares": "2726262.626262626262626261",
            "delegationAmount": "2699000",
            "minSelfDelegation": "100000",
            "commission": {
                "rate": 0.1,
                "maxRate": 0.2,
                "maxChangeRate": 0.01,
                "updateAt": "2019-04-23T04:41:44.782Z"
            }
        }
    ]
}
```

This endpoint enables you to fetch validators an address delegated to.

*HTTP Request*

`GET http://jadepool.example/api/v1/stake/:coinName/delegators/:delegator/validators`

*Query Parameters*

Parameter | Description
--------- | ------- 
coinName | coin name
delegator | delegate from address

*Response Result*

Value | Type | Description
--------- | ------- | ---------
name | string | validator name
address | string | validator's address
pubkey | string | validator's public key
jailed | boolean | if the validator is jailed
status | number | validator's status
shares | string | validator's shares
delegationAmount | string | amount delegated to validator
minSelfDelegation | string | amount delegated to validator by itself
commission | object | validator's commission
rate | float | validator's commission rate
maxRate | float | validator's commission maxRate
maxChangeRate | float | validator's commission maxChangeRate
updateAt | string | validator info update time

### Set Reward Address

> Request "Set Reward Address" API returns JSON structured like this:

```json
{
    "code": 0,
    "status": 0,
    "message": "OK",
    "crypto": "ecc",
    "timestamp": 1556008386558,
    "sig": {
        "r": "4VxS8mGcX5Wml0B25qwT8B1vCUOZG1411KcKGOAiKYE=",
        "s": "fPkv8gI9HwqXecFeZaDvluaDF/TA+pN7At6cd9GBreI=",
        "v": 28
    },
    "result": {
        "data": {
            "timestampBegin": 0,
            "timestampFinish": 0
        },
        "id": "21",
        "coinName": "ATOM",
        "txid": null,
        "meta": null,
        "state": "init",
        "bizType": "SYSTEM_CALL",
        "type": "ATOM",
        "coinType": "ATOM",
        "to": "cosmos1hrnl5pugag20gnu8zy3604jetqrjaf60czkl8u",
        "value": "0",
        "confirmations": 0,
        "create_at": 1556008386549,
        "update_at": 1556008386551,
        "action": "set-reward-address",
        "actionArgs": [
            "cosmos1hrnl5pugag20gnu8zy3604jetqrjaf60czkl8u",
            "cosmos1hrnl5pugag20gnu8zy3604jetqrjaf60czkl8u"
        ],
        "actionResults": [],
        "from": "cosmos1hrnl5pugag20gnu8zy3604jetqrjaf60czkl8u",
        "n": 0,
        "fee": "0",
        "fees": [],
        "hash": "",
        "block": -1,
        "extraData": "",
        "memo": "",
        "sendAgain": false,
        "namespace": "Cosmos",
        "sid": "RPamVnMkXe2aIgElAAAB"
    }
}
```

This endpoint enables you to set address to claim reward to.

*HTTP Request*

`POST http://jadepool.example/api/v1/stake/:coinName/rewards/:delegator/address`

*Query Parameters*

Parameter | Description
--------- | ------- 
coinName | coin name
delegator | delegate from address

*Main Parameters*

Parameter | Required | Type | Description
--------- | ------- | --------- | -----------
data.sequence | no | number | unique sequence
data.address | yes | string | reward address

*Response Result*

Value | Type | Description
--------- | ------- | ---------
id | string | the order ID
coinName | string | unique token name
txid | string | transaction hash
meta | any | supplementary data
state | string | order state
bizType | string | order type
type | string | token type
from | string | transaction input
to | string | transaction output
value | string | transaction value
confirmations | number | number of transaction confirmations
fee | string | fee burnt for the transaction
fees | array | all types of fee burnt for the transaction
hash | string | transaction hash
block | number | the block transaction mined in
extraData | string | same as the extraData in request
memo | string | order note, editable on admin
sendAgain | boolean | if we suggest client to send the same request again

### Fetch Reward Address

> Request "Fetch Reward Address" API returns JSON structured like this:

```json
{
    "code": 0,
    "status": 0,
    "message": "OK",
    "crypto": "ecc",
    "timestamp": 1556018632885,
    "sig": {
        "r": "2fEarBGRKLM9KnEyjZX12yRJxPNScszopIxrL/IraQk=",
        "s": "QH/oBrjOoDHqL3TyR36lVRgcz6L8PmM5ToeezrxJOeQ=",
        "v": 27
    },
    "result": {
        "address": "cosmos1hrnl5pugag20gnu8zy3604jetqrjaf60czkl8u",
        "namespace": "Cosmos",
        "sid": "h2112M6bFQZImpdUAAAB"
    }
}
```

This endpoint enables you to fetch reward address.

*HTTP Request*

`GET http://jadepool.example/api/v1/stake/:coinName/rewards/:delegator/address`

*Query Parameters*

Parameter | Description
--------- | ------- 
coinName | coin name
delegator | delegate from address

*Response Result*

Value | Type | Description
--------- | ------- | ---------
address | string | the address that receives award

### Claim Reward

> Request "Claim Reward" API returns JSON structured like this:

```json
{
    "code": 0,
    "status": 0,
    "message": "OK",
    "crypto": "ecc",
    "timestamp": 1554801579264,
    "sig": {
        "r": "1q87ktmqvcZrB8ID1nGSI3JlM0Zv5m12ldsiCNa5tVg=",
        "s": "XlxYTIf7xYV6J/SrrReVtJIY8Nip1lCsxSVTZSfhGUo=",
        "v": 28
    },
    "result": {
        "data": {
            "timestampBegin": 0,
            "timestampFinish": 0
        },
        "id": "1969",
        "coinName": "ATOM",
        "txid": null,
        "meta": null,
        "state": "init",
        "bizType": "SYSTEM_CALL",
        "type": "ATOM",
        "coinType": "ATOM",
        "to": "cosmos1hrnl5pugag20gnu8zy3604jetqrjaf60czkl8u",
        "value": "0",
        "confirmations": 0,
        "create_at": 1554801579255,
        "update_at": 1554801579257,
        "action": "claim-reward",
        "actionArgs": [
            "cosmos1hrnl5pugag20gnu8zy3604jetqrjaf60czkl8u",
            "cosmosvaloper1wpw2nrq850mwgrhteh3jwc2g7w9led2hervrfu"
        ],
        "actionResults": [],
        "from": "cosmos1hrnl5pugag20gnu8zy3604jetqrjaf60czkl8u",
        "n": 0,
        "fee": "0",
        "fees": [],
        "hash": "",
        "block": -1,
        "extraData": "",
        "memo": "",
        "sendAgain": false,
        "namespace": "Cosmos",
        "sid": "h-02Y6BZGFDuZyWzAAAF"
    }
}
```

This endpoint enables you to claim reward at all or specified validators.

*HTTP Request*

`POST http://jadepool.example/api/v1/stake/:coinName/rewards/:delegator/claim`

*Query Parameters*

Parameter | Description
--------- | ------- 
coinName | coin name
delegator | delegate from address

*Main Parameters*

Parameter | Required | Type | Description
--------- | ------- | --------- | -----------
data.sequence | no | number | unique sequence
data.validator | no | string | specified validator to claim reward from, claim reward from all if not passed

*Response Result*

Value | Type | Description
--------- | ------- | ---------
id | string | the order ID
coinName | string | unique token name
txid | string | transaction hash
meta | any | supplementary data
state | string | order state
bizType | string | order type
type | string | token type
from | string | transaction input
to | string | transaction output
value | string | transaction value
confirmations | number | number of transaction confirmations
fee | string | fee burnt for the transaction
fees | array | all types of fee burnt for the transaction
hash | string | transaction hash
block | number | the block transaction mined in
extraData | string | same as the extraData in request
memo | string | order note, editable on admin
sendAgain | boolean | if we suggest client to send the same request again

### Fetch Overall Reward

> Request "Fetch Overall Reward" API returns JSON structured like this:

```json
{
    "code": 0,
    "status": 0,
    "message": "OK",
    "crypto": "ecc",
    "timestamp": 1556018660578,
    "sig": {
        "r": "KpNI22ydps6qHjkWlKTr+OntLzXPV8uM7ayjALtI6ls=",
        "s": "H4r6Hcm+qmN7wX1mdtzMGxIjdFBrOQgrjX4wjxRvQPg=",
        "v": 28
    },
    "result": [
        {
            "delegator": "cosmos1hrnl5pugag20gnu8zy3604jetqrjaf60czkl8u",
            "nativeName": "stake",
            "nativeAmount": "17.526742169806709000",
            "coinName": "ATOM",
            "amount": "0.00001752674216980671"
        }
    ]
}
```

This endpoint enables you to fetch reward status at all validators.

*HTTP Request*

`GET http://jadepool.example/api/v1/stake/:coinName/rewards/:delegator/profits`

*Query Parameters*

Parameter | Description
--------- | ------- 
coinName | coin name
delegator | delegate from address

*Response Result*

Value | Type | Description
--------- | ------- | ---------
delegator | string | address that performs delegation
nativeName | string | token
nativeAmount | string | reward amount in token unit
coinName | string | main token
amount | string | reward amount in main token unit

### Reward At Validator

> Request "Reward At Validator" API returns JSON structured like this:

```json
{
    "code": 0,
    "status": 0,
    "message": "OK",
    "crypto": "ecc",
    "timestamp": 1554191851801,
    "sig": {
        "r": "myLCMqne33PLyA6PKqHRS/PrAbhFWwuEOt/+RxjQIyk=",
        "s": "Tb3p62GM1aA1x986whPjFjh0K4PC01xNI+aBIElB8E4=",
        "v": 27
    },
    "result": [
        {
            "delegator": "cosmos1hrnl5pugag20gnu8zy3604jetqrjaf60czkl8u",
            "validator": "cosmosvaloper1z7jw2deueg37nd6v3flmn4wy2v3nhz55tsx4y9",
            "nativeName": "stake",
            "nativeAmount": "16.877883495144000000",
            "coinName": "ATOM",
            "amount": "0.000016877883495144"
        }
    ]
}
```

This endpoint enables you to fetch reward at specified validator.

*HTTP Request*

`GET http://jadepool.example/api/v1/stake/:coinName/rewards/:delegator/profits/:validator`

*Query Parameters*

Parameter | Description
--------- | ------- 
coinName | coin name
delegator | delegate from address
validator | validator

*Response Result*

Value | Type | Description
--------- | ------- | ---------
delegator | string | address that performs delegation
validator | string | validator delegated to
nativeName | string | token
nativeAmount | string | reward amount in token unit
coinName | string | main token
amount | string | reward amount in main token unit

# V2 API

<aside class="notice">
All timestamps are in millisecond.
</aside>

<aside class="notice">
All main parameters in POST API should be wraped in "data" object
</aside>

## Address

### Create Multiple Addresses

> Request "Create Multiple Addresses" API returns JSON structured like this:

```json
{
    "code": 0,
    "message": "OK",
    "crypto": "ecc",
    "timestamp": 1556009381747,
    "sig": {
        "r": "zSKIe50E0PEQ0QEP2ktr+rVTSwzHFLHXCbDcZLwbEYE=",
        "s": "O2V+rrLev9PHloM0HXknbn43qc6yGAQ+CYZGTVihON8=",
        "v": 28
    },
    "result": [
        {
            "id": 284,
            "appid": "pri",
            "type": "ETH",
            "address": "0x352c3d587080ae186c6037ccf786ea1c71ed87be",
            "state": "used",
            "mode": "deposit",
            "create_at": 1556009381632,
            "update_at": 1556009381688,
            "namespace": "ETH"
        },
        {
            "id": 285,
            "appid": "pri",
            "type": "ETH",
            "address": "0xe1f3247967f9e51a0cc733f8191166f5d6a8b30d",
            "state": "used",
            "mode": "deposit",
            "create_at": 1556009381730,
            "update_at": 1556009381744,
            "namespace": "ETH"
        }
    ]
}
```

This endpoint enables you to request multiple new addresses at once.

*HTTP Request*

`POST http://jadepool.example/api/v2/address/:coinName/batchnew`

*Query Parameters*

Parameter | Description 
--------- | --------- 
coinName | coin name

*Main Parameters*

Parameter | Required | Type | Description
--------- | ------- | --------- | -----------
data.amount | yes | number | number of addresses, range from 1 to 500.
data.mode | yes | string | address mode: 'auto', 'deposit', 'deposit_memo', 'normal'

*Response Result*

Value | Type | Description
--------- | ------- | ---------
id | number | unique ID in Jadepool's database
appid | string | application ID, configurable on admin
type | string | token type of the new address
address | string | the new address
state | string | this value does not mean anything for now
mode | string | address mode

### Create Single Address

> Request "Create Single Address" API returns JSON structured like this:

```json
{
    "code": 0,
    "message": "OK",
    "crypto": "ecc",
    "timestamp": 1556009180055,
    "sig": {
        "r": "nIYVH6S5qz2gIHtxjWzp+muM4CVaqU0et0NCbCamjKs=",
        "s": "WR/OEB8RMPRXDQopWfpwc2Npki+b82ASsewONMpIR5I=",
        "v": 27
    },
    "result": {
        "id": 283,
        "appid": "pri",
        "type": "ETH",
        "address": "0xf7d862f2b43d3bff3444e6c3dd9aceef837f817d",
        "state": "used",
        "mode": "deposit",
        "create_at": 1556009180037,
        "update_at": 1556009180052,
        "namespace": "ETH",
        "sid": "j5D28yAyhO02C-_6AAAA"
    }
}
```

This endpoint enables you to request single new address.

*HTTP Request*

`POST http://jadepool.example/api/v2/address/:coinName/new`

*Query Parameters*

Parameter | Description 
--------- | --------- 
coinName | coin name

*Main Parameters*

Parameter | Required | Type | Description
--------- | ------- | --------- | -----------
data.mode | yes | string | address mode: 'auto', 'deposit', 'deposit_memo', 'normal'

*Response Result*

Value | Type | Description
--------- | ------- | ---------
id | number | unique ID in Jadepool's database
appid | string | application ID, configurable on admin
type | string | token type of the new address
address | string | the new address
state | string | this value does not mean anything for now
mode | string | address mode

### Validate Address

> Request "Validate Address" API returns JSON structured like this:

```json
{
    "code": 0,
    "message": "OK",
    "crypto": "ecc",
    "timestamp": 1556009541845,
    "sig": {
        "r": "RGw+vmBSAFIknSp4sYPLLlKAv0XNFt+3Xz5gTnttIFA=",
        "s": "V+zMC77GTyh2igr8eMbl0JtpM1hu9ZisJOgYGhmW5hQ=",
        "v": 28
    },
    "result": {
        "address": "0x352c3d587080ae186c6037ccf786ea1c71ed87be",
        "valid": true,
        "namespace": "ETH",
        "sid": "j5D28yAyhO02C-_6AAAA"
    }
}
```

This endpoint enables you to verify if an address is valid for specified token.

*HTTP Request*

`POST http://jadepool.example/api/v2/address/:coinName/verify`

*Query Parameters*

Parameter | Description 
--------- | --------- 
coinName | coin name

*Main Parameters*

Parameter | Required | Type | Description
--------- | ------- | --------- | -----------
data.address | yes | string | address to validate

*Response Result*

Value | Type | Description
--------- | ------- | ---------
address | string | the address to validate
valid | boolean | the address is valid or not

### Force Free

> Request "Force Free" API returns JSON structured like this:

```json
{
    "code": 0,
    "message": "OK",
    "crypto": "ecc",
    "timestamp": 1556010383578,
    "sig": {
        "r": "wBs2UnJLy16lww2mrp8oi71W/Qi1101ElkIDFKGjq9c=",
        "s": "WJYTn/vjf0y/Z3DdkJ/7XAiqvylKI/DhHP0mlMLDg7I=",
        "v": 27
    },
    "result": {
        "data": {
            "timestampBegin": 0,
            "timestampFinish": 0
        },
        "_id": "5cbed58f8d1607c10ddddf93",
        "id": "6515",
        "coinName": "ETH",
        "txid": null,
        "meta": null,
        "state": "init",
        "bizType": "SWEEP_INTERNAL",
        "type": "ETH",
        "coinType": "ETH",
        "from": "0xa268f4ae690ac1421775568d783439c7c1151749",
        "to": "0xbf0aa791e34d3af906050fcb713305c1d92880af",
        "value": "0.001",
        "sequence": 1,
        "confirmations": 0,
        "create_at": 1556010383570,
        "update_at": 1556010383571,
        "actionArgs": [],
        "actionResults": [],
        "n": 0,
        "fee": "0",
        "fees": [],
        "hash": "",
        "block": -1,
        "extraData": "",
        "memo": "",
        "sendAgain": false,
        "namespace": "ETH",
        "sid": "j5D28yAyhO02C-_6AAAA"
    }
}
```

This endpoint enables you to transfer tokens from a deposit address to the main hot wallet address.

*HTTP Request*

`POST http://jadepool.example/api/v2/address/:coinName/free`

*Query Parameters*

Parameter | Description 
--------- | --------- 
coinName | coin name

*Response Result*

Value | Type | Description
--------- | ------- | ---------
id | string | the order ID
coinName | string | unique token name
txid | string | transaction hash
meta | any | supplementary data
state | string | order state
bizType | string | order type
type | string | token type
from | string | transaction input
to | string | transaction output
value | string | transaction value
confirmations | number | number of transaction confirmations
fee | string | fee burnt for the transaction
fees | array | all types of fee burnt for the transaction
hash | string | transaction hash
block | number | the block transaction mined in
extraData | string | same as the extraData in request
memo | string | order note, editable on admin
sendAgain | boolean | if we suggest client to send the same request again

### Force Obtain

> Request "Force Obtain" API returns JSON structured like this:

```json
{
    "code": 0,
    "message": "OK",
    "crypto": "ecc",
    "timestamp": 1556010383578,
    "sig": {
        "r": "wBs2UnJLy16lww2mrp8oi71W/Qi1101ElkIDFKGjq9c=",
        "s": "WJYTn/vjf0y/Z3DdkJ/7XAiqvylKI/DhHP0mlMLDg7I=",
        "v": 27
    },
    "result": {
        "data": {
            "timestampBegin": 0,
            "timestampFinish": 0
        },
        "_id": "5cbed58f8d1607c10ddddf93",
        "id": "6515",
        "coinName": "ETH",
        "txid": null,
        "meta": null,
        "state": "init",
        "bizType": "SWEEP_INTERNAL",
        "type": "ETH",
        "coinType": "ETH",
        "from": "0xa268f4ae690ac1421775568d783439c7c1151749",
        "to": "0xbf0aa791e34d3af906050fcb713305c1d92880af",
        "value": "0.001",
        "sequence": 1,
        "confirmations": 0,
        "create_at": 1556010383570,
        "update_at": 1556010383571,
        "actionArgs": [],
        "actionResults": [],
        "n": 0,
        "fee": "0",
        "fees": [],
        "hash": "",
        "block": -1,
        "extraData": "",
        "memo": "",
        "sendAgain": false,
        "namespace": "ETH",
        "sid": "j5D28yAyhO02C-_6AAAA"
    }
}
```

This endpoint enables you to transfer tokens from the main hot wallet address to a deposit address.

*HTTP Request*

`POST http://jadepool.example/api/v2/address/:coinName/obtain`

*Query Parameters*

Parameter | Description 
--------- | --------- 
coinName | coin name

*Main Parameters*

Parameter | Required | Type | Description
--------- | ------- | --------- | -----------
data.address | yes | string | address to validate
data.sequence | yes | number | unique request sequence
data.value | yes | string | force obtain value

*Response Result*

Value | Type | Description
--------- | ------- | ---------
id | string | the order ID
coinName | string | unique token name
txid | string | transaction hash
meta | any | supplementary data
state | string | order state
bizType | string | order type
type | string | token type
from | string | transaction input
to | string | transaction output
value | string | transaction value
confirmations | number | number of transaction confirmations
fee | string | fee burnt for the transaction
fees | array | all types of fee burnt for the transaction
hash | string | transaction hash
block | number | the block transaction mined in
extraData | string | same as the extraData in request
memo | string | order note, editable on admin
sendAgain | boolean | if we suggest client to send the same request again

### Fetch Incomplete Orders

> Request "Fetch Incomplete Orders" API returns JSON structured like this:

```json
{
    "code": 0,
    "message": "OK",
    "crypto": "ecc",
    "timestamp": 1556017079643,
    "sig": {
        "r": "EWxRFPkVClfS6Y8hz4kyarkhEbvOQLgcDY6GR+D8zdQ=",
        "s": "TavLCYToUrn3778oNslgPXuMu4j/Jsd+8hNbzN3dtZI=",
        "v": 27
    },
    "result": [
        {
            "_id": "5cbeefb353ee1205fdd12284",
            "id": "6515",
            "coinName": "LTC",
            "txid": null,
            "meta": null,
            "state": "init",
            "bizType": "WITHDRAW",
            "type": "LTC",
            "coinType": "LTC",
            "from": "mtRMmr6opfJMakQDxL2Dzv1KeQRt9VLRU8",
            "to": "mrsUG77kvGMXxDwntoeREUmgmPheX2o1F8",
            "value": "0.001",
            "sequence": 2,
            "confirmations": 0,
            "create_at": 1556017075658,
            "update_at": 1556017075660,
            "actionArgs": [],
            "actionResults": [],
            "n": 0,
            "fee": "0",
            "fees": [],
            "data": {
                "timestampBegin": 0,
                "timestampFinish": 0
            },
            "hash": "",
            "block": -1,
            "extraData": "",
            "memo": "",
            "sendAgain": false
        }
    ]
}
```

This endpoint enables you to fetch all incomplete orders that are of state in: 'init', 'holding', 'online', 'pending'.

*HTTP Request*

`GET http://jadepool.example/api/v2/address/:coinName/:address/orders[/:state]`

*Query Parameters*

Parameter | Description 
--------- | --------- 
coinName | coin name
address | related address
state | optional, can be one of the incomplete order states: 'init', 'holding', 'online', 'pending'

*Response Result*

Value | Type | Description
--------- | ------- | ---------
id | string | the order ID
coinName | string | unique token name
txid | string | transaction hash
meta | any | supplementary data
state | string | order state
bizType | string | order type
type | string | token type
from | string | transaction input
to | string | transaction output
value | string | transaction value
confirmations | number | number of transaction confirmations
fee | string | fee burnt for the transaction
fees | array | all types of fee burnt for the transaction
hash | string | transaction hash
block | number | the block transaction mined in
extraData | string | same as the extraData in request
memo | string | order note, editable on admin
sendAgain | boolean | if we suggest client to send the same request again

### Fetch Address Balance

> Request "Fetch Address Balance" API returns JSON structured like this:

```json
{
    "code": 0,
    "message": "OK",
    "crypto": "ecc",
    "timestamp": 1556016915285,
    "sig": {
        "r": "RpVC2Tm8m9LJ77mJcoIBwNLtbb/WQyOl2jqA4hBbwN0=",
        "s": "fUetmUE8uAIDRnXeTSBeM90sIj4pkqABdurQQb6/jxU=",
        "v": 28
    },
    "result": {
        "balance": "0",
        "namespace": "LTC",
        "sid": "viqU_GCM4-wg3P1kAAAD"
    }
}
```

This endpoint enables you to fetch balance of a specified address.

*HTTP Request*

`GET http://jadepool.example/api/v2/address/:coinName/:address/balance`

*Query Parameters*

Parameter | Description 
--------- | --------- 
coinName | coin name
address | related address

*Response Result*

Value | Type | Description
--------- | ------- | ---------
balance | string | the address' balance on blockchain

### Fetch Address Info

> Request "Fetch Address Info" API returns JSON structured like this:

```json
{
    "code": 0,
    "message": "OK",
    "crypto": "ecc",
    "timestamp": 1556016629310,
    "sig": {
        "r": "LPVc8+D2jDINOpLrUepizbhNU+CnKjIb1eH/yowP6nc=",
        "s": "ZW/mtl5bSIgejcXVSNVRa+/5IG7vOk7J6v0pYvbApzA=",
        "v": 28
    },
    "result": {
        "id": 288,
        "appid": "pri",
        "type": "BTC",
        "address": "moUQrRi7cKNjskr3pnKqjXbZZ5cH2dWuiG",
        "state": "used",
        "mode": "deposit",
        "create_at": 1556016613697,
        "update_at": 1556016613733,
        "namespace": "BTC",
        "sid": "XDd9EeHVqN08oGVOAAAC"
    }
}
```

This endpoint enables you to fetch details of a specified address.

*HTTP Request*

`GET http://jadepool.example/api/v2/address/:coinName/:address`

*Query Parameters*

Parameter | Description 
--------- | --------- 
coinName | coin name
address | related address

*Response Result*

Value | Type | Description
--------- | ------- | ---------
id | number | unique ID in Jadepool's database
appid | string | application ID, configurable on admin
type | string | token type of the new address
address | string | the new address
state | string | this value does not mean anything for now
mode | string | address mode

## Audit

### Fetch Audit Order

> Request "Fetch Audit Order" API returns JSON structured like this:

```json
{
    "code": 0,
    "status": 0,
    "message": "OK",
    "crypto": "ecc",
    "hash": "sha3",
    "sort": "key-alphabet",
    "encode": "base64",
    "timestamp": 1583808224613,
    "sig": {
        "r": "t90UoLKHNXTBtmw5yZdO7FJ/gcS60U4JKMgpYnCCo0o=",
        "s": "K4NcSIXRdJph2Yw8mVgSmFnp5689zfc6SSO65Rg8T+8=",
        "v": 28
    },
    "result": {
        "wallet": "default",
        "blocknumber": 214,
        "calculated": true,
        "deposit_total": "0.01",
        "deposit_num": 1,
        "withdraw_total": "0.2",
        "withdraw_num": 1,
        "revert_total": "0",
        "revert_num": 0,
        "refund_total": "0",
        "refund_num": 0,
        "system_call_total": "0",
        "system_call_num": 0,
        "delegate_total": "0",
        "delegate_num": 0,
        "undelegate_total": "0",
        "undelegate_num": 0,
        "principal_fund_total": "0",
        "principal_fund_num": 0,
        "interest_fund_total": "0",
        "interest_fund_num": 0,
        "sweep_total": "0",
        "sweep_num": 0,
        "sweep_internal_total": "0",
        "sweep_internal_num": 0,
        "airdrop_total": "0",
        "airdrop_num": 0,
        "recharge_total": "0",
        "recharge_num": 0,
        "recharge_internal_total": "0",
        "recharge_internal_num": 0,
        "recharge_unexpected_total": "0.601343293996",
        "recharge_unexpected_num": 3,
        "recharge_special_total": "0",
        "recharge_special_num": 0,
        "failed_withdraw_num": 0,
        "failed_sweep_num": 0,
        "failed_sweep_internal_num": 4,
        "failed_refund_num": 0,
        "failed_system_call_num": 0,
        "failed_delegate_num": 0,
        "failed_undelegate_num": 0,
        "fees": [
            {
                "withdraw_fee": "0.0101",
                "refund_fee": "0",
                "sweep_fee": "0",
                "sweep_internal_fee": "0",
                "system_call_fee": "0",
                "delegate_fee": "0",
                "undelegate_fee": "0",
                "failed_withdraw_fee": "0",
                "failed_refund_fee": "0",
                "failed_sweep_fee": "0",
                "failed_sweep_internal_fee": "0.004330699234",
                "failed_system_call_fee": "0",
                "failed_delegate_fee": "0",
                "failed_undelegate_fee": "0",
                "_id": "5e66fee06b50acfa7ec50fec",
                "fee_type": "KSM"
            }
        ],
        "chainKey": "Kusama",
        "type": "KSM",
        "timestamp": 1583808224613,
        "appid": "pri",
        "create_at": "2020-03-10T02:43:44.733Z",
        "update_at": "2020-03-10T02:43:44.833Z",
        "__v": 1,
        "calc_order_num": 13,
        "id": "5e66fee041db0dfa7f558411"
    }
}
```

This endpoint enables you to fetch audit order.

*HTTP Request*

`GET http://jadepool.example/api/v2/audits/:id`

*Query Parameters*

Parameter | Description
--------- | -------
id | audit order ID

*Response Result*

Value | Type | Description
--------- | ------- | ---------
calculated | boolean | if the auditing is finished
deposit_total | string | total deposit token value
deposit_num | number | total deposit times
withdraw_total | string | total withdraw token value
withdraw_num | number | total withdraw times
sweep_total | string | total hot-to-cold token value
sweep_num | number | total hot-to-cold times 
sweep_internal_total | string | total internal-out token value
sweep_internal_num | number | total internal-out times
airdrop_total | string | total airdrop token value
airdrop_num | number | total airdrop times
recharge_total | string | total cold-to-hot token value
recharge_num | number | total cold-to-hot times
recharge_internal_total | string | total internal-in token value
recharge_internal_num | number | total internal-in times
recharge_unexpected_total | string | total unexpected-in token value
recharge_unexpected_num | number | total unexpected-in times
recharge_special_total | string | total special-in token value
recharge_special_num | number | total special-in times
failed_withdraw_num | number | total failed withdrawal times
failed_sweep_num | number | total failed hot-to-cold times
failed_sweep_internal_num | number | total failed internal-out times
fees | array | all type of fees burned
withdraw_fee | string | fees burned for withdrawal
refund_fee | string | fees burned for refund
sweep_fee | string | fees burned for hot-to-cold
sweep_internal_fee | string | fees burned for internal-out
system_call_fee | string | fees burned for all system_call txs
delegate_fee | string | fees burned for staking
undelegate_fee | string | fees burned for unstaking
failed_withdraw_fee | string | fees burned for failed withdrawal
failed_refund_fee | string | fees burned for failed refund
failed_sweep_fee | string | fees burned for refund hot-to-cold
failed_sweep_internal_fee | string | fees burned for refund internal-out
failed_system_call_fee | string | fees burned for all failed system_call txs
failed_delegate_fee | string | fees burned for failed delegation
failed_undelegate_fee | string | fees burned for failed undelegation
type | string | the audited token type
timestamp | number | the audited timestamp
blocknumber | number | corresponding block near the audited timestamp
appid | string | application ID
calc_order_num | number | audited order number
last | string | audit order ID of the previous audit
id | string | audit order ID of the current audit

### Audited Orders

> Request "Audited Orders" API returns JSON structured like this:

```json
{
  "code": 0,
  "status": 0,
  "message": "OK",
  "crypto": "ecc",
  "hash": "sha3",
  "sort": "key-alphabet",
  "encode": "base64",
  "timestamp": 1583897683128,
  "sig": {
    "r": "oQJOtaonOe3NvycDQXNXrjsfS3pIdgRo1imh+9o5Tv8=",
    "s": "eMrUrwaNs4MUFb3d+Y1ASYTN4ec46pHsgc4cvA5PnS8=",
    "v": 28
  },
  "result": [
    {
      "_id": "5e65b0c90c34df59da78c15c",
      "id": "6317",
      "coinName": "KSM",
      "txid": "0xea7876c8121bc7db0d7fd4ed005d267702a0e0fe6df3b9bb0533b0fc28faaae8",
      "meta": "13",
      "appid": "test",
      "wallet": "default",
      "state": "done",
      "bizType": "WITHDRAW",
      "type": "KSM",
      "coinType": "KSM",
      "from": "EkFskcVG8xm7mbASU29VmiJTuFuSBodNrs9xjbUKNHKqmHP",
      "to": "FVCSCiM5TricGwJ2TZ84mRGLgEcNAKfXUCd2fgB2MFJkCxd",
      "value": "0.2",
      "sequence": 1583722697437,
      "confirmations": 3750,
      "create_at": 1583722697549,
      "update_at": 1583897670562,
      "action": "withdraw-from-hotwallet",
      "actionArgs": [
        
      ],
      "actionResults": [
        
      ],
      "postHandlers": [
        
      ],
      "n": 3,
      "fee": "0.02",
      "fees": [
        {
          "_id": "5e660a42667364a489bdae6a",
          "amount": "0.02",
          "coinName": "KSM",
          "nativeAmount": "0",
          "nativeName": ""
        }
      ],
      "data": {
        "timestampBegin": 1583722702743,
        "timestampFinish": 1583745602955,
        "timestampHandle": 1583722702283,
        "failedBlockNumber": null,
        "failedTimestamp": null,
        "goodBlockNumber": 1373448,
        "goodTimestamp": 1583745597170
      },
      "hash": "0xea7876c8121bc7db0d7fd4ed005d267702a0e0fe6df3b9bb0533b0fc28faaae8",
      "txid_rawtx": "",
      "block": 1369700,
      "blockHash": "0x226b6eb54e524ad1ba2f1d11a4b9e7d0171a42dcb5b0ffa184f35e3b8448a959",
      "blockTimestamp": 1583722620000,
      "extraData": "",
      "memo": "",
      "sendAgain": false,
      "relatedOrder": "",
      "relatedIssueRecords": [
        
      ]
    },
    {
      "_id": "5e65b0f490acb159d97b45c5",
      "id": "6318",
      "coinName": "KSM",
      "txid": "0xea7876c8121bc7db0d7fd4ed005d267702a0e0fe6df3b9bb0533b0fc28faaae8",
      "meta": null,
      "appid": "pri",
      "wallet": "default",
      "state": "done",
      "bizType": "DEPOSIT",
      "type": "KSM",
      "coinType": "KSM",
      "from": "EkFskcVG8xm7mbASU29VmiJTuFuSBodNrs9xjbUKNHKqmHP",
      "to": "FVCSCiM5TricGwJ2TZ84mRGLgEcNAKfXUCd2fgB2MFJkCxd",
      "value": "0.2",
      "sequence": 15837227406189476,
      "confirmations": 3749,
      "create_at": 1583722740617,
      "update_at": 1583897670562,
      "actionArgs": [
        
      ],
      "actionResults": [
        
      ],
      "postHandlers": [
        
      ],
      "n": 3,
      "fee": "0.02",
      "fees": [
        {
          "_id": "5e660a3d667364a489bdae63",
          "amount": "0.02",
          "coinName": "KSM",
          "nativeAmount": "0",
          "nativeName": ""
        }
      ],
      "data": {
        "timestampBegin": 1583722740618,
        "timestampFinish": 1583745597398,
        "timestampHandle": 1583722740618
      },
      "hash": "0xea7876c8121bc7db0d7fd4ed005d267702a0e0fe6df3b9bb0533b0fc28faaae8",
      "txid_rawtx": "",
      "block": 1369700,
      "blockHash": "0x226b6eb54e524ad1ba2f1d11a4b9e7d0171a42dcb5b0ffa184f35e3b8448a959",
      "blockTimestamp": 1583722620000,
      "extraData": "",
      "memo": "",
      "sendAgain": false,
      "relatedOrder": "",
      "relatedIssueRecords": [
        
      ]
    }
  ]
}
```

This endpoint enables you to fetch orders in specified audit.

*HTTP Request*

`GET http://jadepool.example/api/v2/audits/:id/orders`

*Query Parameters*

Parameter | Description
--------- | ------- 
id | audit order ID

*Response Result*

Value | Type | Description
--------- | ------- | ---------
id | string | the order ID
coinName | string | unique token name
txid | string | transaction hash
meta | any | supplementary data
state | string | order state
bizType | string | order type
type | string | token type
from | string | transaction input
to | string | transaction output
value | string | transaction value
confirmations | number | number of transaction confirmations
fee | string | fee burnt for the transaction
fees | array | all types of fee burnt for the transaction
hash | string | transaction hash
block | number | the block transaction mined in
extraData | string | same as the extraData in request
memo | string | order note, editable on admin
sendAgain | boolean | if we suggest client to send the same request again
data | object | latest info of the transaction

## Order

### Fetch Order

> Request "fetch Order" API returns JSON structured like this:

```json
{
    "code": 0,
    "message": "OK",
    "crypto": "ecc",
    "timestamp": 1556009989295,
    "sig": {
        "r": "b4uHdTjVe72esYVTLhIami5xz/oNcdPKQLRVorNGoAk=",
        "s": "dzMrG9qeLixaq7B+0qY5FhTWR62vbv3nH3ey0mg03yM=",
        "v": 27
    },
    "result": {
        "_id": "5cbece338d1607c10ddddbcd",
        "id": "6513",
        "coinName": "ETH",
        "txid": null,
        "meta": null,
        "state": "init",
        "bizType": "WITHDRAW",
        "type": "ETH",
        "coinType": "ETH",
        "from": "0xa268f4ae690ac1421775568d783439c7c1151749",
        "to": "0xbf0aa791e34d3af906050fcb713305c1d92880af",
        "value": "0.001",
        "confirmations": 0,
        "create_at": 1556008499740,
        "update_at": 1556008499742,
        "actionArgs": [],
        "actionResults": [],
        "n": 0,
        "fee": "0",
        "fees": [],
        "data": {
            "timestampBegin": 0,
            "timestampFinish": 0
        },
        "hash": "",
        "block": -1,
        "extraData": "",
        "memo": "",
        "sendAgain": false
    }
}
```

This endpoint enables you to fetch any order details from Jadepool database.

*HTTP Request*

`GET http://jadepool.example/api/v2/orders/:id`

*Query Parameters*

Parameter | Description
--------- | ------- 
id | order ID

*Response Result*

Value | Type | Description
--------- | ------- | ---------
id | string | the order ID
coinName | string | unique token name
txid | string | transaction hash
meta | any | supplementary data
state | string | order state
bizType | string | order type
type | string | token type
from | string | transaction input
to | string | transaction output
value | string | transaction value
confirmations | number | number of transaction confirmations
fee | string | fee burnt for the transaction
fees | array | all types of fee burnt for the transaction
hash | string | transaction hash
block | number | the block transaction mined in
extraData | string | same as the extraData in request
memo | string | order note, editable on admin
sendAgain | boolean | if we suggest client to send the same request again
data | object | latest info of the transaction

## Wallet

### Fetch Wallet Status

> Request "Fetch wallet Status" API returns JSON structured like this:

```json
{
    "code": 0,
    "message": "OK",
    "crypto": "ecc",
    "timestamp": 1556010241947,
    "sig": {
        "r": "FzPkBS6BgMRJu/8OLd5jq+JKTBXHEr6rXo5vx/MJZd8=",
        "s": "SXiyJ+eBkOrIF206dJ24IKrZlRgDqe37kOrozaalNCA=",
        "v": 28
    },
    "result": {
        "balance": "0.01",
        "balanceAvailable": "0",
        "balanceUnavailable": "0.01",
        "update_at": 1556010241943,
        "addressesWithBalance": 1,
        "namespace": "ETH",
        "sid": "j5D28yAyhO02C-_6AAAA"
    }
}
```

This endpoint enables you to fetch balance details of any enabled token.

*HTTP Request*

`GET http://jadepool.example/api/v2/wallet/:coinName/status`

*Query Parameters*

Parameter | Description
--------- | ------- 
coinName | coin name

*Response Result*

Value | Type | Description
--------- | ------- | ---------
balance | string | total balance
balanceAvailable | string | balance available for outgoing transfer
balanceUnavailable | string | balance unavailable for outgoing transfer
update_at | number | the balance update time
addressesWithBalance | number | number of addresses that have balance

### Request Withdrawal

> Request "Request Withdrawal" API returns JSON structured like this:

```json
{
    "code": 0,
    "message": "OK",
    "crypto": "ecc",
    "timestamp": 1556010383578,
    "sig": {
        "r": "wBs2UnJLy16lww2mrp8oi71W/Qi1101ElkIDFKGjq9c=",
        "s": "WJYTn/vjf0y/Z3DdkJ/7XAiqvylKI/DhHP0mlMLDg7I=",
        "v": 27
    },
    "result": {
        "data": {
            "timestampBegin": 0,
            "timestampFinish": 0
        },
        "_id": "5cbed58f8d1607c10ddddf93",
        "id": "6514",
        "coinName": "ETH",
        "txid": null,
        "meta": null,
        "state": "init",
        "bizType": "WITHDRAW",
        "type": "ETH",
        "coinType": "ETH",
        "from": "0xa268f4ae690ac1421775568d783439c7c1151749",
        "to": "0xbf0aa791e34d3af906050fcb713305c1d92880af",
        "value": "0.001",
        "sequence": 1,
        "confirmations": 0,
        "create_at": 1556010383570,
        "update_at": 1556010383571,
        "actionArgs": [],
        "actionResults": [],
        "n": 0,
        "fee": "0",
        "fees": [],
        "hash": "",
        "block": -1,
        "extraData": "",
        "memo": "",
        "sendAgain": false,
        "namespace": "ETH",
        "sid": "j5D28yAyhO02C-_6AAAA"
    }
}
```

This endpoint enables you to request withdrawal.

*HTTP Request*

`POST http://jadepool.example/api/v2/wallet/:coinName/withdraw`

*Query Parameters*

Parameter | Description 
--------- | --------- 
coinName | coin name

*Main Parameters*

Parameter | Required | Type | Description
--------- | ------- | --------- | -----------
data.to | yes | string | withdrawal address
data.value | yes | string | value to withdraw
data.sequence | yes | number | unique API nonce for each app ID
data.auth | no | string | authentication for coin, only for HSM
data.extraData | no | string | callback url

*Response Result*

Value | Type | Description
--------- | ------- | ---------
id | string | the order ID
coinName | string | unique token name
txid | string | transaction hash
meta | any | supplementary data
state | string | order state
bizType | string | order type
type | string | token type
from | string | transaction input
to | string | transaction output
value | string | transaction value
confirmations | number | number of transaction confirmations
fee | string | fee burnt for the transaction
fees | array | all types of fee burnt for the transaction
hash | string | transaction hash
block | number | the block transaction mined in
extraData | string | same as the extraData in request
memo | string | order note, editable on admin
sendAgain | boolean | if we suggest client to send the same request again
data | object | latest info of the transaction

### Audit For Specified Coin

> Request "Audit For Specified Coin" API returns JSON structured like this:

```json
{
    "code": 0,
    "message": "OK",
    "crypto": "ecc",
    "timestamp": 1556010708546,
    "sig": {
        "r": "8h5Ut8zNxXNY9ZNDNtcZfRdp/pGOBbieXSRPj3d1adI=",
        "s": "AUlde6nijZ9R3+pY4MxYdKFocViQYzkq0ar9X9lP/WI=",
        "v": 28
    },
    "result": {
        "type": "ETH",
        "id": "5cbecdb38d1607c10ddddb49",
        "blocknumber": 9921056,
        "timestamp": 1546425032000,
        "namespace": "ETH",
        "sid": "j5D28yAyhO02C-_6AAAA"
    }
}
```

This endpoint enables you to request audit with timestamp of your choice.

*HTTP Request*

`POST http://jadepool.example/api/v2/wallet/:coinName/audit`

*Query Parameters*

Parameter | Description 
--------- | --------- 
coinName | coin name

*Main Parameters*

Parameter | Required | Type | Description
--------- | ------- | --------- | -----------
data.audittime | yes | number | audit timestamp

*Response Result*

Value | Type | Description
--------- | ------- | ---------
type | string | the audited token type
id | string | audit order ID of the current/last audit
timestamp | number | the audited timestamp
blocknumber | number | corresponding block near the audited timestamp

## Wallets

### Audit For All Coins

> Request "Audit For All Coins" API returns JSON structured like this:

```json
{
    "code": 0,
    "message": "OK",
    "crypto": "ecc",
    "timestamp": 1556011203974,
    "sig": {
        "r": "r6Epdg/bkbDxZ5Enq6HBs06ZQGikLyij3VJ/SVJL0Ho=",
        "s": "X30CD6m/3kUBFkc2OCNz+939q029R7H/dnyusCyGo3U=",
        "v": 27
    },
    "result": [
        {
            "type": "ETH",
            "namespace": "ETH",
            "id": "5cbecdb38d1607c10ddddb49",
            "blocknumber": 9921056,
            "timestamp": 1546425032000,
            "sid": "j5D28yAyhO02C-_6AAAA"
        }
    ]
}
```

This endpoint enables you to request audit of all tokens.

**HTTP Request**

`POST http://jadepool.example/api/v2/wallets/audit`

*Main Parameters*

Parameter | Required | Type | Description
--------- | ------- | --------- | -----------
data.audittime | yes | number | audit timestamp

*Response Result*

Value | Type | Description
--------- | ------- | ---------
type | string | the audited token type
id | string | audit order ID of the current/last audit
timestamp | number | the audited timestamp
blocknumber | number | corresponding block near the audited timestamp

## Delegation

Same as [V1] (#delegation), except that sequence is required in every API.

# Callback

<aside class="notice">
All timestamps are in millisecond.
</aside>

## Order

> Order notification sends JSON structured like this:

```json
{  
   "code":0,
   "message":"OK",
   "crypto":"ecc",
   "timestamp":1557286096399,
   "sig":{  
      "r":"WiTkCfTdBCGWHsMdd0sLiOZ536Zo013xnfUxHtFMkEk=",
      "s":"WvJHFQucDaOIZ8HkpW3xJiCBXh1eoIdlw4Hh/n0IXDE=",
      "v":28
   },
   "result":{  
      "_id":"5cd24caaa57c8d32ee301222",
      "id":"108",
      "coinName":"NASH",
      "txid":"0x662c73a8361acee47a267f580a55da9d7bbc720bb070bfd25baf765f79c2f019",
      "meta":null,
      "state":"pending",
      "bizType":"WITHDRAW",
      "type":"ETH",
      "subType":"NASH",
      "coinType":"NASH",
      "from":"0x9b878f3c613c77b50ccebd67faf6d7afd46de8ae",
      "to":"0x9cf16ecf95d707bd19a8e7f96e4fd93bd66e8c46",
      "value":"0.2",
      "sequence":11,
      "confirmations":19,
      "create_at":1557286058120,
      "update_at":1557286084437,
      "actionArgs":[  

      ],
      "actionResults":[  

      ],
      "n":0,
      "fee":"0.00040597",
      "fees":[  
         {  
            "_id":"5cd24ccea57c8d32ee301247",
            "amount":"0.00040597",
            "coinName":"ETH",
            "nativeAmount":"0",
            "nativeName":""
         }
      ],
      "data":{  
         "timestampBegin":1557286074480,
         "timestampFinish":0,
         "nonce":932,
         "type":"Ethereum",
         "hash":"0x662c73a8361acee47a267f580a55da9d7bbc720bb070bfd25baf765f79c2f019",
         "blockNumber":8262212,
         "blockHash":"0x0c946f37a4c18935b75f9c7eb714c4d970f1d6d7be47158a5a29131d7f8bf6df",
         "from":[  
            {  
               "address":"0x9b878f3c613c77b50ccebd67faf6d7afd46de8ae",
               "value":"0.2",
               "asset":"NASH"
            }
         ],
         "to":[  
            {  
               "address":"0x9cf16ecf95d707bd19a8e7f96e4fd93bd66e8c46",
               "value":"0.2",
               "asset":"NASH"
            }
         ],
         "fee":"0.00040597",
         "confirmations":19
      },
      "hash":"0x662c73a8361acee47a267f580a55da9d7bbc720bb070bfd25baf765f79c2f019",
      "block":8262212,
      "extraData":"",
      "memo":"",
      "sendAgain":false
   }
}
```

Data block enables you to get latest information about the transaction.

*Callbck Result*

Value | Type | Description
--------- | ------- | ---------
id | string | the order ID
coinName | string | unique token name
txid | string | transaction hash
meta | any | supplementary data
state | string | order state
bizType | string | order type
type | string | token type
from | string | transaction input
to | string | transaction output
value | string | transaction value
confirmations | number | number of transaction confirmations
fee | string | fee burnt for the transaction
fees | array | all types of fee burnt for the transaction
hash | string | transaction hash
block | number | the block transaction mined in
extraData | string | same as the extraData in request
memo | string | order note, editable on admin
sendAgain | boolean | if we suggest client to send the same request again
data | object | latest info of the transaction

## Audit

> Audit notification sends JSON structured like this:

```json
{
   "code":0,
   "status":0,
   "message":"OK",
   "crypto":"ecc",
   "hash":"sha3",
   "sort":"key-alphabet",
   "encode":"base64",
   "timestamp":1582285270762,
   "sig":{
      "r":"Fcn5eCj/2tpF4vLhhrLDNSSlP8smdVwWXcoufINclQc=",
      "s":"cUeE1d1HFQmzJbea/1+YjqNm+/cikJbyzMY8vqClM0I=",
      "v":27
   },
   "result":{
      "current":{
         "id":"5e4fc1d6548491723a096c23",
         "type":"ETH",
         "blocknumber":140215,
         "timestamp":1582285270762,
         "deposit_total":"0",
         "withdraw_total":"0.004",
         "fee_total":"0.000126",
         "internal_fee":"0",
         "internal_num":"0"
      },
      "wallet":"default",
      "blocknumber":140215,
      "calculated":true,
      "deposit_total":"0",
      "deposit_num":0,
      "withdraw_total":"0.004",
      "withdraw_num":1,
      "revert_total":"0",
      "revert_num":0,
      "refund_total":"0",
      "refund_num":0,
      "system_call_total":"0",
      "system_call_num":0,
      "delegate_total":"0",
      "delegate_num":0,
      "undelegate_total":"0",
      "undelegate_num":0,
      "principal_fund_total":"0",
      "principal_fund_num":0,
      "interest_fund_total":"0",
      "interest_fund_num":0,
      "sweep_total":"0",
      "sweep_num":0,
      "sweep_internal_total":"0",
      "sweep_internal_num":0,
      "airdrop_total":"0",
      "airdrop_num":0,
      "recharge_total":"0",
      "recharge_num":0,
      "recharge_internal_total":"0",
      "recharge_internal_num":0,
      "recharge_unexpected_total":"0",
      "recharge_unexpected_num":0,
      "recharge_special_total":"0",
      "recharge_special_num":0,
      "failed_withdraw_num":1,
      "failed_sweep_num":0,
      "failed_sweep_internal_num":0,
      "failed_refund_num":0,
      "failed_system_call_num":0,
      "failed_delegate_num":0,
      "failed_undelegate_num":0,
      "fees":[
         {
            "withdraw_fee":"0.000126",
            "refund_fee":"0",
            "sweep_fee":"0",
            "sweep_internal_fee":"0",
            "system_call_fee":"0",
            "delegate_fee":"0",
            "undelegate_fee":"0",
            "failed_withdraw_fee":"0",
            "failed_refund_fee":"0",
            "failed_sweep_fee":"0",
            "failed_sweep_internal_fee":"0",
            "failed_system_call_fee":"0",
            "failed_delegate_fee":"0",
            "failed_undelegate_fee":"0",
            "_id":"5e65bec73aef16290cb85510",
            "fee_type":"ETH"
         }
      ],
      "chainKey":"ETH",
      "type":"ETH",
      "timestamp":1582285270762,
      "appid":"test",
      "create_at":"2020-02-21T11:41:10.864Z",
      "update_at":"2020-03-09T03:57:59.884Z",
      "__v":6,
      "calc_order_num":2,
      "last":"5e4f8be476adea46c1cea568",
      "id":"5e4fc1d6548491723a096c23"
   }
}
```

Delegation related orders are not audited for now. 

*Callback Result*

Value | Type | Description
--------- | ------- | ---------
calculated | boolean | if the auditing is finished
deposit_total | string | total deposit token value
deposit_num | number | total deposit times
withdraw_total | string | total withdraw token value
withdraw_num | number | total withdraw times
sweep_total | string | total hot-to-cold token value
sweep_num | number | total hot-to-cold times 
sweep_internal_total | string | total internal-out token value
sweep_internal_num | number | total internal-out times
airdrop_total | string | total airdrop token value
airdrop_num | number | total airdrop times
recharge_total | string | total cold-to-hot token value
recharge_num | number | total cold-to-hot times
recharge_internal_total | string | total internal-in token value
recharge_internal_num | number | total internal-in times
recharge_unexpected_total | string | total unexpected-in token value
recharge_unexpected_num | number | total unexpected-in times
recharge_special_total | string | total special-in token value
recharge_special_num | number | total special-in times
failed_withdraw_num | number | total failed withdrawal times
failed_sweep_num | number | total failed hot-to-cold times
failed_sweep_internal_num | number | total failed internal-out times
fees | array | all type of fees burned
withdraw_fee | string | fees burned for withdrawal
refund_fee | string | fees burned for refund
sweep_fee | string | fees burned for hot-to-cold
sweep_internal_fee | string | fees burned for internal-out
system_call_fee | string | fees burned for all system_call txs
delegate_fee | string | fees burned for staking
undelegate_fee | string | fees burned for unstaking
failed_withdraw_fee | string | fees burned for failed withdrawal
failed_refund_fee | string | fees burned for failed refund
failed_sweep_fee | string | fees burned for refund hot-to-cold
failed_sweep_internal_fee | string | fees burned for refund internal-out
failed_system_call_fee | string | fees burned for all failed system_call txs
failed_delegate_fee | string | fees burned for failed delegation
failed_undelegate_fee | string | fees burned for failed undelegation
type | string | the audited token type
timestamp | number | the audited timestamp
blocknumber | number | corresponding block near the audited timestamp
appid | string | application ID
calc_order_num | number | audited order number
last | string | audit order ID of the previous audit
id | string | audit order ID of the current audit

# ECC Signature

## Release 0.13.x

>  Javascript code of building the message to be signed:

```js
function _buildMsg (obj, opts = {}) {
  let sortRule = opts.sort || 'key-alphabet'
  let arr = []
  if (_.isArray(obj)) {
    arr = obj.map((o, i) => ({
      k: (sortRule === 'kvpair' || sortRule === 'value') ? '' : i,
      v: _buildMsg(o, opts)
    }))
  } else if (_.isObject(obj)) {
    for (let k in obj) {
      if (obj[k] !== undefined) {
        arr.push({ k, v: _buildMsg(obj[k], opts) })
      }
    }
  } else if (obj === undefined || obj === null) {
    return ''
  } else {
    return obj.toString()
  }
  // Sort Array
  arr.sort((a, b) => {
    let aVal
    let bVal
    switch (sortRule) {
      case 'key':
        aVal = a.k
        bVal = b.k
        break
      case 'key-alphabet':
        aVal = a.k.toString()
        bVal = b.k.toString()
        break
      case 'value':
        aVal = a.v
        bVal = b.v
        break
      case 'kvpair':
      default:
        aVal = a.k.toString() + a.v
        bVal = b.k.toString() + b.v
        break
    }
    if (aVal < bVal) {
      return -1
    } else if (aVal === bVal) {
      return 0
    } else {
      return 1
    }
  })
  // Build message
  return arr.reduce((lastMsg, curr) => {
    return lastMsg + curr.k + curr.v
  }, '')
}
```

*Steps of building signature for GET requests::*

1. Get the current timestamp.
2. Form a String message that contains the timestamp above. The message looks like this:
</br>
```
timestamp1557913602438
```
3. Encrypt the message using sha3 or sha256 or md5.
4. Sign the encrypted message and get the signature.
5. Send request as the same format as described in [General Structure](#general-structure).

*Steps of building signature for POST requests::*

1. Get the current timestamp.
2. Form a String message that consists of all main parameters in "data" object and the timestamp, the keys 
must be sorted alphabetically. Use "request withdrawal" as an example, the message looks like this:
</br>
```
sequence0timestamp1557913602438tomg2bfYdfii2GG13HK94jXBYPPCSWRmSiAStypeBTCvalue0
```
3. Encrypt the message using sha3 or sha256 or md5.
4. Sign the encrypted message and get the signature.
5. Send request as the same format as described in [General Structure](#general-structure).

*Steps of verifying signature:*

1. Form a String message that consists of all fields in "result" object and the given timestamp of the response, the keys 
must be sorted alphabetically. Use "validate address" as an example, the message looks like this:
</br>
```
addressawesome1namespaceEosiosidJGEPU47lvG6qICvmAAABtimestamp1557912642626validtrue
```
2. Encrypt the message using sha3 or sha256 or md5.
3. Verify signature using the plain encrypted message and Jadepool's public key.

## Release 1.0

*Steps of building signature for GET requests:*

1. Get the current timestamp.
2. Form a String message that contains the timestamp above. The message looks like this:
</br>
```
timestamp1557913602438
```
3. Encrypt the message using sha3 or sha256 or md5.
4. Sign the encrypted message and get the signature.
5. Send request as the same format as described in [General Structure](#general-structure).

*Steps of building signature for POST requests:*

1. Get the current timestamp.
2. Form a String message that consists of all main parameters in "data" object, the keys 
must be sorted alphabetically. Use "request withdrawal" as an example, the message looks like this:
</br>
```
sequence0tomg2bfYdfii2GG13HK94jXBYPPCSWRmSiAStypeBTCvalue0
```
3. add timestamp at the end of the message, the message looks like this:
</br>
```
sequence0tomg2bfYdfii2GG13HK94jXBYPPCSWRmSiAStypeBTCvalue0timestamp1557913602438
```
4. Encrypt the message using sha3 or sha256 or md5.
5. Sign the encrypted message and get the signature.
6. Send request as the same format as described in [General Structure](#general-structure).

*Steps of verifying signature:*

1. Form a String message that consists of all fields in "result" object of the response, the keys 
must be sorted alphabetically. Use "validate address" as an example, the message looks like this:
</br>
```
addressawesome1namespaceEosiosidJGEPU47lvG6qICvmAAABvalidtrue
```
2. add the given timestamp at the end of the message, the message looks like this:
</br>
```
addressawesome1namespaceEosiosidJGEPU47lvG6qICvmAAABvalidtruetimestamp1557912642626
```
2. Encrypt the message using sha3 or sha256 or md5.
3. Verify signature using the plain encrypted message and Jadepool's public key.
