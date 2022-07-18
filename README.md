# Introduction
## API Library
### sign offline demo
https://github.com/the-ninth/sdk-offline-sign

### contract source files
https://github.com/the-ninth/sdk-contract

# General Info
## General API Information
* The base endpoint is: https://api.the-ninth.com   
# 

# Game Assets Endpoints

![process](https://github.com/the-ninth/api-doc/blob/pre/images/process1.jpg)
## Mint assets 

erc-1155 token  

POST /api/v1/game/token/mint  

**Parameters:**
|Name|Type|Mandatory|Description|
|:-----|:-----|:-----|:-----|
|id|INT|YES|the id of erc-1155 token|
|amount|INT|YES|token amount|
|receiver|STRING|YES|token receiver|
**Response:**
```
{
        "contract": "0x123...",// erc-1155 contract address
        "result": true //true: mint success;false:mint failed
}
```

## Get Game Deposit Address 
GET /api/v1/game/capital/deposit/address  

**Parameters:**
|Name|Type|Mandatory|Description|
|:-----|:-----|:-----|:-----|
|game_id|INT|YES|the game id |
**Response:**
```
{
        "address": "0x123...",// game deposit address
        "result": true //true or false
}
```

## Asset Detail 
GET /api/v1/game/asset/assetDetail  

**Parameters:**
|Name|Type|Mandatory|Description|
|:-----|:-----|:-----|:-----|
|game_id|INT|YES|the game id |
**Response:**
```
[
    {
        "contract": "0x123...",
        "amount": "8.91000000", 
        "id": "1"
    },
    {
        "contract": "0x123...",
        "amount": "8.91000000", 
        "id": "1"
    }
]
```

## Transfer 
POST /api/v1/game/asset/transfer  

**Parameters:**
|Name|Type|Mandatory|Description|
|:-----|:-----|:-----|:-----|
|game_id|INT|YES|the game id |
|txs|LIST<TX_REQ>|YES|
**TX_REQ**
|Name|Type|Mandatory|Description|
|to|STRING|YES|receiver |
|id|INT|YES|erc1155 token id  |
|amount|INT|YES|
|req_id|STRING|YES|the unique req id|

**Response:**
```
[
    {
        "req_id":"123",
        "tx_hash":"0x123...", 
        "status":1// 1 process;2 success ,3 failed
    }
]
```

# Wallet Endpoints
## System Status (System)
Fetch system status.  

GET /api/v1/system/status  

**Response:**
```
{ 
    "status": 0,              // 0: normal，1：system maintenance
    "msg": "normal"           // "normal", "system_maintenance"
}
```

## All Coins' Information
Get information of coins (available for deposit and withdraw).

GET /api/v1/wallet/token/config/getall  

**Response:**
```
[
    {
        "contract": "0x123...",
        "decimals": "6",
		"type": "erc20",
		"id": "",
        "isLegalMoney": true,
        "name": "USDT",
        "trading": true,
        "withdrawAllEnable": true
    },
    {
        "contract": "0x123...",
        "decimals": "6",
		"type": "erc1155",
        "id": "1",
        "isLegalMoney": false,
        "name": "GAME ASSET",
        "trading": true,
        "withdrawAllEnable": true
    }
]
```

## Deposit Address
Fetch deposit address with network.

GET /sapi/v1/wallet/capital/deposit/address

**Parameters:**
|Name|Type|Mandatory|Description|
|:-----|:-----|:-----|:-----|
|coin|STRING|YES||
|network|STRING|YES||
* If network is not send, return with default network of the coin.

**Response:**
```
{
    "address": "0x123...",
    "coin": "USDT"
}
```

## Deposit History
Fetch deposit history.

GET /api/v1/wallet/capital/deposit/hisrec

**Parameters:**
|Name|Type|Mandatory|Description|
|:-----|:-----|:-----|:-----|
|coin|STRING|NO|	
|status|INT|NO|0(0:pending,6: credited but cannot withdraw, 1:success)
|startTime|LONG|NO|Default: 90 days from current timestamp
|endTime|LONG|NO|Default: present timestamp
|offset|INT|NO|Default:0
|limit|INT||NO|Default:1000, Max:1000
* Please notice the default startTime and endTime to make sure that time interval is within 0-90 days.
* If both startTime and endTime are sent, time between startTime and endTime must be less than 90 days.
**Response:**
```
[
    {
        "amount":"0.00999800",
        "coin":"USDT",
        "network":"ETH",
        "status":1,
        "address":"0x123...",
        "addressTag":"",
        "txId":"0x123...",
        "insertTime":1599621997000,
        "transferType":0,
        "unlockConfirm":"6/6",
        "confirmTimes":"6/6"
    },
    {
        "amount":"0.50000000",
        "coin":"GAME ASSET",
        "network":"ETH",
        "status":1,
        "address":"0x123...",
        "addressTag":"",
        "txId":"0x123...",
        "insertTime":1599620082000,
        "transferType":0,
        "unlockConfirm":"6/6",
        "confirmTimes":"6/6"
    }
]
```

## Withdraw
Submit a withdraw request.

POST /api/v1/wallet/capital/withdraw/submit

**Parameters:**
|Name|Type|Mandatory|Description|
|:-----|:-----|:-----|:-----|
|coin|	STRING|	YES|	
|withdrawOrderId|	STRING|	NO|	client id for withdraw|
|network|	STRING	|NO	|
|address|	STRING	|YES	|
|addressTag|	STRING|	NO	|Secondary address identifier for coins like XRP,XMR etc.
|amount|	DECIMAL|	YES	|
|transactionFeeFlag|	BOOLEAN|	NO|	When making internal transfer, true for returning the fee to the destination account; false for returning the fee back to the departure account. Default false.|
|name|	STRING|	NO|	Description of the address. Space in name should be encoded into %20.
|walletType	|INTEGER	|NO	|The wallet type for withdraw，0-spot wallet ，1-funding wallet.Default spot wallet|

* If network not send, return with default network of the coin.
**Response:**
```
[
    {
        "address": "0x123...",
        "amount": "1.91000000",
        "applyTime": "2021-10-12 11:12:02",
        "coin": "USDT",
        "id": "1234567",
        "withdrawOrderId": "game123", // will not be returned if there's no withdrawOrderId for this withdraw.
        "network": "ETH", 
        "transferType": 0,   // 1 for internal transfer, 0 for external transfer   
        "status": 6,
        "transactionFee": "0.004",
        "confirmNo":3,  // confirm times for withdraw
        "info":"The address is not valid. Please confirm with the recipient", // reason for withdrawal failure
        "txId": "0x123..."
    }
]
```



## Withdraw History 
Fetch withdraw history.

GET /api/v1/wallet/capital/withdraw/history

**Parameters:**
|Name|Type|Mandatory|Description|
|:-----|:-----|:-----|:-----|
|coin|STRING|NO|
|withdrawOrderId|STRING|NO|
|status|INT|NO|0(0:pending,6: credited but cannot withdraw, 1:success)
|startTime|LONG|NO|Default: 90 days from current timestamp
|endTime|LONG|NO|Default: present timestamp
|offset|INT|NO|Default:0
|limit|INT||NO|Default:1000, Max:1000
* network may not be in the response for old withdraw.
* Please notice the default startTime and endTime to make sure that time interval is within 0-90 days.
* If both startTime and endTimeare sent, time between startTimeand endTimemust be less than 90 days.
* If withdrawOrderId is sent, time between startTime and endTime must be less than 7 days.
* If withdrawOrderId is sent, startTime and endTime are not sent, will return last 7 days records by default.
**Response:**
```
[
    {
        "address": "0x123...",
        "amount": "1.91000000",
        "applyTime": "2021-10-12 11:12:02",
        "coin": "USDT",
        "id": "1234567",
        "withdrawOrderId": "game123", // will not be returned if there's no withdrawOrderId for this withdraw.
        "network": "ETH", 
        "transferType": 0,   // 1 for internal transfer, 0 for external transfer   
        "status": 6,
        "transactionFee": "0.004",
        "confirmNo":3,  // confirm times for withdraw
        "info":"The address is not valid. Please confirm with the recipient", // reason for withdrawal failure
        "txId": "0x123..."
    }
]
```

## Sub-Account

### Create a Sub-account

POST /api/v1/wallet/sub-account/createSubAccount


**Parameters:**
|Name|Type|Mandatory|Description|
|:-----|:-----|:-----|:-----|
|subAccountString|STRING|YES|Please input a string. We will create a virtual email using that string for you to register|

**Response:**
```
{
    "email":"subaccount@gamedemo.com"
}
```

### Query Sub-account List

GET /api/v1/wallet/sub-account/list


**Parameters:**
|Name|Type|Mandatory|Description|
|:-----|:-----|:-----|:-----|
|email|STRING|NO|
|isFreeze|STRING|NO|true or false
|page|	INT|	NO|	Default value: 1|
|limit|	INT|	NO|	Default value: 1, Max value: 200|

**Response:**
```
{
    "subAccounts":[
        {
            "email":"testsub@gmail.com",
            "isFreeze":false,
            "createTime":1544433328000,
            "isManagedSubAccount": false,
            "isAssetManagementSubAccount": false
        },
        {
            "email":"virtual@oxebmvfonoemail.com",
            "isFreeze":false,
            "createTime":1544433328000,
            "isManagedSubAccount": false,
            "isAssetManagementSubAccount": false
        }
    ]
}
```

### Get Sub-account Deposit Address 

Fetch sub-account deposit address

GET /api/v1/wallet/capital/deposit/subAddress

**Parameters:**
|Name|Type|Mandatory|Description|
|:-----|:-----|:-----|:-----|
|email|STRING|NO|
|coin|STRING|YES|
|network|STRING|NO|

**Response:**
```
{
    "address":"0x123...",
    "coin":"USDT",
}
```

### Universal Transfer

POST /api/v1/wallet/sub-account/universalTransfer

**Parameters:**
|Name|Type|Mandatory|Description|
|:-----|:-----|:-----|:-----|
|fromEmail|	STRING	|NO	|
|toEmail	|STRING|	NO	|
|clientTranId	|STRING|	|NO|	Must be unique|
|symbol	|STRING	|NO|	Only supported under ISOLATED_MARGIN type|
|asset|	STRING|	YES	|
|amount	|DECIMAL	|YES|

**Response:**
```
{
    "tranId":112233,
    "clientTranId":"demo"
}
```

### Query Universal Transfer History
GET /api/v1/sub-account/universalTransfer

**Parameters:**
|Name|Type|Mandatory|Description|
|:-----|:-----|:-----|:-----|
|fromEmail|	STRING	|NO	|
|toEmail	|STRING|	NO	|
|clientTranId|	STRING	|NO|
|startTime|	LONG|	NO|	
|endTime|	LONG|	NO|	
|page	|INT|	NO|	Default 1|
|limit	|INT|	NO|	Default 500, Max 500|


**Response:**
```
{
    "result": [
        {
            "tranId": 12345,
            "fromEmail": "demo1@gmail.com",
            "toEmail": "demo1@gmail.com",
            "asset": "USDT",
            "amount": "0.01",
            "createTimeStamp": 1840317374000,
            "status": "SUCCESS",
            "clientTranId": "demo"
        }
    ],
    "totalCount": 1
}
```