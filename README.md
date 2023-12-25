# 锋速 API 文档

官网：https://fengsu.ing <br>
文档：https://docs.fengsu.com <br>
联系我们：mailto:fengsuing@proton.me <br>
github：https://github.com/fengsuing/fengsu-api-docs
<hr>
<hr>

## 项目说明
一：本项目作用于web3行业用户充、提等服务，大大减少了企业、个人开发难度，开箱即用<br>
二：.项目特点：<br>
1.安全，采用了多种不可逆的算法加密重要内容，关键信息设置确保正确<br>
2.快捷，采用了异步、高并发编码方式提高归集的速度<br>
3.可扩展，预留了进一步二次开发的空间，今后将接入更多币种、对接更多主链<br>
4.匿名，重要信息二次加密，不收集用户隐私信息，基于区块链特性，保护用户隐私<br>
5.自动化，全程自动化、无感知运行<br>
6.无控制台，仅需注册一个用户即可快捷方便地使用API进行属于你的二次开发<br>
7.多重RPC验证，以多重Binance RPC为准，数据准确无误
<hr>

## 对接说明

### 1
所列举API除generateKey外，共有必选参数username，仅需传递有效的jwtToken或signature参数即可，无需手动传递username参数，程序会自动解析请求用户<br>


接口返回值code:<br>
200 成功<br>
400 失败<br>
500 服务器错误<br>

### 2
ak、sk鉴权步骤，当采用ak、sk鉴权时，必须携带以下参数
| 参数 | 类型   | 是否必需 | 描述              |
|-----------|--------|----------|--------------------------|
| reqId     | string | YES      | 请求id |
| timestamp     | string | YES      | 请求时间戳，毫秒，有效期过去30秒内 |
| accessKey     | string | YES      | 访问令牌 |
| signature     | string | YES      | 签名 |

其中签名(signature)签发规则是:<br>
1.<p style="color:red">先对timestamp使用sk作为密钥算出timeKey，HmacSHA256(timestamp,security)<p><br>
2.<p style="color:red">再对timeKey使用ak作为密钥算出akKey，HmacSHA256(timeKey,accessKey)<p><br>
3.<p style="color:red">最后使用akKey作为密钥算出reqId作为最后的signature，HmacSHA256(reqId,accessKey)<p>

### 3
首次注册登陆后请点击生成访问令牌并妥善保管ak、sk

<hr>
<hr>

## 重要说明
1.<p style="color:red">币种及链，本项目作用于BSC链，目前集成了BTC、ETH、USDT的充值监测，后续会视情况免费开通其它热门、主流币种的监测<p><br>

2.<p style="color:red">费用，USDT的归集收取归集数量的1%作为手续费，其它币种的归集固定每笔扣除2USDT，从用户个人账户扣除，账户余额为0或者小于0则不可自动归集<p><br>

3.<p style="color:red">链上手续费，由锋速支付，用户无需额外支付<p><br>

4.<p style="color:red">USDT达到设定的阀值才会归集并收取手续费，其它币种一旦产生充值后，无论数量多少则立即归集至用户设置的归集地址，若未设置归集地址，则会忽略归集，直到用户设置地址<p><br>

5.<p style="color:red">回调接口地址，需要开发人员设置，锋速检测到充值后会立即向钱包所属用户的回调接口**post**发送本次充值情况，最多三次重试，直至获取到**code:200**，则认为充值情况已成功发送<p><br>

6.<p style="color:red">上币申请，若需要监测其它币种的充值情况，请发送上币申请至fengsuing@proton.me，上币费用为1000USDT，成功上币后改不退还<p><br>


<hr>
<hr>

## 1.获取钱包地址
获取一个钱包地址，成功后将绑定于请求鉴权的用户名，用户获取后请自行分配

### API

`POST https://api.fengsu.ing/web3/user/getAddress`

### 参数

| 参数 | 类型   | 是否必需 | 描述              |
|-----------|--------|----------|--------------------------|
| netId     | number | YES      | Network ID |

### 示例请求

```http
POST /web3/user/getAddress HTTP/1.1
Host: api.fengsu.ing
Content-Type: application/json

{
  "netId": 123
}
```

### 成功响应示例

```json
{
    "code": 200,
    "data": {
        "address": "0xc24c8887Ee31a18a18a0e3a7c4aDD2b45c918B68"
    },
    "msg": "success"
}
```

## 2.获取充值记录
获取用户所属的钱包充值记录、用户个人账户充值记录

### API

`POST https://api.fengsu.ing/web3/user/rechargeRecords`

### 参数

| 参数 | 类型   | 是否必需 | 描述              |
|-----------|--------|----------|--------------------------|
| type     | number | NO      | 记录类型，1钱包，2个人账户 |
| page     | number | NO      | 默认每页显示15条记录 |
| symbol     | string | NO      | 币种名称 |

### 示例请求

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

### 成功响应示例

```json
{
    "code": 200,
    "data": [
        {
            "id": 3,
            "address": "0xbfD0Afb77B5b0f071aAA1e14a48D17674D44867b",    //收款方地址，即用户地址
            "fromAddress": "0x6a5B10989fC958FAef6f1f60a412eC3c29D7F2fA",    //充值方地址，实际不关切
            "amount": "0.350000000000000000",   //充值数量
            "symbol": "USDT",   //充值币种
            "txHash": "0x3c761d093f952ac561307e265e8dfdecf3d0a1217afd03ba6b788df8b7a17384", //交易哈希
            "timestamp": "1701797061",
            "username": "xxx"   //所属用户
        }
    ],
    "msg": "success"
}
```

## 3.获取归集记录
获取用户所属的钱包归集记录

