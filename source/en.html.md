---
title: Fulfillment Open API Reference

language_tabs:
  - http: HTTP

toc_footers:
  - <a href="/index.html">中文</a>
  - <a href="/th.html">ภาษาไทย</a>

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
Training | https://open-training.flashfulfillment.co.th/
Production | https://open.flashfulfillment.co.th/

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
Host: api-sandbox.fulfillment.com
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

# open_api
## 仓库库存查询API
<a id=仓库库存查询API> </a>
### 基本信息

**Path：** /open/goodsStock

**Method：** POST

**接口描述：**


### 请求参数
**Headers**

| 参数名称  | 参数值  |  是否必须 | 示例  | 备注  |
| ------------ | ------------ | ------------ | ------------ | ------------ |
| Content-Type  |  application/x-www-form-urlencoded | 是  |   |   |
**Body**

| 参数名称  | 参数类型  |  是否必须 | 示例  | 备注  |
| ------------ | ------------ | ------------ | ------------ | ------------ |
| barCode | text  |  是 |  O3O2O1O,BBBUI,NNN  |  条形码 多个用,隔开，验证正则  ^[\w-]+(,[\w-]+)* |
| warehouseId | text  |  否 |  1  |  仓库ID，不填默认全部仓库 |
| goodsStatus | text  |  否 |  normal  |  质量状态[normal]正品，默认[bad]残品 |



### 返回数据

<table>
  <thead class="ant-table-thead">
    <tr>
      <th key=name>名称</th><th key=type>类型</th><th key=required>是否必须</th><th key=default>默认值</th><th key=desc>备注</th><th key=sub>其他信息</th>
    </tr>
  </thead><tbody className="ant-table-tbody"><tr key=0-0><td key=0><span style="padding-left: 0px"><span style="color: #8c8a8a"></span> msg</span></td><td key=1><span>string</span></td><td key=2>非必须</td><td key=3></td><td key=4><span></span></td><td key=5></td></tr><tr key=0-1><td key=0><span style="padding-left: 0px"><span style="color: #8c8a8a"></span> code</span></td><td key=1><span>number</span></td><td key=2>非必须</td><td key=3></td><td key=4><span></span></td><td key=5></td></tr><tr key=0-2><td key=0><span style="padding-left: 0px"><span style="color: #8c8a8a"></span> data</span></td><td key=1><span>object</span></td><td key=2>非必须</td><td key=3></td><td key=4><span></span></td><td key=5></td></tr><tr key=0-2-0><td key=0><span style="padding-left: 20px"><span style="color: #8c8a8a">├─</span> O3O2O1O</span></td><td key=1><span>object</span></td><td key=2>非必须</td><td key=3></td><td key=4><span></span></td><td key=5></td></tr><tr key=0-2-0-0><td key=0><span style="padding-left: 40px"><span style="color: #8c8a8a">├─</span> availableInventory</span></td><td key=1><span>integer</span></td><td key=2>非必须</td><td key=3></td><td key=4><span>可售库存</span></td><td key=5></td></tr><tr key=0-2-0-1><td key=0><span style="padding-left: 40px"><span style="color: #8c8a8a">├─</span> totalInventory</span></td><td key=1><span>integer</span></td><td key=2>非必须</td><td key=3></td><td key=4><span>实物库存</span></td><td key=5></td></tr><tr key=0-2-0-2><td key=0><span style="padding-left: 40px"><span style="color: #8c8a8a">├─</span> inventory</span></td><td key=1><span>integer</span></td><td key=2>非必须</td><td key=3></td><td key=4><span>可用库存</span></td><td key=5></td></tr><tr key=0-2-1><td key=0><span style="padding-left: 20px"><span style="color: #8c8a8a">├─</span> BBBUI</span></td><td key=1><span>object</span></td><td key=2>非必须</td><td key=3></td><td key=4><span></span></td><td key=5></td></tr><tr key=0-2-1-0><td key=0><span style="padding-left: 40px"><span style="color: #8c8a8a">├─</span> availableInventory</span></td><td key=1><span>string</span></td><td key=2>非必须</td><td key=3></td><td key=4><span></span></td><td key=5></td></tr><tr key=0-2-1-1><td key=0><span style="padding-left: 40px"><span style="color: #8c8a8a">├─</span> totalInventory</span></td><td key=1><span>string</span></td><td key=2>非必须</td><td key=3></td><td key=4><span></span></td><td key=5></td></tr><tr key=0-2-1-2><td key=0><span style="padding-left: 40px"><span style="color: #8c8a8a">├─</span> inventory</span></td><td key=1><span>string</span></td><td key=2>非必须</td><td key=3></td><td key=4><span></span></td><td key=5></td></tr><tr key=0-2-2><td key=0><span style="padding-left: 20px"><span style="color: #8c8a8a">├─</span> NNN</span></td><td key=1><span>object</span></td><td key=2>非必须</td><td key=3></td><td key=4><span></span></td><td key=5></td></tr><tr key=0-2-2-0><td key=0><span style="padding-left: 40px"><span style="color: #8c8a8a">├─</span> availableInventory</span></td><td key=1><span>string</span></td><td key=2>非必须</td><td key=3></td><td key=4><span></span></td><td key=5></td></tr><tr key=0-2-2-1><td key=0><span style="padding-left: 40px"><span style="color: #8c8a8a">├─</span> totalInventory</span></td><td key=1><span>string</span></td><td key=2>非必须</td><td key=3></td><td key=4><span></span></td><td key=5></td></tr><tr key=0-2-2-2><td key=0><span style="padding-left: 40px"><span style="color: #8c8a8a">├─</span> inventory</span></td><td key=1><span>string</span></td><td key=2>非必须</td><td key=3></td><td key=4><span></span></td><td key=5></td></tr>
               </tbody>
              </table>
            
