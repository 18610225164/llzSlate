---
title: Fulfillment 开放平台接口

language_tabs:
  - http: HTTP

toc_footers:
  - <a href="/en.html">English</a>
  - <a href="/th.html">ภาษาไทย</a>

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
|0|系统内部错误|
|1|成功|
|1000|数据校验错误|
|1001|客户不存在|
|100112|请先登录|
|1002|签名不通过|
|1003|订单已存在|
|1004|发件地匹配不到|
|1005|目的地匹配不到|
|1006|订单不存在|
|1007|仓库不存在|
|1008|暂未开通COD服务|
|1009|COD不能小于0|
|1010|当前客户存在未完成的揽件通知，无需发送通知|
|1011|货主不存在|
|1012|销售平台不存在|
|1013|快递公司不存在|
|1014|发货单不存在|
|1015|已揽收的订单不可作废|
|1016|统一售价不能为0|
|1017|店铺不存在|
|1018|发件地和仓库不能都为空|
|1019|包裹号pno和客户mchId不匹配|
|1020|保价不能小于0|
|1021|发件地址邮政编码只能是5位的数字|
|1022|发件地址暂未开通服务|
|1023|原发货单必填|
|1024|没有找到此发货单的信息|
|1025|订单未发货，不能补货|
|1026|联系方式必填|
|1027|寄件人姓名必填|
|1101|电话号码格式有误|
|1102|商品信息不合法|
|1103|条形码不能为空|
|1104|商品不存在|
|1105|商品数量必须|
|1106|商品重复|
|1107|订单号不允许为空|
|1108|时间格式不合法|
|1109|订单格式不合法|
|1110|商品名称不能为空|
|1201|下单日期不能为空|
|1202|订单金额不能为空|
|1203|详细地址及省市区邮编不能为空|
|1204|收货人姓名不能为空|
|1205|电话号码不能为空|
|1206|付款方式不能为空|
|1207|详细地址及邮编不能为空|
|1208|省市区不匹配|
|1300|下单日期不合法|
|1301|订单金额不合法|
|1302|省市区邮编不匹配|
|1303|付款方式不合法|
|1304|商品不存在|
|1305|商品未启用|
|1306|购买数量不合法|
|1307|售价不合法|
|1308|运费不合法|
|1309|商品条形码已存在|
|1310|导入文件存在相同的条形码|
|1311|条形码格式有误|
|1312|商品货号格式有误|
|1313|商品保质期参数有误|
|1314|是否SN参数有误|
|1315|成本价参数不合法|
|1316|长度参数不合法|
|1317|宽度参数不合法|
|1318|高度参数不合法|
|1319|重量参数不合法|
|1320|预打包重量不合法|
|1321|保质期商品的保质期天数必填|
|1322|保质期天数不合法|
|1323|保质期商品的临期预警天数必填|
|1324|临期预警天数不合法|
|1325|保质期商品不能同时是SN商品|
|1326|图片地址错误|
|1327|图片格式错误|
|1328|预警天数不能大于保质期天数|
|1329|付款时间不合法|
|1330|国家不能为空|
|1331|府不能为空|
|1332|市不能为空|
|1333|邮编不能为空|
|1334|退款状态不合法|
|1335|商品不能为空|
|1336|商品格式错误|
|1337|退货类型必填|
|1338|退货类型格式错误|
|1400|相同订单存在售价相同的商品|
|1401|订单金额不一致|
|1402|货主没有签约此仓库，或仓库ID不存在|
|1403|质量状态参数有误|
|1501|商品未上架|
|1502|用户收货地址不存在|
|1503|参数必填|
|1504|参数错误|
|1505|地址不能为空|
|1506|ID参数错误|
|1507|数量参数错误|
|1508|购买数量不能低于起订量|
|1509|购物车无此商品|
|1510|商品库存不足或缺货，无法提交订单|
|1511|商品已下架|
|1512|订单已审核无法取消|
|1513|商品已上架|
|1514|上架数量不能为0|
|1600|未授权登录|
|1601|网点信息匹配不到|
|1602|参数非JSON格式|
|1603|外部订单号必须|
|1604|发货单已经取消|
|1605|发货单已经开始交接|
|1606|发货单已经发货完成|
|1607|合并拆分过的发货单不能取消|
|1608|商品不存在或未启用|
|1609|网点编号不能为空|
|1610|网点编号格式有误|
|1611|网点名称不能为空|
|1612|联系人不能为空|
|1613|Code和邮编不能为空|
|1614|网点信息已存在|
|1615|库区不能为空|
|1616|货位编码不能为空|
|1617|该库区不存在|
|1618|表格内货位编码重复|
|1619|系统中已存在|
|1620|货位编码格式错误|
|1621|货位顺序号格式错误|
|1622|货位排格式错误|
|1623|货位组格式错误|
|1624|货位层格式错误|
|1625|货位位格式错误|
|1626|货位最大体积格式错误|
|1627|货位最大承重格式错误|
|1628|货位长格式错误|
|1629|货位宽格式错误|
|1630|货位高格式错误|
|1631|货位质量状态格式错误|
|1632|库区不能修改|
|1633|货位ID不能为空|
|1634|表内货位ID重复|
|1635|货位ID不存在|
|1636|质量状态不可更改|
|1637|系统预置货位不能修改|
|1638|ASSET格式错误|
|1639|开始时间应小于结束时间|
|1640|商品启用了ASSET，请提交ASSET编码|
|1641|ASSET在库内已经存在|
|1642|ASSET重复|
|1643|ASSET数量与入库单商品数量不一致|
|1644|入库类型参数错误|
|1645|网点格式错误|
|1646|日期范围不正确|
|1647|编码不存在|
|1648|货位类型格式错误|

