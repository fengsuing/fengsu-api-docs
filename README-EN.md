# Fengsu API documentation
Official website: https://fengsu.ing <br>
Contact us: fengsuing@proton.me <br>
Chinese API documentation: https://blog.fengsu.ing/2024/01/10/feng-su-qian-bao-api/ <br>
English API doc: https://blog.fengsu.ing/2024/01/10/fengsu-wallet-api/ <br>
github: https://github.com/fengsuing/fengsu-api-docs
<hr>

## project instruction
It is used for deposit and withdrawal services for users in the web3 industry, greatly reducing the difficulty of development for enterprises and individuals, and it can be used right out of the box<br>

1.Security, using a variety of irreversible algorithms to encrypt important content, ensuring that key information settings are correct<br>
2.Fast, using asynchronous and high-concurrency coding methods to improve the speed of collection<br>
3.Extensible, leaving room for further secondary development. In the future, more currencies will be connected and more main chains will be connected<br>
4.Anonymous, secondary encryption of important information, no collection of user privacy information, based on blockchain characteristics, protecting user privacy<br>
5.Automation, full automation, non-perception operation<br>
6.No console, you only need to register a user to quickly and easily use the API for your secondary development<br>
7.Multiple RPC verification, subject to multiple Binance RPC, the data is accurate
<hr>


## Important docking instructions

In addition to generateKey, the listed APIs have a mandatory parameter username. You only need to pass a valid jwtToken or signature parameter. There is no need to manually pass the username parameter. The program will automatically parse the requesting user<br>
<hr>

Interface return value code:<br>
200 Success<br>
400 failed<br>
500 Server Error<br>
<hr>

ak, sk authentication steps, when using ak, sk authentication, the following parameters must be carried

| Parameters | Type   | Required | Description              |
|-----------|--------|----------|--------------------------|
| reqId     | string | YES      | request id |
| timestamp     | string | YES      | Request timestamp, milliseconds, valid within 30 seconds in the past |
| accessKey     | string | YES      | access token |
| signature     | string | YES      | signature |

The signature issuance rules are:<br>
1.<span style="color:#FF6347">First use sk as the key for timestamp to calculate timeKey, HmacSHA256(timestamp,securityKey)</span><br>
2.<span style="color:#FF6347">Use ak as the key for timeKey to calculate akKey, HmacSHA256(timeKey, accessKey)</span><br>
3.<span style="color:#FF6347">Finally use akKey as the key to calculate reqId as the final signature, HmacSHA256 (reqId, accessKey)</span>


After registering and logging in for the first time, please click to generate an access token and keep ak and sk properly.

<hr>

## important
1.<span style="color:#FF6347">Currency and chain. This project works on the BSC chain. It currently integrates recharge monitoring of BTC, ETH, and USDT. In the future, other popular and mainstream currencies will be opened for free depending on the situation. Monitor</span><br>

2.<span style="color:#FF6347">Fees: 1% of the collection amount is charged as a handling fee for the collection of USDT. A fixed deduction of 2 USDT per transaction for the collection of other currencies is deducted from the user's personal account. The account balance If it is 0 or less than 0, it cannot be automatically collected</span><br>

3.<span style="color:#FF6347">The on-chain handling fee will be paid by Fengsu, and users do not need to pay additional</span><br>

4.<span style="color:#FF6347">USDT will be collected and charged for handling fees only when it reaches the set threshold. Once other currencies are recharged, they will be immediately collected into the collection set by the user regardless of the amount. Address, if the collection address is not set, collection will be ignored until the user sets the address</span><br>

5.<span style="color:#FF6347">The callback interface address needs to be set by the developer. After Fengsu detects the recharge, it will immediately send the recharge information to the callback interface **post** of the user who owns the wallet, up to three times. Try again until **code:200** is obtained, then the recharge information is considered to have been sent successfully</span><br>

6.<span style="color:#FF6347">To apply for currency listing, if you need to monitor the recharge status of other currencies, please send a currency listing application to fengsuing@proton.me. The currency listing fee is 1,000USDT. After the currency is successfully listed, it will be Non-refundable</span><br>


<hr>
<hr>