## 入库通知单API
<a id=入库通知单API> </a>
### 基本信息

**Path：** /arrival_notice/create

**Method：** POST

**接口描述：**


### 请求参数
**Headers**

| 参数名称  | 参数值  |  是否必须 | 示例  | 备注  |
| ------------ | ------------ | ------------ | ------------ | ------------ |
| Content-Type  |  application/x-www-form-urlencoded | 是  |   |   |
**Body**

| 参数名称  | 参数类型  |  是否必须 | 示例  | 备注  |
| ------------ | ------------ | ------------ | ------------ | ------------ |
| warehouseId | text  |  是 |  1  |  仓库ID(目前只有1个仓库填1) |
| type | text  |  否 |  1  |  入库类型(目前只能填1) |
| orderSn | text  |  否 |    |  外部单号(不能重复) |
| channelSource | text  |  否 |    |  渠道来源 |
| deliveryWay | text  |  否 |  logistics  |  配送方式：[logistics]物流(默认) [express]快递 [seller]自送 |
| carrier | text  |  否 |    |  承运商 |
| deliveryMan | text  |  否 |    |  送货人 |
| plateNumber | text  |  否 |    |  车牌号 |
| deliveryNumber | text  |  否 |    |  运单号 |
| deliveryContact | text  |  否 |    |  送货人联系方式 |
| remark | text  |  否 |    |  备注 |
| arrivalStart | text  |  否 |  2019-06-10 14:45  |  预计到货开始时间(无需到秒) |
| arrivalEnd | text  |  否 |  2019-06-10 16:45  |  预计到货结束时间(无需到秒) |
| goods | text  |  是 |  [{"i":1,"barCode":"00000000001","num":4,"price":123,"asset":["aaaaaaa1","aaaaaaa2","aaaaaaa3","aaaaaaa4"]},{"i":2,"barCode":"00000000002","num":2,"price":456,"asset":["bbbbbbb1","bbbbbbb2"]}]  |  入库商品的json串，下面参数是json串里的 |
| i | text  |  是 |    |  顺序码，从1开始递增 |
| barCode | text  |  是 |    |  商品条码 |
| num | text  |  是 |    |  入库数量 |
| asset | text  |  否 |  ["bbbbbbb1","bbbbbbb2"]  |  ASSET码数组，当商品开启ASSET时必填 |



## 出库单添加API
<a id=出库单添加API> </a>
### 基本信息

**Path：** /open/returnWarehouseAdd

**Method：** POST

**接口描述：**
<p>商品格式<br>
[{"i":"0","barCode":"FEX00025","goodsName":"测试接口1","specification":"14*20*6 cm","num":"1","price":"400","remark":"商品备注一"}，{"i":"1","barCode":"FEX00026","goodsName":"测试接口2","specification":"14*20*7 cm","num":"2","price":"500","remark":"商品备注二"}]</p>


### 请求参数
**Headers**

| 参数名称  | 参数值  |  是否必须 | 示例  | 备注  |
| ------------ | ------------ | ------------ | ------------ | ------------ |
| Content-Type  |  application/x-www-form-urlencoded | 是  |   |   |
**Body**

| 参数名称  | 参数类型  |  是否必须 | 示例  | 备注  |
| ------------ | ------------ | ------------ | ------------ | ------------ |
| status | text  |  否 |    |  [1]待审核 |
| warehouseId | text  |  是 |  1  |  仓库ID |
| type | text  |  否 |  1  |  退供出库 |
| channelSource | text  |  否 |    |  来源渠道 |
| nodeSn | text  |  否 |  TH01090201  |  网点编码 |
| consigneeName | text  |  是 |    |  收货人 |
| consigneePhone | text  |  是 |    |  联系方式 |
| province | text  |  是 |    |  省 |
| city | text  |  是 |    |  市 |
| district | text  |  是 |    |  区 |
| postalCode | text  |  是 |    |  邮编 |
| consigneeAddress | text  |  是 |    |  详细地址 |
| logistics | text  |  否 |    |  配送方式【物流 logistics】 【快递express】 【 到仓自取self】 |
| outTime | text  |  否 |    |  期望出库时间 |
| goodsStatus | text  |  否 |    |  【normal】正品  【bad】残品 |
| remark | text  |  否 |    |  备注 |
| goods | text  |  是 |    |  出库商品json |
| orderSn | text  |  是 |    |  外部订单号 |



