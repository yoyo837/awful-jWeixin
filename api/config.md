# wx.config

官方文档 https://mp.weixin.qq.com/wiki?t=resource/res_main&id=mp1421141115 -> :1.1.3 步骤三：通过 config 接口注入权限验证配置

调用

```js
wx.config({
    debug: true, // 开启调试模式,调用的所有api的返回值会在客户端alert出来，若要查看传入的参数，可以在pc端打开，参数信息会通过log打出，仅在pc端时才会打印。
    appId: '', // 必填，公众号的唯一标识
    timestamp: , // 必填，生成签名的时间戳
    nonceStr: '', // 必填，生成签名的随机串
    signature: '',// 必填，签名
    jsApiList: [] // 必填，需要使用的JS接口列表
});
```

时的入参数一般需要从后台接口获得，多页应用每个页面调用并`wx.config`即可，坑的地方在于，对于 SPA 的应用在 iOS 下却总是提示签名错误，官方文档说：

> 所有需要使用 JS-SDK 的页面必须先注入配置信息，否则将无法调用（同一个 url 仅需调用一次，对于变化 url 的 SPA 的 web app 可在每次 url 变化时进行调用,目前 Android 微信客户端不支持 pushState 的 H5 新特性，所以使用 pushState 来实现 web app 的页面会导致签名失败，此问题会在 Android6.2 中修复）。

实际情况是，iOS 只需要使用收入访问的入口地址调用后台获得的数据缓存起来并`wx.config`即可，后续路由到任何页面任何时候调用`wx.config`都直接使用前面的缓存；Android 是每个页面都从新调用后台获取并`wx.config`；与官方文档解释的是否支持新特性和版本修复牛头不对马嘴；

在 iOS 下当入口地址页面逻辑存在即时性的前端跳转，比如是分发页面，任然可能报错提示签名错误，可以试试在`window.onload`中获得后台接口需要的`url`;
