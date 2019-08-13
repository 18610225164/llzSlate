---
title: Fulfillment 开放平台接口

language_tabs:
  - http: HTTP

toc_footers:
  - <a href="/">English</a>
  - <a href="http://www.fulfillment.com">Flash Express</a>

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
Sandbox    | https://api-sandbox.fulfillment.com
Production | https://api.fulfillment.com

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
## 仓库库存查询 `POST` `/open/goodsStock`

```http
POST /open/v1/orders HTTP/1.1
Host: api-sandbox.fulfillment.com
Content-Type: application/x-www-form-urlencoded
Accept: application/json
```

> 请求示例

```css
```

> 请求示例参数说明

```yaml

```


### 请求参数说明

|名称|数据类型|是否必填|说明|
|---|---|---|---|
|barCode|string|true|条形码 多个用,隔开，验证正则  ^[\w-]+(,[\w-]+)*|
|warehouseId|string|false|仓库ID，不填默认全部仓库|
|goodsStatus|string|false|质量状态[normal]正品，默认[bad]残品|

> 返回数据体示例

```json
```

### 返回数据体

***data: Array\[goodsStockInfo对象\]，goodsStockInfo对象说明：***

|名称|数据类型|说明|
|---|---|---|
|availableInventory|integer|可售库存|
|totalInventory|integer|实物库存|
|inventory|integer|可用库存|

## 出库单添加 `POST /open/returnWarehouseAdd`

```http
POST /open/v1/orders HTTP/1.1
Host: api-sandbox.fulfillment.com
Content-Type: application/x-www-form-urlencoded
Accept: application/json
```

> 请求示例

```css
```

> 请求示例参数说明

```yaml

```


### 请求参数说明

|名称|数据类型|是否必填|说明|
|---|---|---|---|
|warehouseId|string|true|仓库ID|
|consigneeName|string|true|收货人|
|consigneePhone|string|true|联系方式|
|province|string|true|省|
|city|string|true|市|
|district|string|true|区|
|postalCode|string|true|邮编|
|consigneeAddress|string|true|详细地址|
|goods|string|true|出库商品json, 详细说明在下方|
|orderSn|string|true|外部订单号|
|status|string|false|\[1\]待审核|
|type|string|false|\[1\]退供出库|
|channelSource|string|false|来源渠道|
|nodeSn|string|false|网点编码|
|logistics|string|false|配送方式【物流 logistics】 【快递express】 【 到仓自取self】|
|outTime|string|false|期望出库时间|
|goodsStatus|string|false|【normal】正品  【bad】残品|
|remark|string|false|备注|

***goods字段：出库商品json说明***

|名称|数据类型|是否必填|说明|
|---|---|---|---|
|sellerGoodsId|string|true|商品id|
|number|string|true|商品数量|

> 返回数据体示例

```json
```

### 返回数据体

无

## 发货单新增 `POST` `/Order/addOrder`

```http
POST /open/v1/orders HTTP/1.1
Host: api-sandbox.fulfillment.com
Content-Type: application/x-www-form-urlencoded
Accept: application/json
```

> 请求示例

```css
```

> 请求示例参数说明

```yaml

```


### 请求参数说明

|名称|数据类型|是否必填|说明|
|---|---|---|---|
|platformSourceId|string|true|wms端添加的货主店铺主键ID|
|orderSn|string|true|订单号|
|orderSourceId|string|true|订单主键ID|
|code|string|true|地址code|
|postalCode|string|true|邮编|
|consigneeName|string|true|收件人名称|
|consigneeAddress|string|true|收件人详细地址|
|phoneNumber|string|true|联系电话|
|buyerMessage|string|true|买家留言|
|paidPrice|string|true|订单金额|
|logisticCharge|string|true|运费|
|totalPrice|string|true|订单总金额|
|payMode|string|true|支付方式  【1】货到付款  【2】银行转账|
|goods|string|true|订单商品list\[订单商品信息的json\]|
|type|string|true|订单类型 [1]销售订单 (默认) [2]补货订单|
|deliverySn|string|true|原发货单号(type=2时必填)|

*goods字段: 订单商品信息的json说明*

|名称|数据类型|是否必填|说明|
|---|---|---|---|
|id|string|true|商品id|
|num|string|true|商品数量|
|price|string|true|商品单价|

> 返回数据体示例

```json
```

### 返回数据体

无

## 入库通知单新增 `POST` `/arrival_notice/create`

```http
POST /open/v1/orders HTTP/1.1
Host: api-sandbox.fulfillment.com
Content-Type: application/x-www-form-urlencoded
Accept: application/json
```

> 请求示例

```css
```

> 请求示例参数说明

```yaml

```

`/open/goodsStock`


### 请求参数说明

|名称|数据类型|是否必填|说明|
|---|---|---|---|
|aaaaaa|string|true|bbbbbbb|
|aaaaaa|string|true|bbbbbbb|
|aaaaaa|string|true|bbbbbbb|
|aaaaaa|string|true|bbbbbbb|
|aaaaaa|string|true|bbbbbbb|
|aaaaaa|string|true|bbbbbbb|
|aaaaaa|string|true|bbbbbbb|
|aaaaaa|string|true|bbbbbbb|
|aaaaaa|string|true|bbbbbbb|
|aaaaaa|string|true|bbbbbbb|
|aaaaaa|string|true|bbbbbbb|
|aaaaaa|string|true|bbbbbbb|
|aaaaaa|string|true|bbbbbbb|
|aaaaaa|string|true|bbbbbbb|

> 返回数据体示例

```json
```

### 返回数据体

***data: Array\[goodsStockInfo对象\]，goodsStockInfo对象说明：***

|名称|数据类型|说明|
|---|---|---|
|availableInventory|integer|可售库存|
|totalInventory|integer|实物库存|
|inventory|integer|可用库存|

## 销退单新增 `POST` `/rollback_order/add`

```http
POST /open/v1/orders HTTP/1.1
Host: api-sandbox.fulfillment.com
Content-Type: application/x-www-form-urlencoded
Accept: application/json
```

> 请求示例

```css
```

> 请求示例参数说明

```yaml

```

`/open/goodsStock`


### 请求参数说明

|名称|数据类型|是否必填|说明|
|---|---|---|---|
|aaaaaa|string|true|bbbbbbb|
|aaaaaa|string|true|bbbbbbb|
|aaaaaa|string|true|bbbbbbb|
|aaaaaa|string|true|bbbbbbb|
|aaaaaa|string|true|bbbbbbb|
|aaaaaa|string|true|bbbbbbb|
|aaaaaa|string|true|bbbbbbb|
|aaaaaa|string|true|bbbbbbb|
|aaaaaa|string|true|bbbbbbb|
|aaaaaa|string|true|bbbbbbb|
|aaaaaa|string|true|bbbbbbb|
|aaaaaa|string|true|bbbbbbb|
|aaaaaa|string|true|bbbbbbb|
|aaaaaa|string|true|bbbbbbb|

> 返回数据体示例

```json
```

### 返回数据体

***data: Array\[goodsStockInfo对象\]，goodsStockInfo对象说明：***

|名称|数据类型|说明|
|---|---|---|
|availableInventory|integer|可售库存|
|totalInventory|integer|实物库存|
|inventory|integer|可用库存|


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