## 发货单新增API
<a id=发货单新增API> </a>
### 基本信息

**Path：** /Order/addOrder

**Method：** POST

**接口描述：**
<p><span class="colour" style="color:rgb(85, 85, 85)">商品传输格式&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; [{"id":'20',"num":"1","price":"400"}，{"id":"21","num":"2","price":"500"}]</span></p>


### 请求参数
**Headers**

| 参数名称  | 参数值  |  是否必须 | 示例  | 备注  |
| ------------ | ------------ | ------------ | ------------ | ------------ |
| Content-Type  |  application/x-www-form-urlencoded | 是  |   |   |
**Body**

| 参数名称  | 参数类型  |  是否必须 | 示例  | 备注  |
| ------------ | ------------ | ------------ | ------------ | ------------ |
| platformSourceId | text  |  是 |    |  wms端添加的货主店铺主键ID |
| orderSn | text  |  是 |    |  订单号 |
| orderSourceId | text  |  是 |    |  订单主键ID |
| code | text  |  是 |    |  地址code |
| postalCode | text  |  是 |    |  邮编 |
| consigneeName | text  |  是 |    |  收件人名称 |
| consigneeAddress | text  |  是 |    |  收件人详细地址 |
| phoneNumber | text  |  是 |    |  联系电话 |
| buyerMessage | text  |  是 |    |  买家留言 |
| paidPrice | text  |  是 |    |  订单金额 |
| logisticCharge | text  |  是 |    |  运费 |
| totalPrice | text  |  是 |    |  订单总金额 |
| payMode | text  |  是 |    |  支付方式  【1】货到付款  【2】银行转账 |
| payStatus | text  |  是 |    |  支付状态 【0】未支付【1】 已支付 |
| goods | text  |  是 |      [{"id":'20',"num":"1","price":"400"}，{"id":"21","num":"2","price":"500"}]  |  订单商品信息 商品id 数量 价格 json格式 |
| type | text  |  否 |  1  |  订单类型 [1]销售订单 (默认) [2]补货订单 |
| deliverySn | text  |  否 |    |  原发货单号(type=2时必填) |



## 商品档案查询API
<a id=商品档案查询API> </a>
### 基本信息

**Path：** /open/goods

**Method：** POST

**接口描述：**
<p>1.barCode 不传的时候&nbsp; 返回货主所有的商品列表，填写时返回对应条码的商品信息。<br>
2.如果要查询多个商品则把多个条形码用&nbsp; &nbsp;，（逗号） 隔开&nbsp; 如&nbsp; &nbsp;barCode1,barCode2</p>
<p>training<span class="size"></span>环境链接：<a href="https://open-training.flashfulfillment.co.th/">https://open-training.flashfulfillment.co.th/</a><span class="size"></span><br>
training图片地址示例：<a href="https://wms-upload-training.flashfulfillment.co.th/upload/images/goods/94/76/947600748f3a290edb38aee45d3be9e5.jpg">https://wms-upload-training.flashfulfillment.co.th/upload/images/goods/94/76/947600748f3a290edb38aee45d3be9e5.jpg</a><br data-tomark-pass=""><br>
<br data-tomark-pass=""><br>
<br data-tomark-pass=""><br>
生产环境链接：<a href="https://open.flashfulfillment.co.th/">https://open.flashfulfillment.co.th/</a><br>
生产图片地址示例：<a href="https://wms-upload.flashfulfillment.co.th/upload/images/goods/77/c0/77c04870b9622555c7fd681214ca63a6.png">https://wms-upload.flashfulfillment.co.th/upload/images/goods/77/c0/77c04870b9622555c7fd681214ca63a6.png</a></p>


### 请求参数
**Headers**

| 参数名称  | 参数值  |  是否必须 | 示例  | 备注  |
| ------------ | ------------ | ------------ | ------------ | ------------ |
| Content-Type  |  application/x-www-form-urlencoded | 是  |   |   |
**Body**

| 参数名称  | 参数类型  |  是否必须 | 示例  | 备注  |
| ------------ | ------------ | ------------ | ------------ | ------------ |
| barCode | text  |  否 |    |  商品条形码 |



### 返回数据

