### **接口调用说明**

所有接口通过wx对象(也可使用jWeixin对象)来调用，参数是一个对象，除了每个接口本身需要传的参数之外，还有以下通用参数：

1. success：接口调用成功时执行的回调函数。
2. fail：接口调用失败时执行的回调函数。
3. complete：接口调用完成时执行的回调函数，无论成功或失败都会执行。
4. cancel：用户点击取消时的回调函数，仅部分有用户取消操作的api才会用到。
5. trigger: 监听Menu中的按钮点击时触发的方法，该方法仅支持Menu中的相关接口。

备注：不要尝试在trigger中使用ajax异步请求修改本次分享的内容，因为客户端分享操作是一个同步操作，这时候使用ajax的回包会还没有返回。

以上几个函数都带有一个参数，类型为对象，其中除了每个接口本身返回的数据之外，还有一个通用属性errMsg，其值格式如下：

调用成功时："xxx:ok" ，其中xxx为调用的接口名

用户取消时："xxx:cancel"，其中xxx为调用的接口名

调用失败时：其值为具体错误信息



# **基础接口**



### **判断当前客户端版本是否支持指定JS接口**

```js
wx.checkJsApi({
  jsApiList: ['chooseImage'], // 需要检测的JS接口列表，所有JS接口列表见附录2,
  success: function(res) {
  // 以键值对的形式返回，可用的api值true，不可用为false
  // 如：{"checkResult":{"chooseImage":true},"errMsg":"checkJsApi:ok"}
  }
});
```

备注：checkJsApi接口是客户端6.0.2新引入的一个预留接口，第一期开放的接口均可不使用checkJsApi来检测。



# **微信扫一扫**



### **调起微信扫一扫接口**

```js
wx.scanQRCode({
  needResult: 0, // 默认为0，扫描结果由微信处理，1则直接返回扫描结果，
  scanType: ["qrCode","barCode"], // 可以指定扫二维码还是一维码，默认二者都有
  success: function (res) {
    var result = res.resultStr; // 当needResult 为 1 时，扫码返回的结果
  }
});
```