### API

`POST https://api.fengsu.ing/web3/user/collectRecords`

### 参数

| 参数 | 类型   | 是否必需 | 描述              |
|-----------|--------|----------|--------------------------|
| type     | number | NO      | 记录类型，1钱包，2个人账户 |
| page     | number | NO      | 默认每页显示15条记录 |
| symbol     | string | NO      | 币种名称 |

### 示例请求

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

### 成功响应示例

```json
{
    "code": 200,
    "data": [
        {
            "id": 1,
            "address": "0x315a78baeE95Cf6F854Aa48Eb5Ae42334f1971A8",    //用户设置的归集地址
            "fromAddress": "0xF62496a2c041EE4Df44A982A5C2471A682f96927",    //钱包地址
            "amount": "0.001000000000000000",   //数量
            "symbol": "BTCB",   //币种
            "txHash":   "0x4d9950526fbcc26d5be66b95c2cda65951c71bb9a640559d1657ef0456690944",   //交易哈希
            "timestamp": "1702572811",
            "username": "xxx",
            "fee": "2.000000000000000000"   //单位$
        }
    ],
    "msg": "success"
}
```


## 4.获取用户信息
获取用户信息

### API

`POST https://api.fengsu.ing/web3/user/info`

### 参数

| 参数 | 类型   | 是否必需 | 描述              |
|-----------|--------|----------|--------------------------|

### 示例请求

```http
POST /web3/user/info HTTP/1.1
Host: api.fengsu.ing
Content-Type: application/json
```

### 成功响应示例

```json
{
    "code": 200,
    "data": {
        "username": "xx",
        "email": "xxxx@gmail.com",
        "callback": "https://api.xxx.com",
        "status": 0,
        "createTime": "1703432929858",
        "ak": "AKgz***************************yxv",
        "sk": "SK97***************************gb0",
        "walletAddress": "0x3e97A9272D0a96fe8f8847E992f2B2dd68E21955",  //用户个人账户钱包
        "balance": "0.000000000000000000",  //钱包余额($)
        "fee": "1.000000000",   //USDT归集手续费(%)
        "otherFee": 2,  //其它币种归集固定手续费($)
        "trxThreshold": "10.000000000", //USDT归集阀值
        "isVip": 0,
        "vipExpireTime": null,
        "otpSecret": null,
        "collectAddress": "0x315a78baeE95Cf6F854Aa48Eb5Ae42334f1971A8"  //用户设置的归集地址
    },
    "msg": "success"
}
```

## 5.获取、吊销鉴权密钥对
获取、吊销鉴权密钥对

### API

`POST https://api.fengsu.ing/web3/user/generateKeys`

### 参数

| 参数 | 类型   | 是否必需 | 描述              |
|-----------|--------|----------|--------------------------|
| revoke     | number | NO      | 吊销 |

### 示例请求

```http
POST /web3/user/generateKeys HTTP/1.1
Host: api.fengsu.ing
Content-Type: application/json
```

### 成功响应示例

```json
{
    "code": 200,
    "data": {
        "accessKey": "AKgzk4jd5ah1yw52brb65affg3v4usjyxv",
        "securityKey": "SK977xwp1cv6ssm27ae9mh08us6qsfkgb0"
    },
    "msg": "获取keys成功,只会展示一次，请妥善保存"
}
```

## 6.设置回调接口
用于所属用户的钱包产生充值通知的回调接口，通知会以JSON格式通知如下

```json
{
  "rechargeInfo": {
    "target": "bsc",    //所属主链
    "username": "xxx",  //用户名
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

### 参数

| 参数 | 类型   | 是否必需 | 描述              |
|-----------|--------|----------|--------------------------|
| callback     | string | YES      | 回调接口 |

### 示例请求

```http
POST /web3/user/setCallback HTTP/1.1
Host: api.fengsu.ing
Content-Type: application/json

{
  "callback": "https://api.xxxx.com/callback"
}
```

### 成功响应示例

```json
{
    "code": 200,
    "data": {
        "callback": "https://api.xxxx.com/callback"
    },
    "msg": "success"
}
```

## 8.设置归集阀值
设置USDT归集阀值

### API

`POST https://api.fengsu.ing/web3/user/setThreshold`

### 参数

| 参数 | 类型   | 是否必需 | 描述              |
|-----------|--------|----------|--------------------------|
| amount     | string | YES      | USDT数量，最小值10，默认100 |

### 示例请求

```http
POST /web3/user/setThreshold HTTP/1.1
Host: api.fengsu.ing
Content-Type: application/json

{
  "amount": "1000"
}
```

### 成功响应示例

```json
{
    "code": 200,
    "data": {
        "threshold": "1000"
    },
    "msg": "success"
}
```

## 9.设置用户归集地址
设置用户归集地址，用于接收此前充值的币种

### API

`POST https://api.fengsu.ing/web3/user/setCollectAddress`

### 参数

| 参数 | 类型   | 是否必需 | 描述              |
|-----------|--------|----------|--------------------------|
| address     | string | YES      | 归集地址 |

### 示例请求

```http
POST /web3/user/setCollectAddress HTTP/1.1
Host: api.fengsu.ing
Content-Type: application/json

{
  "address": "0xd93DbfB27e027F5e9e6Da52B9E1C413CE35ADC11"
}
```

### 成功响应示例

```json
{
    "code": 200,
    "data": {
        "address": "0xd93DbfB27e027F5e9e6Da52B9E1C413CE35ADC11"
    },
    "msg": "success"
}
```

### 失败响应示例

```json
{
    "code": 400,
    "msg": "无效地址"
}
```