<table>
  <thead class="ant-table-thead">
    <tr>
      <th key=name>名称</th><th key=type>类型</th><th key=required>是否必须</th><th key=default>默认值</th><th key=desc>备注</th><th key=sub>其他信息</th>
    </tr>
  </thead><tbody className="ant-table-tbody"><tr key=0-0><td key=0><span style="padding-left: 0px"><span style="color: #8c8a8a"></span> msg</span></td><td key=1><span>string</span></td><td key=2>非必须</td><td key=3></td><td key=4><span></span></td><td key=5></td></tr><tr key=0-1><td key=0><span style="padding-left: 0px"><span style="color: #8c8a8a"></span> code</span></td><td key=1><span>number</span></td><td key=2>非必须</td><td key=3></td><td key=4><span></span></td><td key=5></td></tr><tr key=0-2><td key=0><span style="padding-left: 0px"><span style="color: #8c8a8a"></span> data</span></td><td key=1><span>object</span></td><td key=2>非必须</td><td key=3></td><td key=4><span></span></td><td key=5></td></tr><tr key=0-2-0><td key=0><span style="padding-left: 20px"><span style="color: #8c8a8a">├─</span> SIHX</span></td><td key=1><span>object</span></td><td key=2>非必须</td><td key=3></td><td key=4><span>条形码</span></td><td key=5></td></tr><tr key=0-2-0-0><td key=0><span style="padding-left: 40px"><span style="color: #8c8a8a">├─</span> modifiedName</span></td><td key=1><span>string</span></td><td key=2>非必须</td><td key=3></td><td key=4><span></span></td><td key=5></td></tr><tr key=0-2-0-1><td key=0><span style="padding-left: 40px"><span style="color: #8c8a8a">├─</span> abbrName</span></td><td key=1><span>string</span></td><td key=2>非必须</td><td key=3></td><td key=4><span></span></td><td key=5></td></tr><tr key=0-2-0-2><td key=0><span style="padding-left: 40px"><span style="color: #8c8a8a">├─</span> remark</span></td><td key=1><span>string</span></td><td key=2>非必须</td><td key=3></td><td key=4><span>备注</span></td><td key=5></td></tr><tr key=0-2-0-3><td key=0><span style="padding-left: 40px"><span style="color: #8c8a8a">├─</span> isCombo</span></td><td key=1><span>string</span></td><td key=2>非必须</td><td key=3></td><td key=4><span>【1】商品套装【0】普通商品</span></td><td key=5></td></tr><tr key=0-2-0-4><td key=0><span style="padding-left: 40px"><span style="color: #8c8a8a">├─</span> hot</span></td><td key=1><span>string</span></td><td key=2>非必须</td><td key=3></td><td key=4><span>ABC分类</span></td><td key=5></td></tr><tr key=0-2-0-5><td key=0><span style="padding-left: 40px"><span style="color: #8c8a8a">├─</span> sellerId</span></td><td key=1><span>string</span></td><td key=2>非必须</td><td key=3></td><td key=4><span></span></td><td key=5></td></tr><tr key=0-2-0-6><td key=0><span style="padding-left: 40px"><span style="color: #8c8a8a">├─</span> isAsset</span></td><td key=1><span>string</span></td><td key=2>非必须</td><td key=3></td><td key=4><span>【1】Asset商品【0】普通商品</span></td><td key=5></td></tr><tr key=0-2-0-7><td key=0><span style="padding-left: 40px"><span style="color: #8c8a8a">├─</span> encodeType</span></td><td key=1><span>string</span></td><td key=2>非必须</td><td key=3></td><td key=4><span>[unique]SN商品 [其他]非SN商品</span></td><td key=5></td></tr><tr key=0-2-0-8><td key=0><span style="padding-left: 40px"><span style="color: #8c8a8a">├─</span> price</span></td><td key=1><span>string</span></td><td key=2>非必须</td><td key=3></td><td key=4><span>售价</span></td><td key=5></td></tr><tr key=0-2-0-9><td key=0><span style="padding-left: 40px"><span style="color: #8c8a8a">├─</span> isLocked</span></td><td key=1><span>string</span></td><td key=2>非必须</td><td key=3></td><td key=4><span></span></td><td key=5></td></tr><tr key=0-2-0-10><td key=0><span style="padding-left: 40px"><span style="color: #8c8a8a">├─</span> isUnpackedDelivery</span></td><td key=1><span>string</span></td><td key=2>非必须</td><td key=3></td><td key=4><span></span></td><td key=5></td></tr><tr key=0-2-0-11><td key=0><span style="padding-left: 40px"><span style="color: #8c8a8a">├─</span> modified</span></td><td key=1><span>string</span></td><td key=2>非必须</td><td key=3></td><td key=4><span></span></td><td key=5></td></tr><tr key=0-2-0-12><td key=0><span style="padding-left: 40px"><span style="color: #8c8a8a">├─</span> createdName</span></td><td key=1><span>string</span></td><td key=2>非必须</td><td key=3></td><td key=4><span></span></td><td key=5></td></tr><tr key=0-2-0-13><td key=0><span style="padding-left: 40px"><span style="color: #8c8a8a">├─</span> id</span></td><td key=1><span>string</span></td><td key=2>非必须</td><td key=3></td><td key=4><span>商品ID</span></td><td key=5></td></tr><tr key=0-2-0-14><td key=0><span style="padding-left: 40px"><span style="color: #8c8a8a">├─</span> introduction</span></td><td key=1><span>string</span></td><td key=2>非必须</td><td key=3></td><td key=4><span>商品简介</span></td><td key=5></td></tr><tr key=0-2-0-15><td key=0><span style="padding-left: 40px"><span style="color: #8c8a8a">├─</span> image</span></td><td key=1><span>string</span></td><td key=2>非必须</td><td key=3></td><td key=4><span>图片地址</span></td><td key=5></td></tr><tr key=0-2-0-16><td key=0><span style="padding-left: 40px"><span style="color: #8c8a8a">├─</span> storeType</span></td><td key=1><span>string</span></td><td key=2>非必须</td><td key=3></td><td key=4><span></span></td><td key=5></td></tr><tr key=0-2-0-17><td key=0><span style="padding-left: 40px"><span style="color: #8c8a8a">├─</span> parentTwo</span></td><td key=1><span>string</span></td><td key=2>非必须</td><td key=3></td><td key=4><span></span></td><td key=5></td></tr><tr key=0-2-0-18><td key=0><span style="padding-left: 40px"><span style="color: #8c8a8a">├─</span> logisticRequire</span></td><td key=1><span>string []</span></td><td key=2>非必须</td><td key=3></td><td key=4><span>【dangerous】】危险品【fragile】易碎品【not_flight】禁航品【damp_proof】防潮【fire_proof】防火【put_ice_bag】保鲜品【heteromorphism】异形</span></td><td key=5><p key=3><span style="font-weight: '700'">item 类型: </span><span>string</span></p></td></tr><tr key=0-2-0-19><td key=0><span style="padding-left: 40px"><span style="color: #8c8a8a">├─</span> created</span></td><td key=1><span>string</span></td><td key=2>非必须</td><td key=3></td><td key=4><span></span></td><td key=5></td></tr><tr key=0-2-0-20><td key=0><span style="padding-left: 40px"><span style="color: #8c8a8a">├─</span> costPrice</span></td><td key=1><span>string</span></td><td key=2>非必须</td><td key=3></td><td key=4><span>成本价</span></td><td key=5></td></tr><tr key=0-2-0-21><td key=0><span style="padding-left: 40px"><span style="color: #8c8a8a">├─</span> specification</span></td><td key=1><span>string</span></td><td key=2>非必须</td><td key=3></td><td key=4><span>商品规格</span></td><td key=5></td></tr><tr key=0-2-0-22><td key=0><span style="padding-left: 40px"><span style="color: #8c8a8a">├─</span> isShelfLife</span></td><td key=1><span>string</span></td><td key=2>非必须</td><td key=3></td><td key=4><span>[0]非保质期商品 [1]是保质期商品</span></td><td key=5></td></tr><tr key=0-2-0-23><td key=0><span style="padding-left: 40px"><span style="color: #8c8a8a">├─</span> barCode</span></td><td key=1><span>string</span></td><td key=2>非必须</td><td key=3></td><td key=4><span>商品条码</span></td><td key=5></td></tr><tr key=0-2-0-24><td key=0><span style="padding-left: 40px"><span style="color: #8c8a8a">├─</span> volume</span></td><td key=1><span>string</span></td><td key=2>非必须</td><td key=3></td><td key=4><span></span></td><td key=5></td></tr><tr key=0-2-0-25><td key=0><span style="padding-left: 40px"><span style="color: #8c8a8a">├─</span> orderSourceType</span></td><td key=1><span>string</span></td><td key=2>非必须</td><td key=3></td><td key=4><span></span></td><td key=5></td></tr><tr key=0-2-0-26><td key=0><span style="padding-left: 40px"><span style="color: #8c8a8a">├─</span> name</span></td><td key=1><span>string</span></td><td key=2>非必须</td><td key=3></td><td key=4><span>商品名称</span></td><td key=5></td></tr><tr key=0-2-0-27><td key=0><span style="padding-left: 40px"><span style="color: #8c8a8a">├─</span> replenishmentBatchNum</span></td><td key=1><span>string</span></td><td key=2>非必须</td><td key=3></td><td key=4><span></span></td><td key=5></td></tr><tr key=0-2-0-28><td key=0><span style="padding-left: 40px"><span style="color: #8c8a8a">├─</span> parentOne</span></td><td key=1><span>string</span></td><td key=2>非必须</td><td key=3></td><td key=4><span></span></td><td key=5></td></tr><tr key=0-2-0-29><td key=0><span style="padding-left: 40px"><span style="color: #8c8a8a">├─</span> goodsCode</span></td><td key=1><span>string</span></td><td key=2>非必须</td><td key=3></td><td key=4><span>商品货号</span></td><td key=5></td></tr><tr key=0-2-0-30><td key=0><span style="padding-left: 40px"><span style="color: #8c8a8a">├─</span> categoryId</span></td><td key=1><span>string</span></td><td key=2>非必须</td><td key=3></td><td key=4><span></span></td><td key=5></td></tr><tr key=0-2-0-31><td key=0><span style="padding-left: 40px"><span style="color: #8c8a8a">├─</span> status</span></td><td key=1><span>string</span></td><td key=2>非必须</td><td key=3></td><td key=4><span>[1]停用 [2]存盘 [3]启用</span></td><td key=5></td></tr>
               </tbody>
              </table>
            
