---
layout: docs
title: 水印处理（watermark）
order: 232
---

<a id="watermark"></a>
# 图片水印处理（watermark）
- [描述](#description)
- [图片水印](#pic-watermark)
  - [接口规格](#pic-watermark-spec)
  - [水印锚点参数表](#watermark-anchor-spec)
  - [请求](#pic-watermark-request)
  - [响应](#pic-watermark-response)
  - [示例](#pic-watermark-samples)
- [文字水印](#text-watermark)
  - [接口规格](#text-watermark-spec)
  - [请求](#text-watermark-request)
  - [响应](#text-watermark-response)
  - [示例](#text-watermark-samples)
- [同时打多个水印](#multi-watermark)
  - [接口规格](#multi-watermark-spec)
  - [请求](#multi-watermark-request)
  - [响应](#multi-watermark-response)
- [附注](#watermark-remarks)
- [内部参考资源](#imageView-internal-resources)

<a id="description"></a>
## 描述
七牛云存储提供三种水印接口：[图片水印](#pic-watermark)、[文字水印](#text-watermark)，以及一次请求中[同时打多个水印](#multi-watermark)。  

<a id="pic-watermark"></a>
## 图片水印

<a id="pic-watermark-spec"></a>
### 接口规格  

注意：接口规格不含任何空格与换行符，下列内容经过格式化以便阅读。  

```
watermark/1
           /image/<encodedImageURL>
           /dissolve/<dissolve>
           /gravity/<gravity>
           /dx/<distanceX>
           /dy/<distanceY>
           /ws/<watermarkScale>
```

<a id="pic-watermark-params"></a>

参数名称                    | 必填 | 说明
:-------------------------- | :--- | :---------------------------------------------------------------
`/image/<encodedImageURL>`  | 是   | 水印源图片网址（经过[URL安全的Base64编码][urlsafeBase64Href]），必须有效且返回一张图片
`/dissolve/<dissolve>`      |      | 透明度，取值范围1-100，缺省值为100（完全不透明）
`/gravity/<gravity>`        |      | 水印位置，参考[水印锚点参数表](#watermark-anchor-spec)，缺省值为`SouthEast`（右下角）
`/dx/<distanceX>`           |      | 横轴边距，单位:像素(px)，缺省值为10
`/dy/<distanceY>`           |      | 纵轴边距，单位:像素(px)，缺省值为10
`/ws/<watermarkScale>`           |      | 水印图片自适应原图的短边比例，取值范围0-1。

<a id="watermark-anchor-spec"></a>
### 水印锚点参数表

```
NorthWest     |     North      |     NorthEast
              |                |    
              |                |    
--------------+----------------+--------------
              |                |    
West          |     Center     |          East 
              |                |    
--------------+----------------+--------------
              |                |    
              |                |    
SouthWest     |     South      |     SouthEast
```

<a id="pic-watermark-request"></a>
### 请求

<a id="pic-watermark-request-syntax"></a>
#### 请求报文格式

```
GET <ImageDownloadURI>?<接口规格> HTTP/1.1
Host: <ImageDownloadHost>
```
**注意：**当您下载私有空间的资源时，`ImageDownloadURI`的生成方法请参考七牛的[下载凭证][download-tokenHref]。

**示例：**
资源为`http://developer.qiniu.com/resource/gogopher.jpg`，处理样式为`watermark/1/image/aHR0cDovL3d3dy5iMS5xaW5pdWRuLmNvbS9pbWFnZXMvbG9nby0yLnBuZw==/dissolve/50/gravity/SouthEast/dx/20/dy/20`。

```
#构造下载URL
DownloadUrl = 'http://developer.qiniu.com/resource/gogopher.jpg?watermark/1/image/aHR0cDovL3d3dy5iMS5xaW5pdWRuLmNvbS9pbWFnZXMvbG9nby0yLnBuZw==/dissolve/50/gravity/SouthEast/dx/20/dy/20'
……
#最后得到
RealDownloadUrl = 'http://developer.qiniu.com/resource/gogopher.jpg?watermark/1/image/aHR0cDovL3d3dy5iMS5xaW5pdWRuLmNvbS9pbWFnZXMvbG9nby0yLnBuZw==/dissolve/50/gravity/SouthEast/dx/20/dy/20&e=×××&token=MY_ACCESS_KEY:×××'
```
<a id="pic-watermark-request-header"></a>
#### 请求头部

头部名称       | 必填 | 说明
:------------- | :--- | :------------------------------------------
Host           | 是   | 下载服务器域名，可为七牛三级域名或自定义二级域名，参考[域名绑定][cnameBindingHref]

<a id="pic-watermark-response"></a>
### 响应

<a id="pic-watermark-response-syntax"></a>
#### 响应报文格式

```
HTTP/1.1 200 OK
Content-Type: <imageMimeType>

<imageBinaryData>
```

<a id="pic-watermark--response-header"></a>
#### 响应头部

头部名称       | 必填 | 说明
:------------- | :--- | :------------------------------------------
Content-Type   | 是   | MIME类型，成功时为图片的MIME类型，失败时为application/json
Cache-Control  |      | 缓存控制，失败时为no-store，不缓存

<a id="pic-watermark-response-content"></a>
#### 响应内容

■ 如果请求成功，返回图片的二进制数据。  
■ 如果请求失败，返回包含如下内容的JSON字符串（已格式化，便于阅读）：  

```
{
	"code":     <httpCode  int>, 
    "error":   "<errMsg    string>",
}
```

字段名称     | 必填 | 说明                              
:----------- | :--- | :--------------------------------------------------------------------
`code`       | 是   | HTTP状态码，请参考[响应状态](#pic-watermark-response-status)
`error`      | 是   | 与HTTP状态码对应的消息文本

<a id="pic-watermark-response-code"></a>
#### 响应状态码

HTTP状态码 | 含义
:--------- | :--------------------------
200        | 添加水印成功
400	       | 请求报文格式错误
404        | 资源不存在
599	       | 服务端操作失败。<p>如遇此错误，请将完整错误信息（包括所有HTTP响应头部）[通过邮件发送][sendBugReportHref]给我们。

<a id="pic-watermark-samples"></a>
### 图片水印示例

- 水印图片: <br>`ImageURL = "http://developer.qiniu.com/resource/logo-2.jpg"`<br>`encodedImageURL = urlsafe_base64_encode(ImageURL)`
- 水印透明度: 50% (`dissolve=50`)
- 水印位置: 右下角 (`gravity=SouthEast`)
- 横向边距: 20px
- 纵向边距: 20px

![图片水印](http://developer.qiniu.com/resource/gogopher.jpg?watermark/1/image/aHR0cDovL3d3dy5iMS5xaW5pdWRuLmNvbS9pbWFnZXMvbG9nby0yLnBuZw==/dissolve/50/gravity/SouthEast/dx/20/dy/20)

右键拷贝图片链接查看水印生成的具体规格参数。

<a id="text-watermark"></a>
## 文字水印

<a id="text-watermark-spec"></a>
### 规格接口规格  

注意：接口规格不含任何空格与换行符，下列内容经过格式化以便阅读。  

```
watermark/2
           /text/<encodedText>
           /font/<encodedFontName>
           /fontsize/<fontSize>
           /fill/<encodedTextColor>
           /dissolve/<dissolve>
           /gravity/<gravity>
           /dx/<distanceX>
           /dy/<distanceY>
```

<a id="text-watermark-params"></a>

参数名称                   | 必填 | 说明
:------------------------- | :--- | :-----------------------------------------------------------
`/text/<encodedText>`      | 是   | 水印文字内容（经过[URL安全的Base64编码][urlsafeBase64Href]）
`/font/<encodedFontName>`  |      | 水印文字字体（经过URL安全的Base64编码），缺省为黑体，详见支持[字体列表](http://kb.qiniu.com/support-fonts)<br><span style="color: red;">注意：中文水印必须指定中文字体。</span>
`/fontsize/<fontSize>`     |      | 水印文字大小，单位: [缇](http://en.wikipedia.org/wiki/Twip)，等于1/20磅，缺省值0（默认大小），参考DPI为72
`/fill/<encodedTextColor>` |      | 水印文字颜色，RGB格式，可以是颜色名称（比如`red`）或十六进制（比如`#FF0000`），参考[RGB颜色编码表](http://www.rapidtables.com/web/color/RGB_Color.htm)，缺省为白色(TODO). `（经过URL安全的Base64编码）`
`/dissolve/<dissolve>`     |      | 透明度，取值范围1-100，缺省值100（完全不透明）
`/gravity/<gravity>`       |      | 水印位置，参考水印位置参数表，缺省值为SouthEast（右下角）
`/dx/<distanceX>`          |      | 横轴边距，单位:像素(px)，缺省值为10
`/dy/<distanceY>`          |      | 纵轴边距，单位:像素(px)，缺省值为10

<a id="text-watermark-request"></a>
### 请求

<a id="text-watermark-request-syntax"></a>
#### 请求语法

```
GET <ImageDownloadURI>?<接口规格> HTTP/1.1
Host: <ImageDownloadHost>
```
**注意：**当您下载私有空间的资源时，`ImageDownloadURI`的生成方法请参考七牛的[下载凭证][download-tokenHref]。

**示例：**
资源为`http://developer.qiniu.com/resource/gogopher.jpg`，处理样式为`watermark/2/text/5LiD54mb5LqR5a2Y5YKo/font/5a6L5L2T/fontsize/1000/fill/d2hpdGU=/dissolve/85/gravity/SouthEast/dx/20/dy/20`。

```
#构造下载URL
DownloadUrl = 'http://developer.qiniu.com/resource/gogopher.jpg?watermark/2/text/5LiD54mb5LqR5a2Y5YKo/font/5a6L5L2T/fontsize/1000/fill/d2hpdGU=/dissolve/85/gravity/SouthEast/dx/20/dy/20'
……
#最后得到
RealDownloadUrl = 'http://developer.qiniu.com/resource/gogopher.jpg?watermark/2/text/5LiD54mb5LqR5a2Y5YKo/font/5a6L5L2T/fontsize/1000/fill/d2hpdGU=/dissolve/85/gravity/SouthEast/dx/20/dy/20&e=×××&token=MY_ACCESS_KEY:×××'
```

<a id="text-watermark-request-header"></a>
#### 请求头部

头部名称       | 必填 | 说明
:------------- | :--- | :------------------------------------------
Host           | 是   | 下载服务器域名，可为七牛三级域名或自定义二级域名，参考[域名绑定][cnameBindingHref]

<a id="text-watermark-response"></a>
### 响应

<a id="text-watermark-response-syntax"></a>
#### 响应报文格式

```
HTTP/1.1 200 OK
Content-Type: <imageMimeType>

<imageBinaryData>
```

<a id="text-watermark--response-header"></a>
#### 响应头部

头部名称       | 必填 | 说明
:------------- | :--- | :------------------------------------------
Content-Type   | 是   | MIME类型，成功时为图片的MIME类型，失败时为application/json
Cache-Control  |      | 缓存控制，失败时为no-store，不缓存

<a id="text-watermark-response-content"></a>
#### 响应内容

■ 如果请求成功，返回图片的二进制数据。  
■ 如果请求失败，返回包含如下内容的JSON字符串（已格式化，便于阅读）：  

```
{
	"code":     <httpCode  int>, 
    "error":   "<errMsg    string>",
}
```

字段名称     | 必填 | 说明                              
:----------- | :--- | :--------------------------------------------------------------------
`code`       | 是   | HTTP状态码，请参考[响应状态](#text-watermark-response-status)
`error`      | 是   | 与HTTP状态码对应的消息文本

<a id="text-watermark-response-code"></a>
#### 响应状态码

HTTP状态码 | 含义
:--------- | :--------------------------
200        | 添加水印成功
400	       | 请求报文格式错误
404        | 资源不存在
599	       | 服务端操作失败。<p>如遇此错误，请将完整错误信息（包括所有HTTP响应头部）[通过邮件发送][sendBugReportHref]给我们。

<a id="text-watermark-samples"></a>
### 文字水印样例

- 水印文本：`七牛云存储`
- 水印文本字体：`宋体`
- 水印文本字体大小：`1000`
- 水印文本颜色：`white`
- 水印文本透明度：15% (`dissolve=85`)
- 水印文本位置：右下脚 (`gravity=SouthEast`)

![文字水印](http://77fmtb.com1.z0.glb.clouddn.com/gogopher.jpg?watermark/2/text/5LiD54mb5LqR5a2Y5YKo/font/5a6L5L2T/fontsize/1000/fill/d2hpdGU=/dissolve/85/gravity/SouthEast/dx/20/dy/20)

右键拷贝图片链接查看水印生成的具体规格参数。

<a id="multi-watermark"></a>
## 同时打多个水印

本接口用于同时在一个原图上打多个不同类型的水印。  

<a id="multi-watermark-spec"></a>
### 接口规格  

注意1：接口规格不含任何空格与换行符，下列内容经过格式化以便阅读；  
注意2：省略号表示可以附加多个规格参数。  

```
watermark/3
           /text/<textWaterMarkParams1>
           /image/<imageWaterMarkParams1>
           /image/<imageWaterMarkParams2>
           /text/<textWaterMarkParams2>
           ...
```

注意：图片水印与文字水印的规格可以混写，按给定顺序处理。  

参数名称                         | 必填 | 说明
:------------------------------- | :--- | :-----------------------------------------------------------
`/image/<imageWaterMarkParams>`  |      | 参考[图片水印参数](#pic-watermark-params)
`/text/<textWaterMarkParams>`    |      | 参考[文字水印参数](#text-watermark-params)

<a id="multi-watermark-request"></a>
### 请求

<a id="multi-watermark-request-syntax"></a>
#### 请求语法

```
GET <imageDownloadURI>?<接口规格> HTTP/1.1
Host: <imageDownloadHost>
```
**注意：**当您下载私有空间的资源时，`ImageDownloadURI`的生成方法请参考七牛的[下载凭证][download-tokenHref]。

**示例：**
资源为`http://developer.qiniu.com/resource/gogopher.jpg`，处理样式为`watermark/3/image/aHR0cDovL2RldmVsb3Blci5xaW5pdS5jb20vcmVzb3VyY2UvbG9nby0yLmpwZw==/text/5LiD54mb5LqR5a2Y5YKo`。

```
#构造下载URL
DownloadUrl = 'http://developer.qiniu.com/resource/gogopher.jpg?watermark/3/image/aHR0cDovL2RldmVsb3Blci5xaW5pdS5jb20vcmVzb3VyY2UvbG9nby0yLmpwZw==/text/5LiD54mb5LqR5a2Y5YKo'
……
#最后得到
RealDownloadUrl = 'http://developer.qiniu.com/resource/gogopher.jpg?watermark/3/image/aHR0cDovL2RldmVsb3Blci5xaW5pdS5jb20vcmVzb3VyY2UvbG9nby0yLmpwZw==/text/5LiD54mb5LqR5a2Y5YKo&e=×××&token=MY_ACCESS_KEY:×××'
```

<a id="multi-watermark-request-header"></a>
#### 请求头部

头部名称       | 必填 | 说明
:------------- | :--- | :------------------------------------------
Host           | 是   | 下载服务器域名，可为七牛三级域名或自定义二级域名，参考[域名绑定][cnameBindingHref]

<a id="multi-watermark-response"></a>
### 响应

<a id="multi-watermark-response-syntax"></a>
#### 响应报文格式

```
HTTP/1.1 200 OK
Content-Type: <imageMimeType>

<imageBinaryData>
```

<a id="multi-watermark--response-header"></a>
#### 响应头部

头部名称       | 必填 | 说明
:------------- | :--- | :------------------------------------------
Content-Type   | 是   | MIME类型，成功时为图片的MIME类型，失败时为application/json
Cache-Control  |      | 缓存控制，失败时为no-store，不缓存

<a id="multi-watermark-response-content"></a>
#### 响应内容

■ 如果请求成功，返回图片的二进制数据。  
■ 如果请求失败，返回包含如下内容的JSON字符串（已格式化，便于阅读）：  

```
{
	"code":     <httpCode  int>, 
    "error":   "<errMsg    string>",
}
```

字段名称     | 必填 | 说明                              
:----------- | :--- | :--------------------------------------------------------------------
`code`       | 是   | HTTP状态码，请参考[响应状态](#multi-watermark-response-status)
`error`      | 是   | 与HTTP状态码对应的消息文本

<a id="multi-watermark-response-code"></a>
#### 响应状态码

HTTP状态码 | 含义
:--------- | :--------------------------
200        | 添加水印成功
400	       | 请求报文格式错误
404        | 资源不存在
599	       | 服务端操作失败。<p>如遇此错误，请将完整错误信息（包括所有HTTP响应头部）[通过邮件发送][sendBugReportHref]给我们。

<a id="watermark-remarks"></a>
## 附注

- 原图尺寸小于水印图片尺寸时，直接返回原图，不加水印。
- watermark生成的图片会被七牛云存储缓存以加速下载，但不会持久化。需要持久化的缩略图，请参考[触发持久化处理][pfopHref]和[saveas处理][saveasHref]。  

- 使用[qrsctl][qrsctlHref]工具，给图片下载URL中的水印规格添加别名，使得URL更加友好。

	```
    qrsctl login <email> <password>
    qrsctl style <bucket> watermarked.jpg watermark/2/text/<encodedText>
    qrsctl separator <bucket> -
	```
	
	此时，如下两个URL等价:

	```
    http://<Domain>/<Key>?watermark/2/text/<encodedText>
    http://<Domain>/<Key>-watermarked.jpg
	```

- 设置[原图保护][resourceProtectHref]，仅限使用缩略图样式别名的友好URL形式来访问目标图片。

	设置原图保护后，原图不能访问：

	```
    http://<Domain>/<Key>
	```

	同时也禁止根据图像处理API对原图进行参数枚举：

	```
    http://<Domain>/<Key>?watermark/2/text/<encodedText>
	```

	此时只能访问指定规格的图片资源：

	```
    http://<Domain>/<Key>-watermarked.jpg
	```

<a id="imageView-internal-resources"></a>
## 内部参考资源

- [域名绑定][cnameBindingHref]
- [触发持久化处理][pfopHref]
- [saveas处理][saveasHref]
- [URL安全的Base64编码][urlsafeBase64Href]

[qrsctlHref]:       http://developer.qiniu.com/docs/v6/tools/qrsctl.html                "七牛工具"
[resourceProtectHref]: http://kb.qiniu.com/52uad43y                    "原图保护"
[sendBugReportHref]:   mailto:support@qiniu.com?subject=599错误日志    "发送错误报告"
[cnameBindingHref]:             http://kb.qiniu.com/53a48154                     "域名绑定"
[pfopHref]:                     http://developer.qiniu.com/docs/v6/api/reference/fop/pfop/pfop.html                            "触发持久化处理"
[saveasHref]:                   http://developer.qiniu.com/docs/v6/api/reference/fop/saveas.html                                   "saveas处理"

[urlsafeBase64Href]: http://developer.qiniu.com/docs/v6/api/overview/appendix.html#urlsafe-base64 "URL安全的Base64编码"
[download-tokenHref]: http://developer.qiniu.com/docs/v6/api/reference/security/download-token.html  "下载凭证"