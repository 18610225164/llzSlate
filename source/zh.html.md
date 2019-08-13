---
title: FlashExpress 开放平台接口

language_tabs:
  - http: HTTP

toc_footers:
  - <a href="/">English</a>
  - <a href="http://www.flashexpress.com">Flash Express</a>

includes: []

search: true

headingLevel: 2
---

# 开放平台接口介绍
欢迎使用

# 入口地址
API Base Endpoints:

Env        | Endpoint
---        | --------
Sandbox    | https://api-sandbox.flashexpress.com
Production | https://api.flashexpress.com

# 接口规范
## 基本规范

* 传输方式 为保证安全性, 采用 HTTPS 传输
* 提交方式 采用 POST 方法提交
* 数据格式 提交数据采用 `application/x-www-form-urlencoded` 格式, 返回数据采用 `json` 格式
* 字符编码 统一采用 UTF-8 字符编码
* 签名算法 SHA256
* 签名要求 请求数据需要校验签名, 详细方法请参考安全规范-签名算法

## 返回数据基本结构

> 返回数据基本结构示例

```json
{
  "code": 1,
  "message": "success",
  "data": null
}
```

接口返回数据格式为 JSON, 接口返回数据的基本结构包含三个参数

|名称|数据类型|说明|
|---|---|---|
|code|integer|[返回码](#response_code)|
|message|string|错误消息|
|data|object|业务数据, 参考各个接口返回数据|

## 国际化

> 国际化调用示例

```http
POST /open/v1/ping HTTP/1.1
Host: api-sandbox.flashexpress.com
Accept: application/json
Accept-Language: th
```

目前接口支持中文(`zh-CN`)和泰文(`th`). 需要注意 **目前行政区划数据不支持国际化，只支持泰文**

可以通过添加 HTTP Request Header: `Accept-Language` 来切换语言

## 注意事项

<aside class="notice">
重量单位为克(g), 调用接口之前必须先将千克转换成克, 比如: 2Kg -> 2000g
</aside>

<aside class="notice">
金额货币虚拟 分 概念单位, 1泰铢 = 100分, 调用接口前必须将泰铢转换成分
</aside>

# 安全规范
## 签名算法
签名生成的通用步骤如下：

> a. 假设传送的参数

```yml
mch_id: 5a7bdfd22593414adb72df5f
nonce_str: yyv6YJP436wCkdpNdghC
body: test
```

> b. 对参数按照key=value的格式，并按照参数名ASCII字典序排序如下：

```yml
stringA="body=test&mch_id=5a7bdfd22593414adb72df5f&nonce_str=yyv6YJP436wCkdpNdghC"
```

> c. 拼接API密钥:

```yml
stringSignTemp=stringA+"&key=dd807a8e18b40153888e5a9864e70080" //注:key为平台预分配的密钥key

sign=sha256(stringSignTemp).toUpperCase()="BF46380E893F6B8F5486D2EDFE265BB8942C6A9BD5C897E34373734CE08B4B67" // 注:SHA256签名方式
```

> d. 最终发送参数如:

```yml
mch_id: 5a7bdfd22593414adb72df5f
nonce_str: yyv6YJP436wCkdpNdghC
body: test
sign: BF46380E893F6B8F5486D2EDFE265BB8942C6A9BD5C897E34373734CE08B4B67
```

第一步, 设所有发送或者接收到的数据为集合 M, 将集合 M 内非空参数值的参数按照参数名 ASCII 码从小到大排序(字典序), 使用 URL 键值对的格式 (即 `key1=value1&key2=value2…`) 拼接成字符串 stringA.
特别注意以下重要规则:

* 参数名 ASCII 码从小到大排序(字典序)
* 如果参数的值为空不参与签名
* 参数名区分大小写
* 验证调用返回或主动通知签名时, 传送的 sign 参数不参与签名, 将生成的签名与该 sign 值作校验
* 接口可能增加字段, 验证签名时必须支持增加的扩展字段

第二步, 在 stringA 最后拼接上 key 得到 stringSignTemp 字符串, 并对 stringSignTemp 进行 SHA256 运算, 再将得到的字符串所有字符转换为大写, 得到 sign 值 signValue.

<aside class="success">
安全密钥 key 请联系官方支持人员获取或重置
</aside>

## 注意事项

> 传输空值是被允许的, 但不参与签名

```css
/* 假设 body 为空值 */
body = " \t\n"

/* 则 body 不参与签名, 但是依然可以传输 */
body=%20%09%0A&mch_id=5a7bdfd22593414adb72df5f&nonce_str=yyv6YJP436wCkdpNdghC&sign=E893F6B8F5486D2EDFE265BB8942C6A9BD5C897E34373734CE08B4B67BF46380
```

<aside class="notice">
如果有某个值为空, 可以正常传输, 但计算签名时不要计算在内. 空值指完全由空白字符构成的字符串, 空白字符如下：
</aside>

* `\u0009` 水平制表符 `\t`
* `\u000A` 换行 `\n`
* `\u000B` 纵向制表符
* `\u000C` 换页 `\f`
* `\u000D` 回车 `\r`
* `\u001C` 文件分隔符
* `\u001D` 组分隔符
* `\u001E` 记录分隔符
* `\u001F` 单元分隔符

> 数据在签名完成后, 传输之前需要进行 urlencode

```css
/* 假设 body 值如下 */
body = "乔峰&慕容"

/* 传输数据如下 */
body=%E4%B9%94%E5%B3%B0%26%E6%85%95%E5%AE%B9&mch_id=5a7bdfd22593414adb72df5f&nonce_str=yyv6YJP436wCkdpNdghC&sign=E34373734CE08B4B67BF46380E893F6B8F5486D2EDFE265BB8942C6A9BD5C897
```

<aside class="notice">
您已经选用的是 application/x-www-form-urlencoded, 所以不要忘记对传输的每个 value 做 urlencode()
</aside>

<aside class="notice">
计算签名是在 urlencode 之前签名. 开放平台的服务端验证签名前也会先做 urldecode().
</aside>

## 生成随机数算法
开放平台接口规范中包含字段 `nonce_str`, 主要保证签名不可预测. 我们推荐生成随机数算法如下: 调用随机数函数生成, 将得到的值转换为字符串.

# 维护数据 API
## 查询商户所有仓库 `/warehouses`

```http
POST /open/v1/warehouses HTTP/1.1
Host: api-sandbox.flashexpress.com
Content-Type: application/x-www-form-urlencoded
Accept: application/json
```

> 请求示例

```css
mchId=UBP18100020&nonceStr=1525314174723&sign=7DE4B504A237BDC961A30A7BF1C9AFF6FB6F386C02A2F7E1FD4F0B1B6B01FB00
```

> 请求示例参数详情

```yaml
mchId: 'UBP18100020'
nonceStr: '1525314174723'
sign: '7DE4B504A237BDC961A30A7BF1C9AFF6FB6F386C02A2F7E1FD4F0B1B6B01FB00'
```

`POST /open/v1/warehouses`

查询商户所有仓库

### 请求参数

|名称|数据类型|是否必填|说明|
|---|---|---|---|
|mchId|string(32)|true|商户号|
|nonceStr|string(32)|true|随机数|
|sign|string(64)|true|签名|

> 返回数据体示例

```json
{
	"code": 1,
	"message": "success",
	"data": [{
		"warehouseNo": "UBP18100020_001",
		"name": "UBP18100020_001",
		"countryName": "Thailand",
		"provinceName": "อุบลราชธานี",
		"cityName": "เมืองอุบลราชธานี",
		"districtName": "แจระแม",
		"postalCode": "34000",
		"detailAddress": "68/5-6 ม.1 บ้านท่าบ่อ",
		"phone": "0630101454",
		"srcName": "หอมรวม"
	}, {
		"warehouseNo": "UBP18100020_002",
		"name": "UBP18100020_002",
		"countryName": "Thailand",
		"provinceName": "กรุงเทพ",
		"cityName": "บางแค",
		"districtName": "บางแค",
		"postalCode": "10160",
		"detailAddress": "11/369 ซ.53 หมู่ 7 ถ.กาญจนาภิเษก",
		"phone": "0888027955",
		"srcName": "เอกรินทร์"
	}]
}
```

### 返回数据体

|名称|数据类型|说明|
|---|---|---|
|warehouseNo|string(32)|商户仓库编号|
|countryName|string(100)|仓库国家名称|
|provinceName|string(150)|仓库省名称(一级行政区划)|
|cityName|string(150)|仓库市名称(二级行政区划)|
|districtName|string(150)|仓库区名称(三级行政区划)|
|postalCode|string(20)|仓库邮政编码|
|detailAddress|string(200)|仓库详细地址|
|phone|string(20)|寄件人联系方式|
|srcName|string(50)|寄件人姓名|

# 订单 API
## 创建订单 `/orders`

```http
POST /open/v1/orders HTTP/1.1
Host: api-sandbox.flashexpress.com
Content-Type: application/x-www-form-urlencoded
Accept: application/json
```

> 请求示例

```css
mchId=UBP18100020&nonceStr=1526461166805&sign=CD03E4D230D0824E804D2AB013879E39A75238C1230214840C6A31C9DF169BF5&outTradeNo=1526461166805&warehouseNo=UBP18100020_001&srcName=%E0%B8%AB%E0%B8%AD%E0%B8%A1%E0%B8%A3%E0%B8%A7%E0%B8%A1&srcPhone=0630101454&srcProvinceName=%E0%B8%AD%E0%B8%B8%E0%B8%9A%E0%B8%A5%E0%B8%A3%E0%B8%B2%E0%B8%8A%E0%B8%98%E0%B8%B2%E0%B8%99%E0%B8%B5&srcCityName=%E0%B9%80%E0%B8%A1%E0%B8%B7%E0%B8%AD%E0%B8%87%E0%B8%AD%E0%B8%B8%E0%B8%9A%E0%B8%A5%E0%B8%A3%E0%B8%B2%E0%B8%8A%E0%B8%98%E0%B8%B2%E0%B8%99%E0%B8%B5&srcPostalCode=34000&srcDetailAddress=68%2F22-6%E0%B8%A1.1%E0%B8%9A%E0%B9%89%E0%B8%B2%E0%B8%99%E0%B8%97%E0%B9%88%E0%B8%B2%E0%B8%9A%E0%B9%88%E0%B8%AD&dstName=%E0%B8%99%E0%B9%89%E0%B8%B3%E0%B8%9E%E0%B8%A3%E0%B8%B4%E0%B8%81%E0%B9%81%E0%B8%A1%E0%B9%88%E0%B8%AD%E0%B8%B3%E0%B8%9E%E0%B8%A3&dstPhone=0970209976&dstHomePhone=0970220220&dstProvinceName=%E0%B9%80%E0%B8%8A%E0%B8%B5%E0%B8%A2%E0%B8%87%E0%B9%83%E0%B8%AB%E0%B8%A1%E0%B9%88&dstCityName=%E0%B8%AA%E0%B8%B1%E0%B8%99%E0%B8%97%E0%B8%A3%E0%B8%B2%E0%B8%A2&dstPostalCode=50210&dstDetailAddress=127+%E0%B8%AB%E0%B8%A1%E0%B8%B9%E0%B9%88+3+%E0%B8%95.%E0%B8%AB%E0%B8%99%E0%B8%AD%E0%B8%87%E0%B9%81%E0%B8%AB%E0%B8%A2%E0%B9%88%E0%B8%87+%E0%B8%AD.%E0%B8%AA%E0%B8%B1%E0%B8%99%E0%B8%97%E0%B8%A3%E0%B8%B2%E0%B8%A2+%E0%B8%88.%E0%B9%80%E0%B8%8A%E0%B8%B5%E0%B8%A2%E0%B8%87%E0%B9%83%E0%B8%AB%E0%B8%A1%E0%B9%88&articleCategory=1&expressCategory=1&weight=1000&insured=1&insureDeclareValue=100000&codEnabled=1&codAmount=100000&remark=%E0%B8%82%E0%B8%B6%E0%B9%89%E0%B8%99%E0%B8%9A%E0%B8%B1%E0%B8%99%E0%B9%84%E0%B8%94
```

> 请求示例参数说明

```yaml
mchId: 'UBP18100020'
nonceStr: '1526461166805'
sign: 'CD03E4D230D0824E804D2AB013879E39A75238C1230214840C6A31C9DF169BF5'
outTradeNo: '1526461166805'
warehouseNo: 'UBP18100020_001'
expressCategory: 1
srcName: 'หอมรวม'
srcPhone: '0630101454'
srcProvinceName: 'อุบลราชธานี'
srcCityName: 'เมืองอุบลราชธานี'
srcPostalCode: '34000'
srcDetailAddress: '68/22-6ม.1บ้านท่าบ่อ'
dstName: 'น้ำพริกแม่อำพร'
dstPhone: '0970209976'
dstHomePhone: '0970220220'
dstProvinceName: 'เชียงใหม่'
dstCityName: 'สันทราย'
dstPostalCode: '50210'
dstDetailAddress: '127หมู่3ต.หนองแหย่งอ.สันทรายจ.เชียงใหม่'
articleCategory: 1
weight: 1000
insured: 1
insureDeclareValue: 100000
codEnabled: 1
codAmount: 100000
remark: 'ขึ้นบันได'
```

`POST /open/v1/orders`

创建订单


### 请求参数

|名称|数据类型|是否必填|说明|
|---|---|---|---|
|mchId|string(32)|true|商户号|
|nonceStr|string(32)|true|随机数|
|sign|string(64)|true|签名|
|outTradeNo|string(64)|true|商户订单号|
|expressCategory|integer|true|[产品类型](#express_category)(必须为 1)|
|warehouseNo|string(32)|false|寄件人商户仓库编号.必须是通过接口[getAllWarehouses](#getallwarehouses-warehouses)查询的商户仓库编号.|
|srcName|string(50)|false|寄件人姓名|
|srcPhone|string(20)|false|寄件人联系方式|
|srcProvinceName|string(150)|false|寄件人省名称(一级行政区划)|
|srcCityName|string(150)|false|寄件人市名称(二级行政区划)|
|srcPostalCode|string(20)|false|寄件人邮政编码|
|srcDetailAddress|string(200)|false|寄件人的详细地址|
|dstName|string(50)|true|收件人姓名|
|dstPhone|string(20)|true|收件人联系方式|
|dstHomePhone|string(20)|true|收件人家庭联系方式或第二联系方式|
|dstProvinceName|string(150)|true|收件人省名称(一级行政区划)|
|dstCityName|string(150)|true|收件人市名称(二级行政区划)|
|dstPostalCode|string(20)|true|收件人邮政编码|
|dstDetailAddress|string(200)|true|收件人的详细地址|
|articleCategory|integer|true|[物品类型](#article_category)|
|weight|integer|true|重量(单位: 克)|
|insured|integer|true|是否保价(1: 保价 0: 不保价)|
|insureDeclareValue|integer|false|物品金额(单位: 分)|
|codEnabled|integer|true|是否 COD(1: 是 0: 否)|
|codAmount|integer|false|COD 金额(单位: 分)|
|remark|string(200)|false|备注|

> 返回数据体示例

```json
{
	"code": 1,
	"message": "success",
	"data": {
		"pno": "TH47144P18",
		"mchId": "UBP18100020",
		"outTradeNo": "1526461166805"
	}
}
```

### 返回数据体

|名称|数据类型|说明|
|---|---|---|
|pno|string(20)|Flash Express 运单号|
|mchId|string(32)|商户号|
|outTradeNo|string(64)|商户订单号|

## 作废订单 `/orders/{pno}/cancel`

```http
POST /open/v1/orders/TH4714A27/cancel HTTP/1.1
Host: api-sandbox.flashexpress.com
Content-Type: application/x-www-form-urlencoded
Accept: application/json
```
`POST /open/v1/orders/{pno}/cancel`

作废订单

### 请求路径参数 (不参与签名)

|名称|数据类型|是否必填|说明|
|---|---|---|---|
|pno|string(20)|true|Flash Express 运单号|

> 请求示例

```css
mchId=UBP18100020&nonceStr=1525312687777&sign=6981B676CF590F1F9BDC5529AF77B8396D70153F4E01FF781069261F35497389
```

> 请求示例参数说明

```yaml
mchId: 'UBP18100020'
nonceStr: '1525312687777'
sign: '6981B676CF590F1F9BDC5529AF77B8396D70153F4E01FF781069261F35497389'
```

### 请求参数

|名称|数据类型|是否必填|说明|
|---|---|---|---|
|mchId|string(32)|true|商户号|
|nonceStr|string(32)|true|随机数|
|sign|string(64)|true|签名|

> 返回数据体: 无

### 返回数据体

无

## 路由查询 `/orders/{pno}/routes`

```http
POST /open/v1/orders/TH01011C27/routes HTTP/1.1
Host: api-sandbox.flashexpress.com
Accept: application/json
```

`POST /open/v1/orders/{pno}/routes`

查询包裹路由

### 请求路径参数 (不参与签名)

|名称|数据类型|是否必填|说明|
|---|---|---|---|
|pno|string(20)|true|Flash Express 运单号|

> 请求示例

```css
mchId=AA0005&nonceStr=1525315650958&sign=EB1C59F14A1A616DE861E7F09C0FD2949B64EAD98B3D649EA2D73CA0328D77B7
```

> 请求示例参数说明

```yaml
mchId: 'AA0005'
nonceStr: '1525315650958'
sign: 'EB1C59F14A1A616DE861E7F09C0FD2949B64EAD98B3D649EA2D73CA0328D77B7'
```

### 请求参数
|名称|数据类型|是否必填|说明|
|---|---|---|---|
|mchId|string(32)|true|商户号|
|nonceStr|string(32)|true|随机数|
|sign|string(64)|true|签名|

> 返回数据体示例

```json
{
	"code": 1,
	"message": "success",
	"data": {
		"pno": "TH01011C27",
		"state": 5,
		"stateText": "เซ็นรับแล้ว",
		"routes": [{
			"routedAt": 1523357001,
			"routeAction": "DELIVERY_CONFIRM",
			"message": "พัสดุของคุณถูกเซ็นรับแล้ว เซ็นรับโดย TH01011C27"
		}, {
			"routedAt": 1523356924,
			"routeAction": "DELIVERY_TICKET_CREATION_SCAN",
			"message": "มีพัสดุรอการนำส่ง กรุณารอการติดต่อจากเจ้าหน้าที่ Flash Express"
		}, {
			"routedAt": 1523356560,
			"routeAction": "SHIPMENT_WAREHOUSE_SCAN",
			"message": "พัสดุของคุณอยู่ที่ กทม. จะถูกส่งไปยัง จตุโชติ-DC"
		}, {
			"routedAt": 1523356029,
			"routeAction": "RECEIVED",
			"message": "zhao=DC พนักงานเข้ารับพัสดุแล้ว"
		}]
	}
}
```

### 返回数据体

|名称|数据类型|说明|
|---|---|---|
|pno|string(20)|Flash Express 运单号|
|state|integer|包裹状态|
|stateText|string(100)|包裹状态描述|
|routes.routedAt|integer|路由发生时间戳 UTC|
|routes.routedAction|string(29)|路由发生动作|
|routes.message|string(500)|路由消息|

## 打印 `/orders/{pno}/pre_print`

```http
POST /open/v1/orders/TH01011C27/pre_print HTTP/1.1
Host: api-sandbox.flashexpress.com
Accept: application/pdf
```

`POST /open/v1/orders/{pno}/pre_print`

打印包裹面单

### 请求路径参数 (不参与签名)

|名称|数据类型|是否必填|说明|
|---|---|---|---|
|pno|string(20)|true|Flash Express 运单号|

> 请求示例

```css
mchId=AA0005&nonceStr=1525316377546&sign=D500B8379DDB2CEDDBAF0130D1FF2A995FD3AFEFDE1EC90BBD2A1762F562FAE3
```

> 请求示例参数说明

```yaml
mchId: 'AA0005'
nonceStr: '1525316377546'
sign: 'D500B8379DDB2CEDDBAF0130D1FF2A995FD3AFEFDE1EC90BBD2A1762F562FAE3'
```

### 请求参数
|名称|数据类型|是否必填|说明|
|---|---|---|---|
|mchId|string(32)|true|商户号|
|nonceStr|string(32)|true|随机数|
|sign|string(64)|true|签名|

> 返回数据体 为面单 pdf 文件数据流

### 返回数据体

面单 pdf 文件数据流

## 创建个人消费者订单 `/orders/consumer`

```http
POST /open/v1/orders/consumer HTTP/1.1
Host: api-sandbox.flashexpress.com
Content-Type: application/x-www-form-urlencoded
Accept: application/json
```

> 请求示例

```css
mchId=UBP18100020&nonceStr=1530763467835&sign=8D08ACD66D6C3837BB03880A48D639312F76BB70C49DF0283C431AAB9828529B&customerPhone=0630101499&outTradeNo=1530763467835&srcName=%E0%B8%AB%E0%B8%AD%E0%B8%A1%E0%B8%A3%E0%B8%A7%E0%B8%A1&srcPhone=0630101454&srcProvinceName=%E0%B8%81%E0%B8%A3%E0%B8%B8%E0%B8%87%E0%B9%80%E0%B8%97%E0%B8%9E&srcCityName=%E0%B8%AB%E0%B8%99%E0%B8%AD%E0%B8%87%E0%B8%88%E0%B8%AD%E0%B8%81&srcDistrictName=%E0%B8%A5%E0%B8%B3%E0%B8%9C%E0%B8%B1%E0%B8%81%E0%B8%8A%E0%B8%B5&srcPostalCode=10530&srcDetailAddress=68%2F5-6+%E0%B8%A1.1+%E0%B8%9A%E0%B9%89%E0%B8%B2%E0%B8%99%E0%B8%97%E0%B9%88%E0%B8%B2%E0%B8%9A%E0%B9%88%E0%B8%AD&srcLat=13.80063275&srcLng=100.84433917&dstName=%E0%B8%99%E0%B9%89%E0%B8%B3%E0%B8%9E%E0%B8%A3%E0%B8%B4%E0%B8%81%E0%B9%81%E0%B8%A1%E0%B9%88%E0%B8%AD%E0%B8%B3%E0%B8%9E%E0%B8%A3&dstPhone=0970209976&dstHomePhone=0970220220&dstProvinceName=%E0%B9%80%E0%B8%8A%E0%B8%B5%E0%B8%A2%E0%B8%87%E0%B9%83%E0%B8%AB%E0%B8%A1%E0%B9%88&dstCityName=%E0%B8%AA%E0%B8%B1%E0%B8%99%E0%B8%97%E0%B8%A3%E0%B8%B2%E0%B8%A2&dstPostalCode=50210&dstDetailAddress=127+%E0%B8%AB%E0%B8%A1%E0%B8%B9%E0%B9%88+3+%E0%B8%95.%E0%B8%AB%E0%B8%99%E0%B8%AD%E0%B8%87%E0%B9%81%E0%B8%AB%E0%B8%A2%E0%B9%88%E0%B8%87+%E0%B8%AD.%E0%B8%AA%E0%B8%B1%E0%B8%99%E0%B8%97%E0%B8%A3%E0%B8%B2%E0%B8%A2+%E0%B8%88.%E0%B9%80%E0%B8%8A%E0%B8%B5%E0%B8%A2%E0%B8%87%E0%B9%83%E0%B8%AB%E0%B8%A1%E0%B9%88&articleCategory=1&expressCategory=1&weight=1000&insured=1&insureDeclareValue=10000&remark=%E0%B8%82%E0%B8%B6%E0%B9%89%E0%B8%99%E0%B8%9A%E0%B8%B1%E0%B8%99%E0%B9%84%E0%B8%94
```

> 请求示例参数说明

```yaml
mchId: 'UBP18100020'
nonceStr: '1530763467835'
sign: '8D08ACD66D6C3837BB03880A48D639312F76BB70C49DF0283C431AAB9828529B'
customerPhone: '0630101499'
outTradeNo: '1530763467835'
expressCategory: 1
srcName: 'หอมรวม'
srcPhone: '0630101454'
srcProvinceName: 'กรุงเทพ'
srcCityName: 'หนองจอก'
srcDistrictName: 'ลำผักชี'
srcPostalCode: '10530'
srcDetailAddress: '68/5-6 ม.1 บ้านท่าบ่อ'
srcLat: 13.80063275
srcLng: 100.84433917
dstName: 'น้ำพริกแม่อำพร'
dstPhone: '0970209976'
dstHomePhone: '0970220220'
dstProvinceName: 'เชียงใหม่'
dstCityName: 'สันทราย'
dstPostalCode: '50210'
dstDetailAddress: '127หมู่3ต.หนองแหย่งอ.สันทรายจ.เชียงใหม่'
articleCategory: 1
weight: 1000
insured: 1
insureDeclareValue: 100000
remark: 'ขึ้นบันได'
```

`POST /open/v1/orders/consumer`

创建个人消费者订单

### 请求参数

|名称|数据类型|是否必填|说明|
|---|---|---|---|
|mchId|string(32)|true|商户号|
|nonceStr|string(32)|true|随机数|
|sign|string(64)|true|签名|
|customerPhone|string(20)|true|个人消费者电话号码|
|outTradeNo|string(64)|true|商户订单号|
|expressCategory|integer|true|[产品类型](#express_category)(必须为 1)|
|srcName|string(50)|true|寄件人姓名|
|srcPhone|string(20)|true|寄件人联系方式|
|srcProvinceName|string(150)|false|寄件人省名称(一级行政区划)|
|srcCityName|string(150)|false|寄件人市名称(二级行政区划)|
|srcDistrictName|string(150)|false|寄件人区名称(三级行政区划)|
|srcPostalCode|string(20)|true|寄件人邮政编码|
|srcDetailAddress|string(200)|true|寄件人的详细地址|
|srcLat|decimal(11,8)|false|寄件人位置的纬度|
|srcLng|decimal(11,8)|false|寄件人位置的经度|
|dstName|string(50)|true|收件人姓名|
|dstPhone|string(20)|true|收件人联系方式|
|dstHomePhone|string(20)|true|收件人家庭联系方式或第二联系方式|
|dstProvinceName|string(150)|true|收件人省名称(一级行政区划)|
|dstCityName|string(150)|true|收件人市名称(二级行政区划)|
|dstPostalCode|string(20)|true|收件人邮政编码|
|dstDetailAddress|string(200)|true|收件人的详细地址|
|articleCategory|integer|true|[物品类型](#article_category)|
|weight|integer|true|重量(单位: 克)|
|insured|integer|true|是否保价(1: 保价 0: 不保价)|
|insureDeclareValue|integer|false|物品金额(单位: 分)|
|remark|string(200)|false|备注|

> 返回数据体示例

```json
{
	"code": 1,
	"message": "success",
	"data": {
		"orderId": "5b3d98f7a4c2130579510116",
		"customerPhone": "0630101499",
		"outTradeNo": "1530763467835"
	}
}
```

### 返回数据体

|名称|数据类型|说明|
|---|---|---|
|orderId|string(32)|Flash Express订单id|
|customerPhone|string(20)|个人消费者电话号码|
|outTradeNo|string(64)|商户订单号|

# 通知 API
## 通知快递员揽件 `/notify`

```http
POST /open/v1/notify HTTP/1.1
Host: api-sandbox.flashexpress.com
Content-Type: application/x-www-form-urlencoded
Accept: application/json
```

> 请求示例

```css
mchId=UBP18100020&nonceStr=1530089198055&sign=30C169401CF32A3B0A18B12F81EF6484D790ADC1EE839F494D37362B6C0FA47E&warehouseNo=UBP18100020_001&srcName=%E0%B8%AB%E0%B8%AD%E0%B8%A1%E0%B8%A3%E0%B8%A7%E0%B8%A1&srcPhone=0630101454&srcProvinceName=%E0%B8%AD%E0%B8%B8%E0%B8%9A%E0%B8%A5%E0%B8%A3%E0%B8%B2%E0%B8%8A%E0%B8%98%E0%B8%B2%E0%B8%99%E0%B8%B5&srcCityName=%E0%B9%80%E0%B8%A1%E0%B8%B7%E0%B8%AD%E0%B8%87%E0%B8%AD%E0%B8%B8%E0%B8%9A%E0%B8%A5%E0%B8%A3%E0%B8%B2%E0%B8%8A%E0%B8%98%E0%B8%B2%E0%B8%99%E0%B8%B5&srcPostalCode=34000&srcDetailAddress=68%2F5-6+%E0%B8%A1.1+%E0%B8%9A%E0%B9%89%E0%B8%B2%E0%B8%99%E0%B8%97%E0%B9%88%E0%B8%B2%E0%B8%9A%E0%B9%88%E0%B8%AD&estimateParcelNumber=9&remark=ASAP
```

> 请求示例参数说明

```yaml
mchId: 'UBP18100020'
nonceStr: '1530089198055'
sign: '30C169401CF32A3B0A18B12F81EF6484D790ADC1EE839F494D37362B6C0FA47E'
warehouseNo: 'UBP18100020_001'
srcName: 'หอมรวม'
srcPhone: '0630101454'
srcProvinceName: 'อุบลราชธานี'
srcCityName: 'เมืองอุบลราชธานี'
srcPostalCode: '34000'
srcDetailAddress: '68/5-6 ม.1 บ้านท่าบ่อ'
estimateParcelNumber: '9'
remark: 'ASAP'
```

`POST /open/v1/notify`

通知快递员揽件

### 请求参数

<aside class="notice">
关于揽件地址</br>
可以在接口中指定希望快递员去揽件的地址。</br>
如果请求参数{srcName、srcPhone、srcProvinceName、srcCityName、srcPostalCode、srcDetailAddress}全部填写了参数值，注意必须是这几个参数全部填写了参数值，那这几个参数代表的地址就是揽件地址。</br>
如果请求参数{srcName、srcPhone、srcProvinceName、srcCityName、srcPostalCode、srcDetailAddress}没有全部填写参数值，那必须填写请求参数"warehouseNo"的值为揽件地址。
</aside>

|名称|数据类型|是否必填|说明|
|---|---|---|---|
|mchId|string(32)|true|商户号|
|nonceStr|string(32)|true|随机数|
|sign|string(64)|true|签名|
|warehouseNo|string(32)|false|寄件人商户仓库编号.必须是通过接口[getAllWarehouses](#getallwarehouses-warehouses)查询的商户仓库编号.|
|srcName|string(50)|false|寄件人姓名|
|srcPhone|string(20)|false|寄件人联系方式|
|srcProvinceName|string(150)|false|寄件人省名称(一级行政区划)|
|srcCityName|string(150)|false|寄件人市名称(二级行政区划)|
|srcPostalCode|string(20)|false|寄件人邮政编码|
|srcDetailAddress|string(200)|false|寄件人的详细地址|
|estimateParcelNumber|integer|true|预估发件包裹数|
|remark|string(200)|false|备注|

> 返回数据体: 无

### 返回数据体

无

# 常量表
<h2 id="response_code">返回码 (code)</h2>
|Code|Meaning|
|---|---|
|1|成功|
|0|系统内部错误|
|1000|数据校验错误|
|1001|客户不存在|
|1002|签名不通过|
|1003|订单已存在|
|1004|发件地匹配不到|
|1005|目的地匹配不到|
|1006|订单不存在|
|1007|仓库不存在|
|1008|该用户暂未开通COD服务|
|1009|COD不能小于0|
|1010|当前客户存在未完成的揽件通知，无需发送通知|
|1015|已揽收的订单不可作废|
|1018|发件地和仓库不能都为空|
|1019|包裹号pno和客户mchId不匹配|
|1020|保价不能小于0|
|1021|发件地址邮政编码只能是5位的数字|
|1022|发件地址暂未开通服务|

<h2 id="express_category">产品类型 (expressCategory)</h2>
|Code|Meaning|
|---|---|
|1|标准配送|

<h2 id="article_category">物品类型 (articleCategory)</h2>
|Code|Meaning|
|---|---|
|0|文件|
|1|食品|
|2|日用品|
|3|数码产品|
|4|衣物|
|5|其他|

# Demo 下载
<h2 id="demo-csharp">C# Demo</h2>
<a href="download/CSharpDemo.rar">C# Demo</a>

<h2 id="demo-php">PHP Demo</h2>
<a href="download/flashDemo.php.zip">PHP Demo</a>

<h2 id="demo-java">Java Demo</h2>
<a href="download/OpenTest.java.zip">Java Demo</a>
