---
title: FlashExpress Open API Reference

language_tabs:
  - http: HTTP

toc_footers:
  - <a href="/zh.html">中文</a>
  - <a href="http://www.flashexpress.com">Flash Express</a>

includes: []

search: true

headingLevel: 2
---

# Open-API Reference
Welcome!

# Basic URL
API Basic Endpoints:

Env        | Endpoint
---        | --------
Sandbox    | https://api-sandbox.flashexpress.com
Production | https://api.flashexpress.com

# API Reference
## The Basic Standard

* Transfer Protocol: For the security, we use `HTTPS` protocol.
* Request Method: `POST`
* Data Format: Request datas use the `application/x-www-form-urlencoded` format, and the response data use the `json` format.
* Character Encoding: UTF-8
* Signature Algorithm: SHA256
* Signature Requires: The request data is required to verify signature. Please refer to the "The Security Reference - Signature Algorithm" for detail.

## The Basic Response Data Format

> Example for the Data Format:

```json
{
  "code": 1,
  "message": "success",
  "data": null
}
```

The API respond a `JSON` format data, and it includes three parameters as follow:

|Parameter|Type|Description|
|---|---|---|
|code|integer|[ResponseCode](#response_code)|
|message|string|Error Message|
|data|object|business data, please refer to the corresponding API|

## Internationalization Reference

> Example Code

```http
POST /open/v1/ping HTTP/1.1
Host: api-sandbox.flashexpress.com
Accept: application/json
Accept-Language: th
```

Our API supports Chinese(`zh-CN`)and Thai(`th`).
Notice: **We do not support the internationalization of administrative region, we only support Thai.**

You can add the parameter `Accept-Language` in HTTP Request Header to switch the language.

## Notices

<aside class="notice">
We use gram(g) as the weight basic unit. Before calling the API, you must convert the kilogram to gram. For example: 2KG -> 2000g
</aside>

<aside class="notice">
We make a virtual unit for an amount of money: cent.
We set 1THB = 100 cent. Before calling the API, you must convert THB to cent.
</aside>

# The Security Reference
## Signature Algorithm
The common step of make a signature:

> a. suppose the request data as below:

```yml
mchId: 5a7bdfd22593414adb72df5f
nonceStr: yyv6YJP436wCkdpNdghC
body: test
```

> b. Format the parameters with the format of `key=value`, and then sort by the ASCII code of parameter's name DESC.

```yml
stringA="body=test&mchId=5a7bdfd22593414adb72df5f&nonceStr=yyv6YJP436wCkdpNdghC"
```

> c. Join the API Key:

```yml
stringSignTemp=stringA+"&key=dd807a8e18b40153888e5a9864e70080" //Notice:key is supported by our system.

sign=sha256(stringSignTemp).toUpperCase()="BF46380E893F6B8F5486D2EDFE265BB8942C6A9BD5C897E34373734CE08B4B67" // Notice:SHA256 signature
```

> d. The final request data as below:

```yml
mchId: 5a7bdfd22593414adb72df5f
nonceStr: yyv6YJP436wCkdpNdghC
body: test
sign: BF46380E893F6B8F5486D2EDFE265BB8942C6A9BD5C897E34373734CE08B4B67
```

The first step, we suppose all of the request or respond data as a collection M, then sort the elements in M by the ASCII code of non-null parameter's name DESC. at last, join the parameter with the format of `key=value`( `key1=value1&key2=value2…`) as a new string named stringA.
Make attention to these important rules:

* Sort by the parameter's name with its ASCII code ASC.
* Do not sign a parameter if its value is null.
* The parameter's name is case sensitive.
* When verify the response or inform user to make a signature, the sign parameter should not be signed. Generate a signature and check it with the value of sign parameter.
* The API maybe be added parameters, so you must verify it if it is a increasing parameter.

The second step, join the key string at the end of stringA, and we get a string stringSignTemp. Make a SHA256 operation to stringSignTemp and convert the result string to uppercase, then we get the sign's value named signValue.

<aside class="success">
About The security key:Please contact the official support staff to obtain or reset.
</aside>

## Notice

> Passing blank values is allowed, but not involved in signature

```css
/* suppose body parameter is blank */
body = " \t\n"

/* exclude the body parameter when calculating the signature, but it can still be send to open platform server */
body=%20%09%0A&mchId=5a7bdfd22593414adb72df5f&nonceStr=yyv6YJP436wCkdpNdghC&sign=E893F6B8F5486D2EDFE265BB8942C6A9BD5C897E34373734CE08B4B67BF46380
```

<aside class="notice">
If a value is blank, it can be send to open platform server normally, but it must not be included when calculating the signature. Blank value means that a string consisting entirely of whitespace characters, whitespace characters as follows:
</aside>

* `\u0009` HORIZONTAL TABULATION `\t`
* `\u000A` LINE FEED `\n`
* `\u000B` VERTICAL TABULATION
* `\u000C` FORM FEED `\f`
* `\u000D` CARRIAGE RETURN `\r`
* `\u001C` FILE SEPARATOR
* `\u001D` GROUP SEPARATOR
* `\u001E` RECORD SEPARATOR
* `\u001F` UNIT SEPARATOR

> data must be urlencoded before sending to open platform server and after calculating signature

```css
/* suppose the body value is as follows */
body = "Lisa&Ruby"

/* request body */
body=Lisa%26Ruby&mchId=5a7bdfd22593414adb72df5f&nonceStr=yyv6YJP436wCkdpNdghC&sign=E34373734CE08B4B67BF46380E893F6B8F5486D2EDFE265BB8942C6A9BD5C897
```

<aside class="notice">
As you have chosen application/x-www-form-urlencoded, do not forget to invoke urlencode() for every value.
</aside>

<aside class="notice">
Calculate the signature before invoking urlencode(). Meanwhile, the open platform server will verify signature after invoking urldecode().
</aside>

## Random nonce string algorithm
the `nonceStr` parameter in this open platform API specification, is used to ensure the signature is unpredictable. The algorithm Ee recommend is as follows: call random number generating function, convert the resulting value to string.

# Management API
## getAllWarehouses `/warehouses`

```http
POST /open/v1/warehouses HTTP/1.1
Host: api-sandbox.flashexpress.com
Content-Type: application/x-www-form-urlencoded
Accept: application/json
```

> request body example

```css
mchId=UBP18100020&nonceStr=1525314174723&sign=7DE4B504A237BDC961A30A7BF1C9AFF6FB6F386C02A2F7E1FD4F0B1B6B01FB00
```

> parameter description

```yaml
mchId: 'UBP18100020'
nonceStr: '1525314174723'
sign: '7DE4B504A237BDC961A30A7BF1C9AFF6FB6F386C02A2F7E1FD4F0B1B6B01FB00'
```

`POST /open/v1/warehouses`

getAllWarehouses

### Request Parameters

|Name|Type|Required|Description|
|---|---|---|---|
|mchId|string(32)|true|merchant no|
|nonceStr|string(32)|true|random nonce string|
|sign|string(64)|true|signature|

> response body example

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

### Response Body

|Name|Type|Description|
|---|---|---|
|warehouseNo|string(32)|shipper warehouse no.|
|countryName|string(100)|warehouse's province name|
|provinceName|string(150)|warehouse's province name|
|cityName|string(150)|warehouse's city name.city is a sub unit of province.|
|districtName|string(150)|warehouse's district name.district is a sub unit of city.|
|postalCode|string(20)|warehouse's postal code|
|detailAddress|string(200)|warehouse's detail address|
|phone|string(20)|shipper's phone number|
|srcName|string(50)|shipper's name|

# Order API
## Create order `/orders`

```http
POST /open/v1/orders HTTP/1.1
Host: api-sandbox.flashexpress.com
Content-Type: application/x-www-form-urlencoded
Accept: application/json
```

> request body example

```css
mchId=AA0005&nonceStr=1536749552628&sign=D4515A46B6094589F1F7615ADCC988FBB03A79010F2A206DC982F27D396F93A0&outTradeNo=%231536749552628%23&warehouseNo=AA0005_001&srcName=%E0%B8%AB%E0%B8%AD%E0%B8%A1%E0%B8%A3%E0%B8%A7%E0%B8%A1++create+order+test+name&srcPhone=0630101454&srcProvinceName=%E0%B8%AD%E0%B8%B8%E0%B8%9A%E0%B8%A5%E0%B8%A3%E0%B8%B2%E0%B8%8A%E0%B8%98%E0%B8%B2%E0%B8%99%E0%B8%B5&srcCityName=%E0%B9%80%E0%B8%A1%E0%B8%B7%E0%B8%AD%E0%B8%87%E0%B8%AD%E0%B8%B8%E0%B8%9A%E0%B8%A5%E0%B8%A3%E0%B8%B2%E0%B8%8A%E0%B8%98%E0%B8%B2%E0%B8%99%E0%B8%B5&srcDistrictName=%E0%B9%83%E0%B8%99%E0%B9%80%E0%B8%A1%E0%B8%B7%E0%B8%AD%E0%B8%87&srcPostalCode=34000&srcDetailAddress=68%2F5-6+%E0%B8%A1.1+%E0%B8%9A%E0%B9%89%E0%B8%B2%E0%B8%99%E0%B8%97%E0%B9%88%E0%B8%B2%E0%B8%9A%E0%B9%88%E0%B8%AD+create+order+test+address&dstName=%E0%B8%99%E0%B9%89%E0%B8%B3%E0%B8%9E%E0%B8%A3%E0%B8%B4%E0%B8%81%E0%B9%81%E0%B8%A1%E0%B9%88%E0%B8%AD%E0%B8%B3%E0%B8%9E%E0%B8%A3&dstPhone=0970209976&dstHomePhone=0970220220&dstProvinceName=%E0%B9%80%E0%B8%8A%E0%B8%B5%E0%B8%A2%E0%B8%87%E0%B9%83%E0%B8%AB%E0%B8%A1%E0%B9%88&dstCityName=%E0%B8%AA%E0%B8%B1%E0%B8%99%E0%B8%97%E0%B8%A3%E0%B8%B2%E0%B8%A2&dstDistrictName=%E0%B8%AA%E0%B8%B1%E0%B8%99%E0%B8%9E%E0%B8%A3%E0%B8%B0%E0%B9%80%E0%B8%99%E0%B8%95%E0%B8%A3&dstPostalCode=50210&dstDetailAddress=127+%E0%B8%AB%E0%B8%A1%E0%B8%B9%E0%B9%88+3+%E0%B8%95.%E0%B8%AB%E0%B8%99%E0%B8%AD%E0%B8%87%E0%B9%81%E0%B8%AB%E0%B8%A2%E0%B9%88%E0%B8%87+%E0%B8%AD.%E0%B8%AA%E0%B8%B1%E0%B8%99%E0%B8%97%E0%B8%A3%E0%B8%B2%E0%B8%A2+%E0%B8%88.%E0%B9%80%E0%B8%8A%E0%B8%B5%E0%B8%A2%E0%B8%87%E0%B9%83%E0%B8%AB%E0%B8%A1%E0%B9%88+create+order+test+address&articleCategory=1&expressCategory=1&weight=1000&insured=1&insureDeclareValue=10000&codEnabled=1&codAmount=10000&remark=%E0%B8%82%E0%B8%B6%E0%B9%89%E0%B8%99%E0%B8%9A%E0%B8%B1%E0%B8%99%E0%B9%84%E0%B8%94
```

> parameter description

```yaml
mchId: 'AA0005'
nonceStr: '1536749552628'
sign: 'D4515A46B6094589F1F7615ADCC988FBB03A79010F2A206DC982F27D396F93A0'
outTradeNo: '#1536749552628#'
warehouseNo: 'AA0005_001'
srcName: 'หอมรวม  create order test name'
srcPhone: '0630101454'
srcProvinceName: 'อุบลราชธานี'
srcCityName: 'เมืองอุบลราชธานี'
srcDistrictName: 'ในเมือง'
srcPostalCode: '34000'
srcDetailAddress: '68/5-6 ม.1 บ้านท่าบ่อ create order test address'
dstName: 'น้ำพริกแม่อำพร'
dstPhone: '0970209976'
dstHomePhone: '0970220220'
dstProvinceName: 'เชียงใหม่'
dstCityName: 'สันทราย'
dstDistrictName: 'สันพระเนตร'
dstPostalCode: '50210'
dstDetailAddress: '127 หมู่ 3 ต.หนองแหย่ง อ.สันทราย จ.เชียงใหม่ create order test address'
articleCategory: 1
expressCategory: 1
weight: 1000
insured: 1
insureDeclareValue: 10000
codEnabled: 1
codAmount: 10000
remark: 'ขึ้นบันได
```

`POST /open/v1/orders`

create order

### Request Parameters

<aside class="notice">
About shipper's information address.</br>
You could specify shipper's information of the parcel in 2 ways.</br>
1st,If you input values of all request parameters of {srcName,srcPhone,srcProvinceName,srcCityName,srcPostalCode,srcDetailAddress}, notice that these several parameters must be all input values,  these parameters are the shipper's information.</br>
2nd,If you don't input value of any request parameter of {srcName,srcPhone,srcProvinceName,srcCityName,srcPostalCode,srcDetailAddress}, you must input value of request parameter "warehouseNo" to show the shipper's information.
</aside>

|Name|Type|Required|Description|
|---|---|---|---|
|mchId|string(32)|true|merchant no|
|nonceStr|string(32)|true|random nonce string|
|sign|string(64)|true|signature|
|outTradeNo|string(64)|true|merchant order no|
|expressCategory|integer|true|[Express Category](#express_category)(must be 1)|
|warehouseNo|string(32)|false|shipper warehouse no.must be one value returned by API [getAllWarehouses](#getallwarehouses-warehouses).|
|srcName|string(50)|false|shipper's name|
|srcPhone|string(20)|false|shipper's phone number|
|srcProvinceName|string(150)|false|shipper's province name|
|srcCityName|string(150)|false|shipper's city name.city is a sub unit of province.|
|srcDistrictName|string(150)|false|shipper's district name.district is a sub unit of city.|
|srcPostalCode|string(20)|false|shipper's postal code|
|srcDetailAddress|string(200)|false|shipper's detail address|
|dstName|string(50)|true|consignee's name|
|dstPhone|string(20)|true|consignee's phone number|
|dstHomePhone|string(20)|false|consignee's home phone number or consigne's second phone number|
|dstProvinceName|string(150)|true|consignee's province name|
|dstCityName|string(150)|true|consignee's city name.city is a sub unit of province.|
|dstDistrictName|string(150)|false|consignee's district name.district is a sub unit of city.|
|dstPostalCode|string(20)|true|consignee's postal code|
|dstDetailAddress|string(200)|true|consignee's detail address|
|articleCategory|integer|true|[Article Category](#article_category)|
|weight|integer|true|article weight(unit: gram)|
|insured|integer|true|whether insured(1: yes 0: no)|
|insureDeclareValue|integer|false|article declare value for insurance(unit: cent)|
|codEnabled|integer|true|whether COD(1: yes 0: no)|
|codAmount|integer|false|COD amount(unit: cent)|
|remark|string(200)|false|remark|

> response body example

```json
{
	"code": 1,
	"message": "success",
	"data": {
		"pno": "TH47146DR36",
		"mchId": "AA0005",
		"outTradeNo": "#1536749552628#",
		"sortCode": "01N-CNXY-00",
		"dstStoreName": "เชียงใหม่-DC"
	}
}
```

### Response Body

|Name|Type|Description|
|---|---|---|
|pno|string(20)|Flash Express tracking number|
|mchId|string(32)|merchant no|
|outTradeNo|string(64)|merchant order no|
|sortCode|string(13)|Flash Express sorting code|
|dstStoreName|string(50)|Flash Store Name to deliver the parcel|

## Cancel order `/orders/{pno}/cancel`

```http
POST /open/v1/orders/TH4714A27/cancel HTTP/1.1
Host: api-sandbox.flashexpress.com
Content-Type: application/x-www-form-urlencoded
Accept: application/json
```
`POST /open/v1/orders/{pno}/cancel`

cancel order

### Path Variables (not involved in the signature)

|Name|Type|Required|Description|
|---|---|---|---|
|pno|string(20)|true|Flash Express tracking number|

> request body example

```css
mchId=UBP18100020&nonceStr=1525312687777&sign=6981B676CF590F1F9BDC5529AF77B8396D70153F4E01FF781069261F35497389
```

> parameter description

```yaml
mchId: 'UBP18100020'
nonceStr: '1525312687777'
sign: '6981B676CF590F1F9BDC5529AF77B8396D70153F4E01FF781069261F35497389'
```

### Request Parameters

|Name|Type|Required|Description|
|---|---|---|---|
|mchId|string(32)|true|merchant no|
|nonceStr|string(32)|true|random nonce string|
|sign|string(64)|true|signature|

> response body: none

### Response Body

None.

## Tracking order `/orders/{pno}/routes`

```http
POST /open/v1/orders/TH01011C27/routes HTTP/1.1
Host: api-sandbox.flashexpress.com
Accept: application/json
```

`POST /open/v1/orders/{pno}/routes`

tracking order

### Path Variables (not involved in the signature)

|Name|Type|Required|Description|
|---|---|---|---|
|pno|string(20)|true|Flash Express tracking number|

> request body example

```css
mchId=AA0005&nonceStr=1525315650958&sign=EB1C59F14A1A616DE861E7F09C0FD2949B64EAD98B3D649EA2D73CA0328D77B7
```

> parameter description

```yaml
mchId: 'AA0005'
nonceStr: '1525315650958'
sign: 'EB1C59F14A1A616DE861E7F09C0FD2949B64EAD98B3D649EA2D73CA0328D77B7'
```

### Request Parameters
|Name|Type|Required|Description|
|---|---|---|---|
|mchId|string(32)|true|merchant no|
|nonceStr|string(32)|true|random nonce string|
|sign|string(64)|true|signature|

> response body example

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

### Response Body

|Name|Type|Description|
|---|---|---|
|pno|string(20)|Flash Express tracking number|
|state|integer|parcel state|
|stateText|string(100)|parcel state description|
|routes.routedAt|integer|route status timestamp in UTC|
|routes.routedAction|string(29)|route status action|
|routes.message|string(500)|route status message|

## getParcelDeliveredInfo `/orders/{pno}/deliveredInfo`

```http
POST /open/v1/orders/TH01011C27/deliveredInfo HTTP/1.1
Host: api-sandbox.flashexpress.com
Accept: application/json
```

`POST /open/v1/orders/{pno}/deliveredInfo`

getParcelDeliveredInfo

### Path Variables (not involved in the signature)

|Name|Type|Required|Description|
|---|---|---|---|
|pno|string(20)|true|Flash Express tracking number|

> request body example

```css
mchId=AA0005&nonceStr=1539057892711&sign=9B9519983FEC2B2C3BBD15FF1F858ACEA681E4800B82863EEB91163E87E7B2D9
```

> parameter description

```yaml
mchId: 'AA0005'
nonceStr: '1539057892711'
sign: '9B9519983FEC2B2C3BBD15FF1F858ACEA681E4800B82863EEB91163E87E7B2D9'
```

### Request Parameters
|Name|Type|Required|Description|
|---|---|---|---|
|mchId|string(32)|true|merchant no|
|nonceStr|string(32)|true|random nonce string|
|sign|string(64)|true|signature|

> response body example

```json
{
	"code": 1,
	"message": "success",
	"data": {
		"pno": "TH01011C27",
		"signer": "TH01011C27",
		"signerTypeText": "เพื่อนร่วมงาน",
		"pod": "https://fle-training-asset-internal.oss-ap-southeast-1.aliyuncs.com/deliveryConfirm/1523356999-b384e30df5ed4436a5adf00d80902929.jpg"
	}
}
```

### Response Body

|Name|Type|Description|
|---|---|---|
|pno|string(20)|Flash Express tracking number|
|signer|string(50)|signer's signature when parcel is delivered|
|signerTypeText|string(50)|signer type description|
|pod|string(150)|signer's signature picture url|

## Print label `/orders/{pno}/pre_print`

```http
POST /open/v1/orders/TH01011C27/pre_print HTTP/1.1
Host: api-sandbox.flashexpress.com
Accept: application/pdf
```

`POST /open/v1/orders/{pno}/pre_print`

print label

### Path Variables (not involved in the signature)

|Name|Type|Required|Description|
|---|---|---|---|
|pno|string(20)|true|Flash Express tracking number|

> request body example

```css
mchId=AA0005&nonceStr=1525316377546&sign=D500B8379DDB2CEDDBAF0130D1FF2A995FD3AFEFDE1EC90BBD2A1762F562FAE3
```

> parameter description

```yaml
mchId: 'AA0005'
nonceStr: '1525316377546'
sign: 'D500B8379DDB2CEDDBAF0130D1FF2A995FD3AFEFDE1EC90BBD2A1762F562FAE3'
```

### Request Parameters
|Name|Type|Required|Description|
|---|---|---|---|
|mchId|string(32)|true|merchant no|
|nonceStr|string(32)|true|random nonce string|
|sign|string(64)|true|signature|

> response body: pdf file stream of label

### Response Body

pdf file stream of label

## Create order by merchant tracking number `/ordersByMchPno`

```http
POST /open/v1/ordersByMchPno HTTP/1.1
Host: api-sandbox.flashexpress.com
Content-Type: application/x-www-form-urlencoded
Accept: application/json
```

> request body example

```css
mchId=AA0005&nonceStr=1538020484532&sign=840BDFAC52E4607AEC4AB7CD7A95ABE43CFD974D8DE0726DD6C3891EBB1FF6D8&mchPno=MP123456111&warehouseNo=AA0005_001&srcName=test+srcName1111&srcPhone=111111111&srcProvinceName=%E0%B8%AD%E0%B8%B8%E0%B8%9A%E0%B8%A5%E0%B8%A3%E0%B8%B2%E0%B8%8A%E0%B8%98%E0%B8%B2%E0%B8%99%E0%B8%B5&srcCityName=%E0%B9%80%E0%B8%A1%E0%B8%B7%E0%B8%AD%E0%B8%87%E0%B8%AD%E0%B8%B8%E0%B8%9A%E0%B8%A5%E0%B8%A3%E0%B8%B2%E0%B8%8A%E0%B8%98%E0%B8%B2%E0%B8%99%E0%B8%B5&srcPostalCode=34000&srcDetailAddress=68%2F5-6+%E0%B8%A1.1+%E0%B8%9A%E0%B9%89%E0%B8%B2%E0%B8%99%E0%B8%97%E0%B9%88%E0%B8%B2%E0%B8%9A%E0%B9%88%E0%B8%AD99111&dstName=%E0%B8%99%E0%B9%89%E0%B8%B3%E0%B8%9E%E0%B8%A3%E0%B8%B4%E0%B8%81%E0%B9%81%E0%B8%A1%E0%B9%88%E0%B8%AD%E0%B8%B3%E0%B8%9E%E0%B8%A3&dstPhone=091111111&dstHomePhone=092222222&dstProvinceName=%E0%B9%80%E0%B8%8A%E0%B8%B5%E0%B8%A2%E0%B8%87%E0%B9%83%E0%B8%AB%E0%B8%A1%E0%B9%88&dstCityName=%E0%B8%AA%E0%B8%B1%E0%B8%99%E0%B8%97%E0%B8%A3%E0%B8%B2%E0%B8%A2&dstPostalCode=50210&dstDetailAddress=127+%E0%B8%AB%E0%B8%A1%E0%B8%B9%E0%B9%88+3+%E0%B8%95.%E0%B8%AB%E0%B8%99%E0%B8%AD%E0%B8%87%E0%B9%81%E0%B8%AB%E0%B8%A2%E0%B9%88%E0%B8%87+%E0%B8%AD.%E0%B8%AA%E0%B8%B1%E0%B8%99%E0%B8%97%E0%B8%A3%E0%B8%B2%E0%B8%A2+%E0%B8%88.%E0%B9%80%E0%B8%8A%E0%B8%B5%E0%B8%A2%E0%B8%87%E0%B9%83%E0%B8%AB%E0%B8%A1%E0%B9%88&articleCategory=1&expressCategory=1&weight=1000&codEnabled=1&codAmount=2000&remark=-
```

> parameter description

```yaml
mchId: 'AA0005'
nonceStr: '1538020484532'
sign: '840BDFAC52E4607AEC4AB7CD7A95ABE43CFD974D8DE0726DD6C3891EBB1FF6D8'
mchPno: 'MP123456111'
warehouseNo: 'AA0005_001'
srcName: 'test srcName1111'
srcPhone: '111111111'
srcProvinceName: 'อุบลราชธานี'
srcCityName: 'เมืองอุบลราชธานี'
srcPostalCode: '34000'
srcDetailAddress: '68/5-6 ม.1 บ้านท่าบ่อ99111'
dstName: 'น้ำพริกแม่อำพร'
dstPhone: '091111111'
dstHomePhone: '092222222'
dstProvinceName: 'เชียงใหม่'
dstCityName: 'สันทราย'
dstPostalCode: '50210'
dstDetailAddress: '127 หมู่ 3 ต.หนองแหย่ง อ.สันทราย จ.เชียงใหม่'
articleCategory: 1
expressCategory: 1
weight: 1000
codEnabled: 1
codAmount: 2000
remark: '-'
```

`POST /open/v1/ordersByMchPno`

Create order by merchant tracking number

### Request Parameters

<aside class="notice">
About shipper's information address.</br>
You could specify shipper's information of the parcel in 2 ways.</br>
1st,If you input values of all request parameters of {srcName,srcPhone,srcProvinceName,srcCityName,srcPostalCode,srcDetailAddress}, notice that these several parameters must be all input values,  these parameters are the shipper's information.</br>
2nd,If you don't input value of any request parameter of {srcName,srcPhone,srcProvinceName,srcCityName,srcPostalCode,srcDetailAddress}, you must input value of request parameter "warehouseNo" to show the shipper's information.
</aside>

|Name|Type|Required|Description|
|---|---|---|---|
|mchId|string(32)|true|merchant no|
|nonceStr|string(32)|true|random nonce string|
|sign|string(64)|true|signature|
|mchPno|string(20)|true|merchant tracking number.Must be unique.|
|expressCategory|integer|true|[Express Category](#express_category)(must be 1)|
|warehouseNo|string(32)|false|shipper warehouse no.must be one value returned by API [getAllWarehouses](#getallwarehouses-warehouses).|
|srcName|string(50)|false|shipper's name|
|srcPhone|string(20)|false|shipper's phone number|
|srcProvinceName|string(150)|false|shipper's province name|
|srcCityName|string(150)|false|shipper's city name.city is a sub unit of province.|
|srcDistrictName|string(150)|false|shipper's district name.district is a sub unit of city.|
|srcPostalCode|string(20)|false|shipper's postal code|
|srcDetailAddress|string(200)|false|shipper's detail address|
|dstName|string(50)|true|consignee's name|
|dstPhone|string(20)|true|consignee's phone number|
|dstHomePhone|string(20)|false|consignee's home phone number or consigne's second phone number|
|dstProvinceName|string(150)|true|consignee's province name|
|dstCityName|string(150)|true|consignee's city name.city is a sub unit of province.|
|dstDistrictName|string(150)|false|consignee's district name.district is a sub unit of city.|
|dstPostalCode|string(20)|true|consignee's postal code|
|dstDetailAddress|string(200)|true|consignee's detail address|
|articleCategory|integer|true|[Article Category](#article_category)|
|weight|integer|true|article weight(unit: gram)|
|insured|integer|true|whether insured(1: yes 0: no)|
|insureDeclareValue|integer|false|article declare value for insurance(unit: cent)|
|codEnabled|integer|true|whether COD(1: yes 0: no)|
|codAmount|integer|false|COD amount(unit: cent)|
|remark|string(200)|false|remark|

> response body example

```json
{
	"code": 1,
	"message": "success",
	"data": {
		"mchPno": "MP123456111",
		"mchId": "AA0005",
		"sortCode": "01N-CNXY-00",
		"dstStoreName": "เชียงใหม่-DC"
	}
}
```

### Response Body

|Name|Type|Description|
|---|---|---|
|mchPno|string(20)|merchant tracking number|
|mchId|string(32)|merchant no|
|sortCode|string(13)|Flash Express sorting code|
|dstStoreName|string(50)|Flash Store Name to deliver the parcel|

## Create consumer order `/orders/consumer`

```http
POST /open/v1/orders/consumer HTTP/1.1
Host: api-sandbox.flashexpress.com
Content-Type: application/x-www-form-urlencoded
Accept: application/json
```

> request body example

```css
mchId=UBP18100020&nonceStr=1530763467835&sign=8D08ACD66D6C3837BB03880A48D639312F76BB70C49DF0283C431AAB9828529B&customerPhone=0630101499&outTradeNo=1530763467835&srcName=%E0%B8%AB%E0%B8%AD%E0%B8%A1%E0%B8%A3%E0%B8%A7%E0%B8%A1&srcPhone=0630101454&srcProvinceName=%E0%B8%81%E0%B8%A3%E0%B8%B8%E0%B8%87%E0%B9%80%E0%B8%97%E0%B8%9E&srcCityName=%E0%B8%AB%E0%B8%99%E0%B8%AD%E0%B8%87%E0%B8%88%E0%B8%AD%E0%B8%81&srcDistrictName=%E0%B8%A5%E0%B8%B3%E0%B8%9C%E0%B8%B1%E0%B8%81%E0%B8%8A%E0%B8%B5&srcPostalCode=10530&srcDetailAddress=68%2F5-6+%E0%B8%A1.1+%E0%B8%9A%E0%B9%89%E0%B8%B2%E0%B8%99%E0%B8%97%E0%B9%88%E0%B8%B2%E0%B8%9A%E0%B9%88%E0%B8%AD&srcLat=13.80063275&srcLng=100.84433917&dstName=%E0%B8%99%E0%B9%89%E0%B8%B3%E0%B8%9E%E0%B8%A3%E0%B8%B4%E0%B8%81%E0%B9%81%E0%B8%A1%E0%B9%88%E0%B8%AD%E0%B8%B3%E0%B8%9E%E0%B8%A3&dstPhone=0970209976&dstHomePhone=0970220220&dstProvinceName=%E0%B9%80%E0%B8%8A%E0%B8%B5%E0%B8%A2%E0%B8%87%E0%B9%83%E0%B8%AB%E0%B8%A1%E0%B9%88&dstCityName=%E0%B8%AA%E0%B8%B1%E0%B8%99%E0%B8%97%E0%B8%A3%E0%B8%B2%E0%B8%A2&dstPostalCode=50210&dstDetailAddress=127+%E0%B8%AB%E0%B8%A1%E0%B8%B9%E0%B9%88+3+%E0%B8%95.%E0%B8%AB%E0%B8%99%E0%B8%AD%E0%B8%87%E0%B9%81%E0%B8%AB%E0%B8%A2%E0%B9%88%E0%B8%87+%E0%B8%AD.%E0%B8%AA%E0%B8%B1%E0%B8%99%E0%B8%97%E0%B8%A3%E0%B8%B2%E0%B8%A2+%E0%B8%88.%E0%B9%80%E0%B8%8A%E0%B8%B5%E0%B8%A2%E0%B8%87%E0%B9%83%E0%B8%AB%E0%B8%A1%E0%B9%88&articleCategory=1&expressCategory=1&weight=1000&insured=1&insureDeclareValue=10000&remark=%E0%B8%82%E0%B8%B6%E0%B9%89%E0%B8%99%E0%B8%9A%E0%B8%B1%E0%B8%99%E0%B9%84%E0%B8%94
```

> parameter description

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

create consumer order

### Request Parameters

|Name|Type|Required|Description|
|---|---|---|---|
|mchId|string(32)|true|merchant no|
|nonceStr|string(32)|true|random nonce string|
|sign|string(64)|true|signature|
|customerPhone|string(20)|true|consumer's phone number|
|outTradeNo|string(64)|true|merchant order no|
|expressCategory|integer|true|[Express Category](#express_category)(must be 1)|
|srcName|string(50)|true|shipper's name|
|srcPhone|string(20)|true|shipper's phone number|
|srcProvinceName|string(150)|true|shipper's province name|
|srcCityName|string(150)|true|shipper's city name.city is a sub unit of province.|
|srcDistrictName|string(150)|false|shipper's district name.district is a sub unit of city.|
|srcPostalCode|string(20)|true|shipper's postal code|
|srcDetailAddress|string(200)|true|shipper's detail address|
|srcLat|decimal(11,8)|false|shipper's location latitude|
|srcLng|decimal(11,8)|false|shipper's location longitude|
|dstName|string(50)|true|consignee's name|
|dstPhone|string(20)|true|consignee's phone number|
|dstHomePhone|string(20)|false|consignee's home phone number or consigne's second phone number|
|dstProvinceName|string(150)|true|consignee's province name|
|dstCityName|string(150)|true|consignee's city name.city is a sub unit of province.|
|dstPostalCode|string(20)|true|consignee's postal code|
|dstDetailAddress|string(200)|true|consignee's detail address|
|articleCategory|integer|true|[Article Category](#article_category)|
|weight|integer|true|article weight(unit: gram)|
|insured|integer|true|whether insured(1: yes 0: no)|
|insureDeclareValue|integer|false|article declare value for insurance(unit: cent)|
|remark|string(200)|false|remark|

> response body example

```json
{
	"code": 1,
	"message": "success",
	"data": {
		"orderId": "5b3d98f7a4c2130579510116",
		"customerPhone": "0630101499",
		"outTradeNo": "1530763467835",
		"sortCode": "01N-CNXY-00",
		"dstStoreName": "เชียงใหม่-DC"
	}
}
```

### Response Body

|Name|Type|Description|
|---|---|---|
|orderId|string(32)|Flash Express order id|
|customerPhone|string(20)|consumer's phone number|
|outTradeNo|string(64)|merchant order no|
|sortCode|string(13)|Flash Express sorting code|
|dstStoreName|string(50)|Flash Store Name to deliver the parcel|

# Notify API
## getAllNotificationsInDate `/notifications`

```http
POST /open/v1/notifications HTTP/1.1
Host: api-sandbox.flashexpress.com
Content-Type: application/x-www-form-urlencoded
Accept: application/json
```

> request body example

```css
mchId=AA0005&nonceStr=1538021824975&sign=3C16B6E0D0DBFC6A1E46EF4D0FCEEB6C54E95960223D08FD5D583C7B7BB04A0C&date=2018-09-27
```

> parameter description

```yaml
mchId: 'AA0005'
nonceStr: '1538021824975'
sign: '3C16B6E0D0DBFC6A1E46EF4D0FCEEB6C54E95960223D08FD5D583C7B7BB04A0C'
date: '2018-09-27'
```

`POST /open/v1/notifications`

getAllNotificationsInDate

### Request Parameters

|Name|Type|Required|Description|
|---|---|---|---|
|mchId|string(32)|true|merchant no|
|nonceStr|string(32)|true|random nonce string|
|sign|string(64)|true|signature|
|date|string(10)|true|notifying date.format:YYYY-MM-DD|

> response body example

```json
{
	"code": 1,
	"message": "success",
	"data": [{
		"ticketPickupId": 7392,
		"createdAt": 1538047000,
		"srcProvinceName": "อุบลราชธานี",
		"srcCityName": "เมืองอุบลราชธานี",
		"srcDistrictName": null,
		"srcDetailAddress": "68/5-6 ม.1 บ้านท่าบ่อ",
		"staffInfoName": "LIU005",
		"staffInfomobile": "1234567896",
		"state": 1,
		"stateText": "รอรับพัสดุ",
		"kaWarehouseNo": null,
		"kaWarehouseName": null,
		"remark": "ASAP",
		"estimateParcelNumber": 1,
		"kaName": "aaa test zhao",
		"postalCode": null,
		"phone": null,
		"srcName": null,
		"parcelNumber": 0
	}, {
		"ticketPickupId": 7391,
		"createdAt": 1538046970,
		"srcProvinceName": "อุบลราชธานี",
		"srcCityName": "เมืองอุบลราชธานี",
		"srcDistrictName": null,
		"srcDetailAddress": "68/5-6 ม.1 บ้านท่าบ่อ",
		"staffInfoName": "LIU005",
		"staffInfomobile": "1234567896",
		"state": 1,
		"stateText": "รอรับพัสดุ",
		"kaWarehouseNo": null,
		"kaWarehouseName": null,
		"remark": "ASAP",
		"estimateParcelNumber": 1,
		"kaName": "aaa test zhao",
		"postalCode": null,
		"phone": null,
		"srcName": null,
		"parcelNumber": 0
	}]
}
```

### Response Body

|Name|Type|Description|
|---|---|---|
|ticketPickupId|integer|Flash Express notification id|
|kaName|string(50)|merchant name|
|srcName|string(50)|contact name of picking up address|
|phone|string(20)|contact phone number of picking up address|
|srcProvinceName|string(150)|province name of picking up address|
|srcCityName|string(150)|city name of picking up address.city is a sub unit of province.|
|srcDistrictName|string(150)|district name of picking up address.district is a sub unit of city.|
|srcDetailAddress|string(200)|detail address of picking up address|
|postalCode|string(20)|postal code of picking up address|
|kaWarehouseNo|string(32)|warehouse no. of picking up address.|
|kaWarehouseName|string(32)|warehouse name of picking up address.|
|staffInfoName|string(50)|courier name to picking up parcels|
|staffInfomobile|string(20)|courier phone number to picking up parcels|
|state|integer|[Notification State](#notification_state)|
|stateText|string(100)|state description of notification|
|estimateParcelNumber|integer|estimating parcels count to pick up|
|parcelNumber|integer|actually picked up parcels count|
|remark|string(200)|remark|
|createdAt|integer|notifying timestamp in UTC|

## Notify courier `/notify`

```http
POST /open/v1/notify HTTP/1.1
Host: api-sandbox.flashexpress.com
Content-Type: application/x-www-form-urlencoded
Accept: application/json
```

> request body example

```css
mchId=AA0005&nonceStr=1536749937115&sign=3E750D747981D6175EBB4E1F581DCD9246E60E7E82F8126C8091B27664FCD625&warehouseNo=AA0005_001&srcName=%E0%B8%AB%E0%B8%AD%E0%B8%A1%E0%B8%A3%E0%B8%A7%E0%B8%A1++nofity+test+name&srcPhone=0630101454&srcProvinceName=%E0%B8%AD%E0%B8%B8%E0%B8%9A%E0%B8%A5%E0%B8%A3%E0%B8%B2%E0%B8%8A%E0%B8%98%E0%B8%B2%E0%B8%99%E0%B8%B5&srcCityName=%E0%B9%80%E0%B8%A1%E0%B8%B7%E0%B8%AD%E0%B8%87%E0%B8%AD%E0%B8%B8%E0%B8%9A%E0%B8%A5%E0%B8%A3%E0%B8%B2%E0%B8%8A%E0%B8%98%E0%B8%B2%E0%B8%99%E0%B8%B5&srcDistrictName=%E0%B9%83%E0%B8%99%E0%B9%80%E0%B8%A1%E0%B8%B7%E0%B8%AD%E0%B8%87&srcPostalCode=34000&srcDetailAddress=68%2F5-6+%E0%B8%A1.1+%E0%B8%9A%E0%B9%89%E0%B8%B2%E0%B8%99%E0%B8%97%E0%B9%88%E0%B8%B2%E0%B8%9A%E0%B9%88%E0%B8%AD+nofity+test+address&estimateParcelNumber=100&remark=ASAP
```

> parameter description

```yaml
mchId: 'AA0005'
nonceStr: '1536749937115'
sign: '3E750D747981D6175EBB4E1F581DCD9246E60E7E82F8126C8091B27664FCD625'
warehouseNo: 'AA0005_001'
srcName: 'หอมรวม  nofity test name'
srcPhone: '0630101454'
srcProvinceName: 'อุบลราชธานี'
srcCityName: 'เมืองอุบลราชธานี'
srcDistrictName: 'ในเมือง'
srcPostalCode: '34000'
srcDetailAddress: '68/5-6 ม.1 บ้านท่าบ่อ nofity test address'
estimateParcelNumber: 100
remark: 'ASAP'
```

`POST /open/v1/notify`

Notify courier

### Request Parameters

<aside class="notice">
About picking up address.</br>
You could specify the address you want the courier to pick up your parcels.</br>
If you input values of all request parameters of {srcName,srcPhone,srcProvinceName,srcCityName,srcPostalCode,srcDetailAddress}, notice that these several parameters must be all input values,  this address will be pickup address.</br>
If you don't input values of all request parameters of {srcName,srcPhone,srcProvinceName,srcCityName,srcPostalCode,srcDetailAddress}, you must input value of request parameter "warehouseNo" as pickup address.
</aside>

|Name|Type|Required|Description|
|---|---|---|---|
|mchId|string(32)|true|merchant no|
|nonceStr|string(32)|true|random nonce string|
|sign|string(64)|true|signature|
|warehouseNo|string(32)|false|warehouse no. of picking up address.must be one value returned by API [getAllWarehouses](#getallwarehouses-warehouses).|
|srcName|string(50)|false|contact name of picking up address|
|srcPhone|string(20)|false|contact phone number of picking up address|
|srcProvinceName|string(150)|false|province name of picking up address|
|srcCityName|string(150)|false|city name of picking up address.city is a sub unit of province.|
|srcDistrictName|string(150)|false|district name of picking up address.district is a sub unit of city.|
|srcPostalCode|string(20)|false|postal code of picking up address|
|srcDetailAddress|string(200)|false|detail address of picking up address|
|estimateParcelNumber|integer|true|estimating parcels count to pick up|
|remark|string(200)|false|remark|

> response body: none

### Response Body

None.

## Cancel notification `/notify/{id}/cancel`

```http
POST /open/v1/orders/TH4714A27/cancel HTTP/1.1
Host: api-sandbox.flashexpress.com
Content-Type: application/x-www-form-urlencoded
Accept: application/json
```
`POST /open/v1/notify/{id}/cancel`

cancel notification

### Path Variables (not involved in the signature)

|Name|Type|Required|Description|
|---|---|---|---|
|id|integer|true|Flash Express notification id.must be one value returned by API [getAllNotificationsInDate](#getallnotificationsindate-notifications).|

> request body example

```css
mchId=AA0005&nonceStr=1538032588802&sign=4A16C1AD40BCEF7201149F5F125F7AA00D29D78561002DA30D9572AA79CD6F74
```

> parameter description

```yaml
mchId: 'AA0005'
nonceStr: '1538032588802'
sign: '4A16C1AD40BCEF7201149F5F125F7AA00D29D78561002DA30D9572AA79CD6F74'
```

### Request Parameters

|Name|Type|Required|Description|
|---|---|---|---|
|mchId|string(32)|true|merchant no|
|nonceStr|string(32)|true|random nonce string|
|sign|string(64)|true|signature|

> response body: none

### Response Body

None.

# Constants
<h2 id="response_code">Response Code</h2>
|Code|Meaning|
|---|---|
|1|success|
|0|internal server error|
|1000|Failed to submit|
|1001|customer not found|
|1002|invalid signature|
|1003|order already exists|
|1004|shipper's address not match|
|1005|destination not match|
|1006|order not found|
|1007|warehouse not found|
|1008|COD service not subscribed|
|1009|COD can not be < 0|
|1010|current address has been notified|
|1015|order has been picked up,can not be canceled|
|1018|shipper's address and warehouseNo could not both empty|
|1019|pno and mchId not match|
|1020|Insured declare value can not be < 0|
|1021|shipper's postal code only be a 5 digit number|
|1022|shipper's address not open|

<h2 id="express_category">Express Category</h2>
|Code|Meaning|
|---|---|
|1|Standard delivery|

<h2 id="article_category">Article Category</h2>
|Code|Meaning|
|---|---|
|0|File|
|1|Food|
|2|Daily Necessities|
|3|Electronics|
|4|Clothing|
|5|Others|

<h2 id="settlement_category">Settlement Category</h2>
|Code|Meaning|
|---|---|
|1|Cash settlement|
|2|Credit settlement|

<h2 id="notification_state">Notification State</h2>
|Code|Meaning|
|---|---|
|0|Open|
|1|Assigned|
|2|Completed|
|4|Canceled|

# Demo Download
<h2 id="demo-csharp">C# Demo</h2>
<a href="download/CSharpDemo.rar">C# Demo</a>

<h2 id="demo-php">PHP Demo</h2>
<a href="download/flashDemo.php.zip">PHP Demo</a>

<h2 id="demo-java">Java Demo</h2>
<a href="download/OpenTest.java.zip">Java Demo</a>


