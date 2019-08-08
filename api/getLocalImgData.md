# wx.getLocalImgData

官方文档 https://mp.weixin.qq.com/wiki?t=resource/res_main&id=mp1421141115 -> :4 图像接口

调用

```js
wx.chooseImage({
  count: 1, // 默认9
  sizeType: ["original", "compressed"], // 可以指定是原图还是压缩图，默认二者都有
  sourceType: ["album", "camera"], // 可以指定来源是相册还是相机，默认二者都有
  success: function(res) {
    var localIds = res.localIds; // 返回选定照片的本地ID列表，localId可以作为img标签的src属性显示图片
  }
});
```

时得到的 localIds 可以通过

```js
wx.getLocalImgData({
  localId: "", // 图片的localID
  success: function(res) {
    var localData = res.localData; // localData是图片的base64数据，可以用img标签显示
  }
});
```

得到 base64 值，通常应用场景是上传图片，直接使用拍照得到的图片占位显示；问题是`localData`的值在不同平台表现是不一致的；官方文档如下说明：

> 备注：此接口仅在 iOS WKWebview 下提供，用于兼容 iOS WKWebview 不支持 localId 直接显示图片的问题。具体可参考《iOS 网页开发适配指南》

实际情况是，`getLocalImgData`在 Android 和 iOS 里都可以访问，但 Android 下返回的`localData`不包含头部的 base64 码，你需要`'data:image/jpeg;base64,' + localData`得到完整的 base64 值, 且值内可能包含换行符，需要`replace(/\r|\n/g, '')`; 而对于 iOS，头部是 `jgp`（连 `jpg` 都不是）而非 `jpeg`，需要`replace('data:image/jgp', 'data:image/jpeg')`, 不过不替换也能使用；