## 地址库修改API
<a id=地址库修改API> </a>
### 基本信息

**Path：** /seller_address/update

**Method：** POST

**接口描述：**


### 请求参数
**Headers**

| 参数名称  | 参数值  |  是否必须 | 示例  | 备注  |
| ------------ | ------------ | ------------ | ------------ | ------------ |
| Content-Type  |  application/x-www-form-urlencoded | 是  |   |   |
**Body**

| 参数名称  | 参数类型  |  是否必须 | 示例  | 备注  |
| ------------ | ------------ | ------------ | ------------ | ------------ |
| nodeSn | text  |  是 |  TH01050101  |  网点编号 |
| name | text  |  是 |    |  网点名称 |
| type | text  |  否 |    |  网点类型 |
| contact | text  |  是 |    |  联系人 |
| telephone | text  |  是 |    |  电话号码 |
| code | text  |  是 |    |  地址Code |
| postalCode | text  |  是 |    |  地址邮编 |
| address | text  |  是 |    |  详细地址 |
| organization | text  |  否 |    |  分管机构 |
| remark | text  |  否 |    |  备注 |



## 地址库删除API
<a id=地址库删除API> </a>
### 基本信息

**Path：** /seller_address/delete

**Method：** POST

**接口描述：**
<p>此api由于wms业务需求&nbsp; 将改功能改成禁用功能</p>