## 1. Get wallet address
Obtain a wallet address. After success, it will be bound to the user name requesting authentication. After the user obtains it, please assign it yourself.

### API

`POST https://api.fengsu.ing/web3/user/getAddress`

### Parameters

| Parameters | Type   | Required | Description              |
|-----------|--------|----------|--------------------------|
| netId     | number | YES      | network id, default 56 |

### Sample request

```http
POST /web3/user/getAddress HTTP/1.1
Host: api.fengsu.ing
Content-Type: application/json

{
   "netId": 56
}
```

### Successful response example

```json
{
     "code": 200,
     "data": {
         "address": "0xc24c8887Ee31a18a18a0e3a7c4aDD2b45c918B68"
     },
     "msg": "success"
}
```

## 2. Get recharge records
Obtain the user's wallet recharge record and user's personal account recharge record

### API

`POST https://api.fengsu.ing/web3/user/rechargeRecords`

### Parameters

| Parameters | Type   | Required | Description              |
|-----------|--------|----------|--------------------------|
| type     | number | NO      | Record type, 1 wallet, 2 personal accounts |
| page     | number | NO      | Each page displays 15 records by default |
| symbol     | string | NO      | currency name |

### Sample request

```http
POST /web3/user/rechargeRecords HTTP/1.1
Host: api.fengsu.ing
Content-Type: application/json

{
   "type": 1,
   "page": 1,
   "symbol": "USDT"
}
```

### Successful response example

```json
{
     "code": 200,
     "data": [
         {
             "id": 3,
             "address": "0xbfD0Afb77B5b0f071aAA1e14a48D17674D44867b", //Payee address, that is, user address
             "fromAddress": "0x6a5B10989fC958FAef6f1f60a412eC3c29D7F2fA", //The recharge party address does not actually matter
             "amount": "0.350000000000000000", //Amount of recharge
             "symbol": "USDT", //Recharge currency
             "txHash": "0x3c761d093f952ac561307e265e8dfdecf3d0a1217afd03ba6b788df8b7a17384", //Transaction Hash
             "timestamp": "1701797061",
             "username": "xxx" //User to whom
         }
     ],
     "msg": "success"
}
```

## 3. Get collection records
Get the wallet collection record to which the user belongs

### API

`POST https://api.fengsu.ing/web3/user/collectRecords`

### Parameters

| Parameters | Type | Required | Description |
|-----------|--------|----------|---------------------------|
| type | number | NO | Record type, 1 wallet, 2 personal accounts |
| page | number | NO | Each page displays 15 records by default |
| symbol | string | NO | currency name |


### Sample request

```http
POST /web3/user/collectRecords HTTP/1.1
Host: api.fengsu.ing
Content-Type: application/json

{
   "type": 1,
   "page": 1,
   "symbol": "BTCB"
}
```

### Successful response example

```json
{
     "code": 200,
     "data": [
         {
             "id": 1,
             "address": "0x315a78baeE95Cf6F854Aa48Eb5Ae42334f1971A8", //Collection address set by the user
             "fromAddress": "0xF62496a2c041EE4Df44A982A5C2471A682f96927", //wallet address
             "amount": "0.001000000000000000", //Amount
             "symbol": "BTCB", //currency
             "txHash": "0x4d9950526fbcc26d5be66b95c2cda65951c71bb9a640559d1657ef0456690944", //Transaction Hash
             "timestamp": "1702572811",
             "username": "xxx",
             "fee": "2.000000000000000000" //Unit$
         }
     ],
     "msg": "success"
}
```


## 4. Obtain user information
Get user information

### API

`POST https://api.fengsu.ing/web3/user/info`

### Parameters

| Parameters | Type | Required | Description |
|-----------|--------|----------|--------------------------|

### Sample request

```http
POST /web3/user/info HTTP/1.1
Host: api.fengsu.ing
Content-Type: application/json
```

### Successful response example

