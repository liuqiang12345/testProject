

##[REST API]

| 版本号 | 说明         | 时间       | 备注 |
| ------ | ------------ | ---------- | ---- |
| v1.0.0 | 币币交易相关 | 2018年12月 | 可用 |
|        |              |            |      |



##[请求路径]

 中心柜台 : requestUrl = https://sit1-globalapi.bithumb.me/api/request
 欧洲柜台   : requestUrl = https://sit1-euroapi.bithumb.me//api/request
## [请求参数说明] (重要)

### 公共请求参数

| 字段       | 说明              | 必填(是/否/可选) | 备注                                                         | 类型   |
| ---------- | ----------------- | ---------------- | ------------------------------------------------------------ | ------ |
| apiKey     | 用户账号(api专用) | 是               | 在用户申请界面申请                                           | String |
| bizCode    | 业务编号号        | 是               | 详情 参考[业务编号号说明]                                    | String |
| timestamp  | 时间戳毫秒        | 是               | 毫秒时间戳                                                   | Long   |
| encodeData | 业务数据          | 是               | 对应业务数据参考业务[业务参数说明]                           | String |
| signature  | 签名数据          | 是               | 对应业务数据签名数据,参考[数据认证签名]业务不需要签名 传固定值 "-1" | String |
| version    | 版本号            | 是               | 当前调用Api 版本号 [REST API简介]                            | String |
| msgNo      | 请求流水          | 是               | 用户生成 (50位以内显示长度 唯一字符串)                       | String |

### 返回结果说明

如果 查询有分页条件(page, count)接口 返回数据格式

```java
	{

         "num":5  // 数据总数量

	    "list":[{"":""}……...]   查询数据 集合

	}
```



##[数据签名] (重要)

- 用户计算签名的基于哈希的协议，此处使用 HmacSHA256  (签名参数 , secretKey(用户申请页面获取))

  String signature = HmacSHA256 . encode ( jsonStringData , secretKey );

- 签名数据(signature) :签名数据由 公共请求参数中 除signature 字段 按照key的字典顺序来排序(升序)组成jsonString 进行 签名

- encodeData : 用base64 编码 (业务参数数据) 

- 签名事例

  

  ```java
  {
  
      "apiKey" : "XXXXXXXXXXXX", 	// (用户申请页面获取)
  
  	"bizCode" :"PLACE_ORDER_COIN", 	//参考[业务编号表]
  
  	"encodeData" : "Base64.encode ({"symbol","BTC-ETH","type":"LIMIT", ... ...})"  //参考 [业务参数说明]
  
  	"msgNo":"1234567890", 参考[请求参数说明]
  
  	"timestamp":15348923323343,
  
  	"version":"V1.0.0"	 // 参考  [REST API简介] ]
  
  }  
  字典顺序排序(升序)    生成 源串:
  apiKey=XXXXXXX&bizCode=PLACE_ORDER_SPOT&encodeData=XXXXXXX&msgNo=123456789&timestamp=1455323333332&version=v1.0.0
  HmacSHA256 生成 signature
  ```

  

## [请求与应答] (重要)

- POST 请求 Content-Type:application/json

- 请求参数事例:

  ```java
   {
    	"timestamp":"1455323333332",
    	"apiKey":"XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX", //(用户申请页面获取)
    	"bizCode":"PLACE_ORDER_COIN",  //参考[业务编号表]
        	"version":"v1.0.0",
        	"encodeData":"eyJhcGFhIjoicHBwcHBwcHAifQ==",   //base64 编码
    	"signature":"1EF13F23D123A3123GXXXXXXXXXXXXXXXXXXXXXXXXX"	//参考[数据签名]
    }
   
  ```

  

  应答事例:

  

  ```java
  {
        "encodeData": "业务返回数据", //base64 编码 数据类容参考 [业务参数说明]
        "signature": "签名数据",    //参考 [数据签名]
        "msgCode": "应答码", //参考 [应答码对照表]
        "msg": "应答码明细",  //  尽量避免作为业务判断,参考 [应答码对照表]
        "timestamp": 1545220632343,
        "vesion": "v1.0.0"
    }
  
  ```

  

  [业务编号表]

注意:编码版兼容(v.2.0.0 的业务编码 在v1.0.0 无法兼容使用 反之兼容可用)状态默认 可用