### 请求参数
**Headers**

| 参数名称  | 参数值  |  是否必须 | 示例  | 备注  |
| ------------ | ------------ | ------------ | ------------ | ------------ |
| Content-Type  |  application/x-www-form-urlencoded | 是  |   |   |
**Body**

| 参数名称  | 参数类型  |  是否必须 | 示例  | 备注  |
| ------------ | ------------ | ------------ | ------------ | ------------ |
| nodeSn | text  |  是 |    |  网点编号 |



## 地址库新增API
<a id=地址库新增API> </a>
### 基本信息

**Path：** /seller_address/create

**Method：** POST

**接口描述：**


### 请求参数
**Headers**

| 参数名称  | 参数值  |  是否必须 | 示例  | 备注  |
| ------------ | ------------ | ------------ | ------------ | ------------ |
| Content-Type  |  application/x-www-form-urlencoded | 是  |   |   |
**Body**

| 参数名称  | 参数类型  |  是否必须 | 示例  | 备注  |
| ------------ | ------------ | ------------ | ------------ | ------------ |
| nodeSn | text  |  是 |  TH01050101  |  网点编号 |
| name | text  |  是 |    |  网点名称 |
| type | text  |  否 |    |  网点类型 |
| contact | text  |  是 |    |  联系人 |
| telephone | text  |  是 |    |  电话号码 |
| code | text  |  是 |    |  地址Code |
| postalCode | text  |  是 |    |  地址邮编 |
| address | text  |  是 |    |  详细地址 |
| organization | text  |  否 |    |  分管机构 |
| remark | text  |  否 |    |  备注 |



## 客户商品出库明细
<a id=客户商品出库明细> </a>
### 基本信息

**Path：** /open/outBoundGoods

**Method：** POST

**接口描述：**
<p>返回数据字段解释&nbsp; &nbsp;barCode&nbsp; 商品条码， outNumber&nbsp; &nbsp;出库数量&nbsp; &nbsp;outWarehouseTime&nbsp; 出库时间&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;outSn 出库单单号 （方便测试使用）</p>


### 请求参数
**Headers**

| 参数名称  | 参数值  |  是否必须 | 示例  | 备注  |
| ------------ | ------------ | ------------ | ------------ | ------------ |
| Content-Type  |  application/x-www-form-urlencoded | 是  |   |   |
**Body**

| 参数名称  | 参数类型  |  是否必须 | 示例  | 备注  |
| ------------ | ------------ | ------------ | ------------ | ------------ |
| startTime | text  |  是 |  2018-07-15  |  查询开始日期 |
| endTime | text  |  是 |  2019-07-15  |  查询结束日期   开始时间结束时间不要大于一年 |
| nodeSn | text  |  是 |  TH3052641  |  客户编码（网点编号） |
| barCode | text  |  是 |    |  商品SN  多个以 逗号隔开     barcode1,barcode2,barcode3 |



## 销退单新增API
<a id=销退单新增API> </a>
### 基本信息

**Path：** /rollback_order/add

**Method：** POST

**接口描述：**
<p><span class="colour" style="color: rgb(0, 0, 0);">{</span><br>
<span class="colour" style="color: rgb(163, 21, 21);">"code"</span><span class="colour" style="color: rgb(0, 0, 0);">:</span><span class="colour" style="color: rgb(9, 136, 90);">1</span><span class="colour" style="color: rgb(0, 0, 0);">,</span><br>
<span class="colour" style="color: rgb(163, 21, 21);">"msg"</span><span class="colour" style="color: rgb(0, 0, 0);">:</span><span class="colour" style="color: rgb(4, 81, 165);">"成功"</span><span class="colour" style="color: rgb(0, 0, 0);">,</span><br>
<span class="colour" style="color: rgb(163, 21, 21);">"data"</span><span class="colour" style="color: rgb(0, 0, 0);">:</span><span class="colour" style="color: rgb(4, 81, 165);">"115"</span><br>
<span class="colour" style="color: rgb(0, 0, 0);">}</span></p>


