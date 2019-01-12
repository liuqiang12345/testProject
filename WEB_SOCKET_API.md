## [WEB_SOCKET行情 API]

| 版本   | 说明 | 备注 |
| ------ | ---- | ---- |
| v1.0.0 |      | 可用 |
|        |      |      |
|        |      |      |



### 链接地址:

wss接口的url为: wss://192.168.1.1/

### 请求说明:

1. 直接访问时URL格式为 /ws/spot

### 订阅事件:

- 订阅行情事件 payload 格式:{"code":"3","data":"BB_COIN-MARKET"}

- 其中 code 业务编码 参考事件code表

- 其中data 固定格式**BB _ 币种名称 - 市场名称**

- 事件订阅返回init数据格式

  ```java
  {
  	"code": 4,
  	"data": {
  		"data": {
  			"realTimeList": [],//实时交易数据
  			"orderbook": { 
  				"b": [], //买
  				"s": [], //卖
  				"type": "ORDERBOOK"`
  			},
  			"first": "first"
  		},
  		"wsproperty": "init"`
  	}
  }
  ```

  

### 心跳事件 

- 心跳事件 payload {"code":"0,"data:""}  
- 客户端需要每隔10s 主动发送 ping 帧  确保链接可用

### 行情数据推送

- orderbook payload

```java
{
	"code": 4,
	"data": {
		"data": {
			"b": [],  //买
			"s": [],  //卖
			"symbol": "BTC-USDT",
			"type": "ORDERBOOK"
		},
		"wsproperty": "RealtimeDate"
	}
}


```



- trsde payload

```java
{
	"code": 4,
	"data": {
		"data": {
			"p": "1.00", //价格
			"s": "buy", 
			"symbol": "BTC-USDT", //币种-市场
			"t": "1547276117", //时间
			"type": "TRADE",
			"v": "0.70", 成交数量
			"ver": "429.00" 自增id
		},
		"wsproperty": "RealtimeDate"
	}
}


```







| 事件code | 说明                |
| -------- | ------------------- |
| 0        | ping                |
| 3        | spot 订阅           |
| 4        | spot 服务端推送事件 |