| 业务编号                | 说明                                       | 所在版本 | 是/否需要签名认证 |
| ----------------------- | ------------------------------------------ | -------- | ----------------- |
| PLACE_ORDER_SPOT        | 币币现货下单                               | v1.0.0   | 是                |
| SUBMIT_CANCEL_SPOT      | 币币现货撤单单条                           | v1.0.0   | 是                |
| ASSET_QUERY_SPOT        | 币币交易账号资产查询                       | v1.0.0   | 是                |
| ORDER_QUERY_SPOT        | 币币订单查询                               | v1.0.0   | 是                |
| ORDER_DETAIL_QUERY_SPOT | 币币订单成交明细                           | v1.0.0   | 是                |
| MARKET_SPOT             | 市场行情(包含最新成交价 和 24小时交易统计) | v1.0.0   | 否                |
| ORDER_BOOK_SPOT         | 深度 买卖最多 各 200 条                    | v1.0.0   | 否                |
|                         |                                            |          |                   |
|                         |                                            |          |                   |

 ## [业务参数说明]

### 1. PLACE_ORDER_SPOT (币币现货下单)

请求参数说明

| 字段          | 说明                          | 必填(是/否/可选) | 备注                                       | 类型   |
| ------------- | ----------------------------- | ---------------- | ------------------------------------------ | ------ |
| symbol        | 币对符号，格式是 token-market | 是               | 前面交易币种 后面是交易市场     (BTC-USDT) | String |
| type          | 交易类型 (limit or market)    | 是               | limit 限价  market 市价                    | String |
| side          | 交易方向 (buy or sell)        | 是               | buy 买入  sell 卖出                        | String |
| price         | 价格                          | 是               | type为 市价(limit)交易时 固定值 "-1"       | String |
| amount        | 数量                          | 是               | 交易数量                                   | String |
| amountDisplay | 可见数量                      | 是               | amount = amountDisplay                     | String |

返回结果说明

| 字段     | 说明           | 必填(是/否/可选) | 备注                        | 类型   |
| -------- | -------------- | ---------------- | --------------------------- | ------ |
| forderId | 返回平台订单号 | 是               | 供撤单查询使用 不支持 msgNo | String |
| fsymbol  | 返回币对符号   | 是               |                             | String |



### 2. SUBMIT_CANCEL_SPOT (订单撤销)

请求参数说明

| 字段    | 说明   | 必填(是/否/可选) | 备注       | 类型   |
| ------- | ------ | ---------------- | ---------- | ------ |
| orderID | 订单号 | 是               | 由平台返回 | String |



### 3. ASSET_QUERY_SPOT (币币交易账号资产查询)

请求参数说明

| 字段     | 说明     | 必填(是/否/可选) | 备注   | 类型   |
| -------- | -------- | ---------------- | ------ | ------ |
| coinType | 币种类型 | 是               | 如 BTC | String |

返回结果说明

| 字段    | 说明     | 必填(是/否/可选) | 备注 | 类型   |
| ------- | -------- | ---------------- | ---- | ------ |
| fcount  | 总资产   | 是               |      | String |
| ffrozen | 冻结资产 | 是               |      | String |
| fcoinId | 币种类型 | 是               |      | String |



### 4. ORDER_QUERY_SPOT(币币订单查询) 

请求参数说明 

| 字段      | 说明         | 必填(是/否/可选) | 备注                                               | 类型   |
| --------- | ------------ | ---------------- | -------------------------------------------------- | ------ |
| time      | 时间查询条件 | 可选             | (thisweek=本周，thisweekago=本周以前) 不选查全部   | String |
| fside     | 查询条件     | 可选             | bug or sell  不选查全部                            | String |
| fcoinId   | 币种类型     | 可选             | 不选查全部                                         | String |
| fmarketId | 市场         | 可选             | 不选查全部                                         | String |
| fstatus   | 订单类型     | 可选             | history:close单据，entrust:open委托单据 不选查全部 | String |
| page      | 页码         | 可选             | 不选 默认 1                                        | String |
| count     | 显示数量     | 可选             | 不选 默认 10                                       | String |
| orderId   | 订单号       | 可选             | 服务端返回的订单id                                 | String |

返回结果说明 (List)

| 字段          | 说明           | 必填(是/否/可选) | 备注                                                      | 类型   |
| ------------- | -------------- | ---------------- | --------------------------------------------------------- | ------ |
| forderId      | 订单号         | 是               |                                                           | String |
| fmarketId     | 市场           | 是               |                                                           | String |
| fcoinId       | 币种           | 是               |                                                           | String |
| fprice        | 下单价         | 是               | 下单价 市价 为 -1                                         | String |
| fdeal         | 成交量         | 是               | 已经成交数量                                              | String |
| ftotal        | 订单委托总量   | 是               |                                                           | String |
| succPrice     | 成交均价       | 是               |                                                           | String |
| fstatus       | 订单状态       | 是               | init或send或pending=进行中，success=已完成，cancel=已撤销 | String |
| forderType    | 订单类型       | 是               | （market=市价，limit=限价）                               | String |
| fside         | 下单方向       | 是               | bug or sell                                               | String |
| fstartTime    | 开始时间       | 是               |                                                           | Long   |
| fcompleteTime | 最后变化时间吗 | 是               | 订单最后变更时间                                          | Long   |

