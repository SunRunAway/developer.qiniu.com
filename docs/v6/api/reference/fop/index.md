---
layout: docs
title: 数据处理
order: 500
---

<a id="imageFop"></a>
# 数据处理

本节描述为资源执行数据处理的接口规格，涵盖以下内容：  

* [处理结果另存（saveas）][saveasHref]
* [持久化处理][pfpHref]
  * [触发持久化处理（pfop）][pfopHref]
  * [持久化处理状态查询（prefop）][prefopHref]
* [图片][imageHref]
  * [图片基本处理（imageView2）][view2Href]
  * [图片高级处理（imageMogr2）][mogr2Href]
  * [图片基本信息（imageInfo）][infoHref]
  * [图片EXIF信息（exif）][exifHref]
  * [图片水印处理（watermark）][watermarkHref]
  * [图片主色调（imageAve）][imageaveHref]
* [音视频][avHref]
  * [音视频处理（avthumb）][avHref]
  * [音视频切片（segtime）][seHref]
  * [音视频拼接（avconcat）][avconcatHref]
  * [音视频元信息（avinfo）][avinfoHref]
  * [视频帧缩略图（vframe）][vframeHref]
  * [视频水印][vwatermarkHref]
  * [视频采样缩略图（vsample）][vsampleHref]
  * [私有M3U8（pm3u8）][pm3u8Href]
* [文件拼接（concat）][concatHref]
* [资源下载二维码（qrcode）][qrcodeHref]
* [MD转HTML（md2html）][md2htmlHref]
* [第三方数据处理服务][third-partHref]
  * [广告过滤服务（ad）][adHref]
  * [图片鉴黄服务（nrop）][nropHref]
  * [文档转换服务（yifangyun_preview）][yifangyunHref]
  * [看大图云服务 (sequickimage) ][sequickimageHref]
  * [人脸裁剪服务 (facecrop2) ][facecrop2Href]

[imageHref]:        /docs/v6/api/reference/fop/image/index.html        "图片处理"
[avHref]:           /docs/v6/api/reference/fop/av/avthumb.html           "音视频处理"
[seHref]:           /docs/v6/api/reference/fop/av/segtime.html           "音视频切片"
[avconcatHref]:           /docs/v6/api/reference/fop/av/avconcat.html           "音视频拼接"
[avinfoHref]:           /docs/v6/api/reference/fop/av/avinfo.html           "音视频元信息"
[vframeHref]:           /docs/v6/api/reference/fop/av/vframe.html           "视频截图"
[vwatermarkHref]:           /docs/v6/api/reference/fop/av/video-watermark.html           "视频水印"
[vsampleHref]:           /docs/v6/api/reference/fop/av/vsample.html           "视频采样缩略图"
[pm3u8Href]:           /docs/v6/api/reference/fop/av/pm3u8.html           "私有M3U8"
[pfpHref]:         /docs/v6/api/reference/fop/pfop/index.html         "持久化处理"
[concatHref]:       /docs/v6/api/reference/fop/concat.html             "文件拼接"
[qrcodeHref]:       /docs/v6/api/reference/fop/qrcode.html             "二维码处理"
[md2htmlHref]:      /docs/v6/api/reference/fop/md2html.html            "Markdown文本处理"
[saveasHref]:       /docs/v6/api/reference/fop/saveas.html             "处理结果另存"
[third-partHref]:   /docs/v6/api/reference/fop/third-party/index.html        "第三方数据处理服务"
[pfopHref]:         /docs/v6/api/reference/fop/pfop/pfop.html         "触发持久化处理"
[prefopHref]:         /docs/v6/api/reference/fop/pfop/prefop.html         "持久化处理状态查询"
[view2Href]:         /docs/v6/api/reference/fop/image/imageview2.html         "基本图片处理"
[mogr2Href]:         /docs/v6/api/reference/fop/image/imagemogr2.html        "高级图片处理"
[infoHref]:        /docs/v6/api/reference/fop/image/imageinfo.html        "图片基本信息"
[exifHref]:        /docs/v6/api/reference/fop/image/exif.html        "图片Exif信息"
[watermarkHref]:        /docs/v6/api/reference/fop/image/watermark.html        "水印处理"
[imageaveHref]:        /docs/v6/api/reference/fop/image/imageave.html        "图片主色调"
[adHref]:        /docs/v6/api/reference/fop/third-party/ad.html        "广告过滤服务"
[nropHref]:        /docs/v6/api/reference/fop/third-party/nrop.html        "图片鉴黄服务"
[yifangyunHref]:        /docs/v6/api/reference/fop/third-party/yifangyun_preview.html        "文档转换服务"