```json
{
     "code": 200,
     "data": {
         "username": "xx",
         "email": "xxxx@gmail.com",
         "callback": "https://api.xxx.com",
         "status": 0,
         "createTime": "1703432929858",
         "ak": "AKgz******************************yxv",
         "sk": "SK97******************************gb0",
         "walletAddress": "0x3e97A9272D0a96fe8f8847E992f2B2dd68E21955", //User's personal account wallet
         "balance": "0.000000000000000000", //Wallet balance ($)
         "fee": "1.000000000", //USDT collection fee (%)
         "otherFee": 2, //Fixed handling fee for other currencies ($)
         "trxThreshold": "10.000000000", //USDT collection threshold
         "isVip": 0,
         "vipExpireTime": null,
         "otpSecret": null,
         "collectAddress": "0x315a78baeE95Cf6F854Aa48Eb5Ae42334f1971A8" //Collection address set by the user
     },
     "msg": "success"
}
```

## 5. Obtain and revoke authentication key pairs
Obtain and revoke authentication key pairs

### API

`POST https://api.fengsu.ing/web3/user/generateKeys`

### Parameters

| Parameters | Type | Required | Description |
|-----------|--------|----------|--------------------------|
| revoke | number | NO | revoke,default number 1|

### Sample request

```http
POST /web3/user/generateKeys HTTP/1.1
Host: api.fengsu.ing
Content-Type: application/json
```

### Successful response example

```json
{
     "code": 200,
     "data": {
         "accessKey": "AKgzk4jd5ah1yw52brb65affg3v4usjyxv",
         "securityKey": "SK977xwp1cv6ssm27ae9mh08us6qsfkgb0"
     },
     "msg": "Keys obtained successfully, will only be displayed once, please save them properly"
}
```

## 6. Set callback interface
The callback interface used to generate recharge notifications for the user's wallet. The notifications will be notified in JSON format as follows:

```json
{
   "rechargeInfo": {
     "target": "bsc", //The main chain it belongs to
     "username": "xxx", //username
     "from": "0x6a5B10989fC958FAef6f1f60a412eC3c29D7F2fA",
     "to": "0x45678CC2acC0c2c913c07346BC692B857033CACa",
     "amount": "3.5",
     "symbol": "USDT",
     "txHash": "0x70d1406a02bd8ce3bf0d5e486b41648aa53ef59970b68a1c694ad78b1f97df2a",
     "timestamp": 1703436688
   }
}
```

### API

`POST https://api.fengsu.ing/web3/user/setCallback`

### Parameters

| Parameters | Type | Required | Description |
|-----------|--------|----------|--------------------------|
|callback | string | YES | callback interface |

### Sample request

```http
POST /web3/user/setCallback HTTP/1.1
Host: api.fengsu.ing
Content-Type: application/json

{
   "callback": "https://api.xxxx.com/callback"
}
```

### Successful response example

```json
{
     "code": 200,
     "data": {
         "callback": "https://api.xxxx.com/callback"
     },
     "msg": "success"
}
```

## 8. Set the collection threshold
Set USDT collection threshold

### API

`POST https://api.fengsu.ing/web3/user/setThreshold`

### Parameters

| Parameters | Type | Required | Description |
|-----------|--------|----------|--------------------------|
| amount | string | YES | USDT amount, minimum value 10, default 100 |

### Sample request

```http
POST /web3/user/setThreshold HTTP/1.1
Host: api.fengsu.ing
Content-Type: application/json

{
   "amount": "1000"
}
```

### Successful response example

```json
{
     "code": 200,
     "data": {
         "threshold": "1000"
     },
     "msg": "success"
}
```

## 9. Set user collection address
Set the user collection address to receive previously deposited currencies

### API

`POST https://api.fengsu.ing/web3/user/setCollectAddress`

### Parameters

| Parameters | Type | Required | Description |
|-----------|--------|----------|--------------------------|
| address | string | YES | collection address |

### Sample request

```http
POST /web3/user/setCollectAddress HTTP/1.1
Host: api.fengsu.ing
Content-Type: application/json

{
   "address": "0xd93DbfB27e027F5e9e6Da52B9E1C413CE35ADC11"
}
```

### Successful response example

```json
{
     "code": 200,
     "data": {
         "address": "0xd93DbfB27e027F5e9e6Da52B9E1C413CE35ADC11"
     },
     "msg": "success"
}
```

### Failure response example

```json
{
     "code": 400,
     "msg": "Invalid address"
}
```