### 5. ORDER_DETAIL_QUERY_SPOT (订单交易明细)

请求参数说明

| 字段    | 说明     | 必填(是/否/可选) | 备注                  | 类型   |
| ------- | -------- | ---------------- | --------------------- | ------ |
| orderId | 订单id   | 是               | 平台返回 不选查询所有 | String |
| page    | 页码     | 可选             | 不选默认1             | String |
| count   | 显示条数 | 可选             | 不选默认10            | String |

返回结果说明 (List)

| 字段          | 说明             | 必填(是/否/可选) | 备注                   | 类型   |
| ------------- | ---------------- | ---------------- | ---------------------- | ------ |
| orderId       | 订单号           | 是               |                        | String |
| orderSign     | 当前订单当前状态 |                  | 当前make还是taker 成交 | String |
| getCount      | 获得数量         | 是               |                        | String |
| getCountUnit  | 获得数量单位     | 是               | 币种                   | String |
| loseCount     | 失去数量         | 是               |                        | String |
| loseCountUnit | 失去数量单位     | 是               | 币种                   | String |
| price         | 成交价格         | 是               |                        | String |
| priceUnit     | 成交价格单位     | 是               | 币种                   | String |
| fee           | 手续费           | 是               | 手续费                 | String |
| feeUnit       | 手续费单位       | 是               | 币种                   | String |
| time          | 时间             | 是               |                        | Long   |
| fsymbol       | 交易币对         | 是               | BTC-USDT               | String |
| side          | 方向             | 是               | 买 or 卖               | String |

### 6.  MARKET_SPOT (现货市场行情)

请求参数说明

| 字段      | 说明     | 必填(是/否/可选) | 备注                | 类型   |
| --------- | -------- | ---------------- | ------------------- | ------ |
| fmarketId | 市场名称 | 可选             | BTC,USDT 不选查所有 | String |
| fcoinId   | 币种名称 | 可选             | 不选查所有          | String |

返回结果说明 (List)

| 字段     | 说明       | 必填(是/否/可选) | 备注       | 类型   |
| -------- | ---------- | ---------------- | ---------- | ------ |
| marketId | 市场名称   | 是               |            | String |
| coinId   | 币种名称   | 是               |            | String |
| high     | 最高价     | 是               | 统计24小时 | String |
| low      | 最低价     | 是               | 统计24小时 | String |
| range    | 涨跌幅     | 是               | 统计24小时 | String |
| volume   | 成交量     | 是               | 统计24小时 | String |
| newp     | 最新成交价 | 是               | 当前       | String |

### 7.  ORDER_BOOK_SPOT (深度数据)

请求参数说明

| 字段     | 说明     | 必填(是/否/可选) | 备注                       | 类型   |
| -------- | -------- | ---------------- | -------------------------- | ------ |
| coinPair | 币对名称 | 是               | 不选查所有(USDT-BTC)       | String |
| count    | 查询数量 | 可选             | 不选查所有(最多 买卖各200) | String |

返回结果说明

| 字段 | 说明 | 必填(是/否/可选) | 备注 | 类型 |
| ---- | ---- | ---------------- | ---- | ---- |
| b    | 买单 | 是               |      | list |
| s    | 卖单 | 是               |      | list |

事例:

​	"{\"b\":[\"1.00:1.00\"],\"s\":[\"2.00:2.00\"],\"type\":\"ORDERBOOK\"}"

  price : count    //    单价 : 数量  (没做合并)

##  [应答码对照表]

| code | msg                 | 说明                   | 备注             |
| ---- | ------------------- | ---------------------- | ---------------- |
| 0    | success             | 成功                   | 请求成功         |
| 9999 | system error        | 系统异常               | 联系开发人员     |
| 9000 | missing parameter   | 公共参数缺失或者不存在 | 检查公共参数     |
| 9001 | Version not matched | 版本不匹配             | 检查调用API 版本 |
| 9004 | access denied       | 访问受限制             | 检查绑定的ip     |
| 9005 | key expired         | apikey 失效            |                  |
|      |                     |                        |                  |
|      |                     |                        |                  |
|      |                     |                        |                  |
|      |                     |                        |                  |