### 请求参数
**Headers**

| 参数名称  | 参数值  |  是否必须 | 示例  | 备注  |
| ------------ | ------------ | ------------ | ------------ | ------------ |
| Content-Type  |  application/x-www-form-urlencoded | 是  |   |   |
**Body**

| 参数名称  | 参数类型  |  是否必须 | 示例  | 备注  |
| ------------ | ------------ | ------------ | ------------ | ------------ |
| externalOrderSn | text  |  是 |  dsfsfsf  |  订单号（外部订单号） |
| backType | text  |  是 |  allRejected  |  退货类型 枚举值 allRejected primary |
| backReason | text  |  否 |  jijijijiji  |  退回原因 |
| deliverySn | text  |  否 |  DO19081217361  |  销售订单号(SCM发货单号) |
| externalUserInfo | text  |  否 |  xingfukuaidi12  |  用户ID（用户在电商平台的用户） |
| senderAddress | text  |  是 |    |   |
| senderCountry | text  |  否 |  th  |  国家  默认泰国  填了也不用 |
| province | text  |  是 |  กรุงเทพ  |  省 |
| city | text  |  是 |  คลองสามวา  |  市 |
| district | text  |  是 |  ทรายกองดิน  |  区 |
| postalCode | text  |  是 |  10510  |  邮政编码 |
| senderName | text  |  否 |  binbin  |  寄件人姓名 |
| senderPhone | text  |  否 |  34534373457474  |  寄件人电话号码   9-20位数字 |
| carrier | text  |  否 |  快递  |  承运商 |
| backExpressSn | text  |  否 |  急急急试试  |  退回运单号 |
| oriExpressSn | text  |  否 |  急急急  |  原运单号 |
| weight | text  |  否 |  56  |  订单重量 g 传整形 |
| size | text  |  否 |  4445  |  订单尺寸 mm  传整型 |
| buyerRemark | text  |  否 |  备注  |  买家备注 |
| backPayMode | text  |  是 |  cod  |  付款类型 枚举值  bank online cod  |
| backStatus | text  |  否 |  1  |  退款状态 枚举值 默认1  1：待退款 2：已退款 3：不退款 |
| bankId | text  |  否 |  电饭锅  |  退款账号 |
| payee | text  |  否 |  lolo  |  收款人 |
| bankName | text  |  否 |  建行  |  银行 |
| warehouseId | text  |  是 |  78  |  仓库ID |
| goods | text  |  是 |  [{"barCode\":4,\"num\":34,\"price\":5565},{\"barCode\":5,\"num\":3}]  |  商品明细 json字符转  转义和不转义的都支持 |



### 返回数据

<table>
  <thead class="ant-table-thead">
    <tr>
      <th key=name>名称</th><th key=type>类型</th><th key=required>是否必须</th><th key=default>默认值</th><th key=desc>备注</th><th key=sub>其他信息</th>
    </tr>
  </thead><tbody className="ant-table-tbody"><tr key=0-0><td key=0><span style="padding-left: 0px"><span style="color: #8c8a8a"></span> code</span></td><td key=1><span>number</span></td><td key=2>非必须</td><td key=3></td><td key=4><span></span></td><td key=5></td></tr><tr key=0-1><td key=0><span style="padding-left: 0px"><span style="color: #8c8a8a"></span> msg</span></td><td key=1><span>string</span></td><td key=2>非必须</td><td key=3></td><td key=4><span></span></td><td key=5></td></tr><tr key=0-2><td key=0><span style="padding-left: 0px"><span style="color: #8c8a8a"></span> data</span></td><td key=1><span>string</span></td><td key=2>非必须</td><td key=3></td><td key=4><span></span></td><td key=5></td></tr>
               </tbody>
              </table>
            


# Constants
<h2 id="response_code">Response Code</h2>
|Code|Meaning|
|---|---|
|0|internal server error|
|1|success|
|1000|Failed to submit|
|1001|customer not found|
|100112|Please log in|
|1002|invalid signature|
|1003|order already exists|
|1004|shipper's address not match|
|1005|destination not match|
|1006|order not found|
|1007|warehouse not found|
|1008|COD service not subscribed|
|1009|COD can not be < 0|
|1010|current address has been notified|
|1011|Owner not exists|
|1012|Sale platform not exists|
|1013|Logistic company not exists|
|1014|Delivery order not exists|
|1015|order has been picked up,can not be canceled|
|1016|Sales price cannot be 0|
|1017|The store does not exist|
|1018|shipper's address and warehouseNo could not both empty|
|1019|pno and mchId not match|
|1020|Insured declare value can not be < 0|
|1021|shipper's postal code only be a 5 digit number|
|1022|shipper's address not open|
|1023||
|1024||
|1025||
|1026||
|1027||
|1101| Contact number is incorrect|
|1102| Commodity information is  incorrect|
|1103|The barcode can't empty |
|1104| Commodity not exist|
|1105| Quantity must be filled|
|1106|Commodity duplicate|
|1107|Order number is not allowed to be empty|
|1108|Time format is   incorrect|
|1109|Form order  is invalid|
|1110|The product name  can't be empty|
|1201|Order date can't empty|
|1202|Order amount can't empty|
|1203|Detailed address and provincial and city postal code can't empty|
|1204|Consignee name can't empty|
|1205|Phone number  can't empty|
|1206|The payment method can't empty|
|1207|Detailed address and postal code cannot be empty|
|1208|Provincial not match|
|1300|Order date is  incorrect|
|1301|Order amount is incorrect|
|1302|District,  Province, Postal Code not match|
|1303|The payment method is incorrect|
|1304|Commodity not exist|
|1305|Commodity not enabled|
|1306|The order quantity  is incorrect|
|1307|The price is incorrect|
|1308|Freight fee is invalid|
|1309|Commodity barcode already exists|
|1310|The same barcode exists in the import file|
|1311|The barcode format is incorrect|
|1312|Commodity number format is incorrect|
|1313|Commodity shelf life parameter is incorrect|
|1314|The SN parameter is incorrect|
|1315|Cost price parameter is incorrect|
|1316|The length parameter is incorrect|
|1317|The width parameter is incorrect|
|1318|The height parameter is incorrect|
|1319|The weight parameter is incorrect|
|1320|Prepackage weight is incorrect|
|1321|The number of days for shelf life  item  are required|
|1322|The number of days for shelf life  is incorrect|
|1323|Warning days for shelf life  item  are required|
|1324|Warning days  is incorrect|
|1325|Shelf life commodity can't be SN code|
|1326|Image URL error|
|1327|Image format error|
|1328|Warning days can't  greater than Shelf life days|
|1329|Payment time is illegal|
|1330|Country cannot be empty|
|1331|City cannot be empty|
|1332|City cannot be empty|
|1333|Zip code cannot be empty|
|1334||
|1335||
|1336||
|1337||
|1338||
|1400|The same order has the same price|
|1401|The order amount is inconsistent|
|1402|The owner did not sign this warehouse, or the warehouse ID does not exist|
|1403|The quality status parameter is incorrect|
|1501|Commodity not yet on sale|
|1502|User receipt address not exist|
|1503|Parameters required|
|1504|Parameter error|
|1505|Address cannot be empty|
|1506|ID parameter is incorrect|
|1507|Quantity parameter is incorrect|
|1508|The number of purchases cannot be less min qty.|
|1509|There is no item in the shopping cart|
|1510|Inventory insufficient  or out of stock, unable to submit order|
|1511|Commodity sold out|
|1512|Order approved cannot be cancelled|
|1513|Commodity on sale|
|1514|The number of on sale cannot be 0|
|1600|Not allowed to enter the system|
|1601|Branch information  not match|
|1602|Parameter non-JSON format|
|1603|External order no.  required|
|1604|Delivery bill has been  cancelled|
|1605|Delivery bill   already handover|
|1606|Delivery bill    has been shipped|
|1607|Merge or split of delivery bill cannot be cancelled|
|1608|Commodity not exist or is not enabled|
|1609|Branch code cannot be empty|
|1610|Branch code format is incorrect|
|1611|Branch name cannot be empty|
|1612|Contact name cannot be empty|
|1613|Code and Postal code cannot be empty|
|1614|Branch information already exists|
|1615|Repository area cannot be empty|
|1616|Ware-location coding cannot be empty|
|1617|Repository area not exists|
|1618|Ware-location coding in the table is repeated|
|1619|Already exists in the system|
|1620|Location code  format is incorrect|
|1621|Location sequence format is incorrect|
|1622|Location row format is incorrect|
|1623|Location group format is incorrect|
|1624|Location  floor  format is incorrect|
|1625|Location format is incorrect|
|1626|Location maximum volume  format is incorrect|
|1627|Location maximum load bearing  format is incorrect|
|1628|Location long format is incorrect|
|1629|Location width format is incorrect|
|1630|Location high format is incorrect|
|1631|Location quality status format is incorrect|
|1632|The area cannot edit|
|1633|Location ID cannot be empty|
|1634|Location ID in the table is repeating|
|1635|Location ID not exist|
|1636|Quality status cannot be changed|
|1637|System preset location cannot be modified|
|1638|ASSET format error|
|1639|Start time should be less than end time|
|1640|Products enabled ASSET, please submit ASSET code|
|1641|ASSET already exists in the warehouse|
|1642|Duplicate ASSET code exists|
|1643|ASSET quantity with inbound bill item quantity not match.|
|1644|Inbound type parameter invalid|
|1645|Branch format incorrect|
|1646|Date range is incorrect|
|1647|The code does not exist|
|1648|Incorrect format of location type|
