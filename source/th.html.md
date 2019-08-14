---
title: Fulfillment 开放平台接口

language_tabs:
  - http: HTTP

toc_footers:
  - <a href="/index.html">中文</a>
  - <a href="/en.html">English</a>

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
Training | https://open-training.flashfulfillment.co.th/
Production | https://open.flashfulfillment.co.th/

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
Host: api-sandbox.fulfillment.com
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
            


# 常量表
<h2 id="response_code">返回码 (code)</h2>
|Code|Meaning|
|---|---|
|0|ระบบภายในไม่ถูกต้อง|
|1|สำเร็จ|
|1000|การตรวจสอบข้อมูลไม่ถูกต้อง|
|1001|ไม่พบรายชื่อลูกค้า|
|100112|โปรดเข้าสู่ระบบ|
|1002|การเซ็นชื่อไม่ผ่าน|
|1003|พบใบออเดอร์แล้ว|
|1004|ที่อยู่ผู้ส่งไม่ตรงกัน|
|1005|ที่อยู่ผู้รับไม่ตรงกัน|
|1006|ใบพบใบออเดอร์|
|1007|ไม่พบคลังสินค้า|
|1008|ไม่เปิดใช้บริการCODชั่วคราว|
|1009|COD ต้องไม่น้อยกว่า0|
|1010|ลูกค้าคนปัจจุบันมีการแจ้งรับสินค้าที่ไม่ยังไม่เสร็จสิ้น ไม่จำเป็นต้องแจ้งการจัดส่ง|
|1011|ไม่ปรากฎเจ้าของสินค้า|
|1012|ไม่พบแพลตฟอร์มการขาย|
|1013|ไม่มีบริษัทโลจิสติกส์|
|1014|ไม่พบใบจัดส่งสินค้า|
|1015|ใบออเดอร์ที่รับสินค้าแล้วไม่สามารถทำลายได้|
|1016|ราคาขายต้องไม่เป็น 0|
|1017|ไม่พบร้านค้าดัง|
|1018|ไม่สามารถเว้นว่างที่อยู่จัดส่งและคลังสินค้า ได้|
|1019|เลขพัสดุpno กับ ลูกค้า mchld ไม่ตรงกัน|
|1020|ราคารับประกันไม่สามารถน้อยกว่า0|
|1021|รหัสไปรษณีย์ของที่อยู่จัดส่งสามารถระบุได้เพียง5ตัวอักขระ|
|1022|ที่อยู่จัดส่งปิดให้บริการชั่วคราว|
|1023||
|1024||
|1025||
|1026||
|1027||
|1101|เบอร์โทรติดต่อไม่ถูกต้อง|
|1102|ข้อมูลสินค้าไม่ถูกต้อง|
|1103|ไม่สามารถเว้นว่างเลขบาร์โค้ด|
|1104|ไม่พบสินค้า|
|1105|จำเป็นต้องระบุจำนวนสินค้า|
|1106|สินค้าซ้ำกัน|
|1107|ไม่อนุญาติให้เว้นว่างหมายเลขใบออเดอร์ |
|1108|รูปแบบเวลาไม่ถูกต้อง|
|1109|รูปแบบใบออเดอร์ไม่ถูกต้อง|
|1110|ไม่สามารถเว้นว่างชื่อสินค้า|
|1201|ไม่สามารถเว้นว่างวันที่สั่งซื้อ |
|1202|ไม่สามารถเว้นว่างยอดเงินใบออเดอร์ |
|1203|ไม่สามารถเว้นว่างที่อยู่โดยละเอียด ตำบล อำเภอ จังหวัด และรหัสไปรษณีย์ |
|1204|ไม่สามารถเว้นว่างชื่อผู้รับสินค้า|
|1205|ไม่สามารเว้นว่างเบอร์โทรติดต่อ |
|1206|ไม่สามารถเว้นว่างวิธีการชำระเงิน |
|1207|รายละเอียดที่อยู่และรหัสไปรษณีย์ไม่สามารถเว้นว่าง|
|1208|เขตเมืองไม่ถูกต้อง|
|1300|วันที่สั่งซื้อไม่ถูกต้อง  |
|1301|ยอดเงินของใบออเดอร์ไม่ถูกต้อง|
|1302|ตำบล อำเภอ จังหวัด รหัสไปรษณีย์ ไม่ตรงกัน|
|1303|วิธีการชำระเงินไม่ถูกต้อง |
|1304|ไม่พบสินค้า  |
|1305|ไม่ได้เปิดใช้งานสินค้า  |
|1306|จำนวนที่สั่งซื้อไม่ถูกต้อง|
|1307|ราคาไม่ถูกต้อง|
|1308|ค่าขนส่งไม่ถูกต้อง|
|1309|พบบาร์โค้ดสินค้าแล้ว|
|1310|พบบาร์โค้ดที่เหมือนกันในไฟล์เอกสารที่นำเข้า|
|1311|รูปแบบบาร์โค้ดไม่ถูกต้อง|
|1312|รูปแบบรหัสสินค้าไม่ถูกต้อง|
|1313|ระยะเก็บรักษาสินค้าไม่ถูกต้อง|
|1314|รหัสSNสินค้าไม่ถูกต้องหรือไม่|
|1315|ราคาต้นทุนสินค้าไม่ถูกต้อง|
|1316|ความยาวสินค้าไม่ถูกต้อง|
|1317|ความกว้างสินค้าไม่ถูกต้อง|
|1318|ความสูงสินค้าไม่ถูกต้อง|
|1319|น้ำหนักสินค้าไม่ถูกต้อง|
|1320|น้ำหนักเตรียมแพ็คไม่ถูกต้อง|
|1321|จำเป็นต้องระบุจำนวนวันเก็บรักษาสำหรับสินค้าที่มีอายุเก็บรักษา|
|1322|จำนวนวันเก็บรักษาไม่ถูกต้อง|
|1323|จำเป็นต้องระบุจำนวนแจ้งวันเตือนใกล้หมดอายุสำหรับสินค้าที่มีอายุเก็บรักษา|
|1324|จำนวนวันที่แจ้งเตือนสินค้าใกล้หมดอายุไม่ถูกต้อง|
|1325|สินค้าที่มีวันเก็บรักษาต้องไม่ใช่สินค้ารหัสSN|
|1326|ที่อยู่ URL ภาพไม่ถูกต้อง|
|1327|รูปแบบของรูปภาพไม่ถูกต้อง|
|1328|จำนวนวันแจ้งเตือนสินค้าใกล้หมดอายุต้องไม่มากกว่าจำนวนวันเก็บรักษา|
|1329|เวลาการชำระเงินไม่ถูกต้อง|
|1330|ไม่สามารถเว้นว่างชื่อประเทศ|
|1331|ไม่สามารถเว้นว่างชื่อจังหวัด|
|1332|ไม่สามารถเว้นว่างชื่อจังหวัด|
|1333|ไม่สามารถเว้นว่างรหัสไปรษณีย์|
|1334||
|1335||
|1336||
|1337||
|1338||
|1400|พบสินค้าราคาเท่ากันในใบออเดอร์ที่เดียวกัน|
|1401|ยอดเงินของใบออเดอร์ไม่ตรงกัน|
|1402|เจ้าของสินค้าไม่ได้เซ็นต์สัญญากับคลังสินค้านี้หรือไม่พบรหัสIDคลังสินค้า|
|1403|พารามิเตอร์สถานะคุณภาพไม่ถูกต้อง|
|1501|สินค้ายังไม่วางจำหน่าย|
|1502|ไม่พบที่อยู่จัดส่งของผู้ใช้|
|1503|จำเป็นต้องกรอกข้อมูล|
|1504|พารามิเตอร์ไม่ถูกต้อง|
|1505|ไม่สามารถเว้นว่างที่อยู่|
|1506|ID ไม่ถูกต้อง|
|1507|จำนวนไม่ถูกต้อง|
|1508|จำนวนที่สั่งซื้อต้องไม่น้อยกว่ายอดขั้นต่ำ|
|1509|ไม่มีรายการดังกล่าวในรถเข็น|
|1510|จำนวนสินค้าไม่เพียงพอหรือของหมด , ไม่สามารถส่งคำสั่งซื้อได้|
|1511|สินค้าถูกลบแล้ว|
|1512|คำสั่งซื้ออนุมัติแล้วไม่สามารถยกเลิกได้|
|1513|สินค้าวางจำหน่ายแล้ว|
|1514|จำนวนสินค้าที่วางจำหน่ายต้องไม่เป็น0|
|1600|ไม่อนุญาติให้เข้าระบบ|
|1601|ไม่พบข้อมูลสาขาที่ให้บริการ|
|1602|ข้อมูลไม่ใช่รูปแบบ JSON|
|1603|ต้องระบุหมายเลขออเดอร์นอกระบบ|
|1604|ใบจัดส่งสินค้ายกเลิกแล้ว|
|1605|ใบจัดส่งสินค้าเริ่มส่งมอบแล้ว|
|1606|ใบจัดส่งสินค้าได้จัดส่งเรียบร้อยแล้ว|
|1607|ใบจัดส่งสินค้าที่รวมหรือแยกรายการไม่สามารถยกเลิกได้|
|1608|ไม่พบสินค้าหรือสินค้ายังไม่เปิดใช้งาน|
|1609|ไม่สามารถเว้นว่างรหัสสาขาให้บริการการได้|
|1610|รูปแบบรหัสสาขาให้บริการไม่ถูกต้อง|
|1611|ไม่สามารถเว้นว่างชื่อสาขาให้บริการได้|
|1612|ไม่สามารถเว้นว่างชื่อผู้ติดต่อได้|
|1613|ไม่สามารถเว้นว่างโค้ดและรหัสไปรษณีย์ได้|
|1614|พบข้อมูลสาขาบริการแล้ว|
|1615|ไม่สามารถเว้นว่างโซนจัดเก็บสินค้า|
|1616|ไม่สามารถเว้นว่างรหัสที่วางสินค้า|
|1617|ไม่พบโซนจัดเก็บสินค้าดังกล่าว|
|1618|รหัสที่วางสินค้าในตารางมีข้อมูลที่ซ้ำกัน|
|1619|มีข้อมูลในระบบแล้ว|
|1620|รูปแบบรหัสของที่วางสินค้าไม่ถูกต้อง|
|1621|รูปแบบลำดับของที่วางสินค้าไม่ถูกต้อง|
|1622|รูปแบบแถวของที่วางสินค้าไม่ถูกต้อง|
|1623|รูปแบบกลุ่มของที่วางสินค้าไม่ถูกต้อง|
|1624|รูปแบบชั้นของที่วางสินค้าไม่ถูกต้อง|
|1625|รูปแบบตำแหน่งของที่วางสินค้าไม่ถูกต้อง|
|1626|รูปแบบปริมาตรที่มากสุดของที่วางสินค้าไม่ถูกต้อง|
|1627|รูปแบบน้ำหนักที่สามารถรองรับได้ของที่วางสินค้าไม่ถูกต้อง|
|1628|รูปแบบความยาวของที่วางสินค้าไม่ถูกต้อง|
|1629|รูปแบบความกว้างของที่วางสินค้าไม่ถูกต้อง|
|1630|รูปแบบความสูงของที่วางสินค้าไม่ถูกต้อง|
|1631|ปแบบสถานะคุณภาพของที่วางสินค้าไม่ถูกต้อง|
|1632|ซนสินค้าไม่สามารถแก้ไขได้|
|1633|ไม่สามารถเว้นว่างID ที่วางสินค้า|
|1634|ID ที่วางสินค้าในตารางซ้ำกัน|
|1635|ไม่พบ ID ที่วางสินค้า|
|1636|ไม่สามารถเปลี่ยนแปลงสถานะคุณภาพได้|
|1637|ที่วางสินค้าที่ระบบจัดสรรไว้ล่วงหน้าไม่สามารถแก้ไขได้|
|1638|รูปแบบAsset ไม่ถูกต้อง|
|1639|เวลาเริ่มควรน้อยกว่าเวลาสิ้นสุด|
|1640|สินค้าถูกกำหนดเป็น Asset กรุณายื่นหมายเลข Asset|
|1641|สินค้าAsset มีการจัดเก็บภายในคลังแล้ว|
|1642|พบรหัสASSET ที่ซ้ำกัน|
|1643|จำนวนสินค้าAssetกับจำนวนสินค้าในใบรับเข้าคลังไม่ตรงกัน|
|1644|ประเภทข้อมูลรับเข้าคลังไม่ถูกต้อง|
|1645|รูปแบบสาขาไม่ถูกต้อง|
|1646|ระยะเวลาไม่ถูกต้|
|1647|ไม่พบรหัสดังกล่าว|
|1648|รูปแบบหรือประเภทพื้นที่จัดเก็บไม่ถูกต้อง|
