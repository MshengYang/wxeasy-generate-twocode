## 前言

小程序的需求，做的微信小程序需要生成二维码和条形码。github 上找了一下相关的库，发现基本上 star 相对多一点的插件，都是好几年前更新的，无法直接拉取使用，于是修改了插件以兼容当前微信版本.

以下是效果:

<img src="https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/d00d3e1408d240f1ae586a27e6a89e23~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=750&h=1542&s=82352&e=png&b=454545" width="300px" height="550px">

## 原始插件

[WYuanyang/wxapp-qrcode: 微信小程序--二维码生成器 (github.com)](https://github.com/WYuanyang/wxapp-qrcode)

[alsey/wxbarcode: 微信小程序条码、二维码生成模块 (github.com)](https://github.com/alsey/wxbarcode)

非常感谢大佬的贡献！这两个库的最后更新时间一个在 4 年以前，一个在 7 年以前，与当前版本存在一定兼容问题

## 兼容的微信基础库版本

> 2.32.2 更早没去看，有兼容问题请自行修改下哈

## 主要存在的兼容性问题

1、获取 canvas 实例的方法改变

```js
//before
ctx = wx.createCanvasContext(canvas, $this);
//after
const query = wx.createSelectorQuery();
query
  .select("#" + canvas)
  .fields({ node: true, size: true })
  .exec((res) => {
    const canvas = res[0].node;
    const ctx = canvas.getContext("2d");
    //xxxx
  });
```

2、canvas 实例的方法有变更

```js
//before
ctx.setFillStyle("#000000");
//after
ctx.fillStyle = "#000000";
```

3、新增了对不同机型 canvas 大小的兼容性

机型上显示相同大小的 Canvas，需要考虑到设备像素比（device pixel ratio）的影响。在高清屏幕上，为了保持图像的清晰度，一个 CSS 像素可能对应多个物理像素，而不同的设备像素比会导致不同的物理像素数量。

所以要对 canvas 缩放

```js
const dpr = wx.getSystemInfoSync().pixelRatio;
canvas.width = res[0].width * dpr;
canvas.height = res[0].height * dpr;
ctx.scale(dpr, dpr);
```

## 代码

你可以按照上面的兼容性问题下载原插件自己修改，也可以复制以下这个项目的**utils**文件
[MshengYang/wxeasy-generate-twocode (github.com)](https://github.com/MshengYang/wxeasy-generate-twocode)

## 使用示例

```html
<canvas style="width: 400rpx;height: 160rpx;" id="barcode" type="2d" />
<text style="margin-bottom: 24rpx;">12321saasgasgsaa</text>
<canvas style="width: 250rpx;height: 250rpx;" id="mycanvas" type="2d" />
```

```ts
import QR from "../../../utils/qrcode.js";
import barCode from "../../../utils/barcode.js";
//xxx
this.createQrCode("https://www.baidu.com", "mycanvas", 125, 125);
barCode.barcode("barcode", "asbfjkasgkas", 400, 160);
```

更多查看原插件
