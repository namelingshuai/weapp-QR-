# weapp-QR
# 结合别人的代码写出了一个小程序二维码生成,也就是在后台获取到字符串然后放到生成的二维码里面

# 样式自己写就可以了


# index.wxml
```html
<canvas style="width: 430rpx;height: 430rpx;" bindtap="previewImg" canvas-id="mycanvas"></canvas>
```
# index.js
```javascript
Page({
    data: {

    },
    onLoad: function (options) {
        // 生命周期函数--监听页面加载
        var size = _this.setCanvasSize(); //动态设置画布大小
        var initUrl = "这是一个测试"; // 这里是一个字符串，可以用后台返回的
        _this.createQrCode(initUrl, "mycanvas", size.w, size.h);
    },

    // 二维码生成
    setCanvasSize: function () {
        var size = {};
        try {
            var res = wx.getSystemInfoSync();
            var scale = 750 / 450; //不同屏幕下canvas的适配比例；设计稿是750宽
            var width = res.windowWidth / scale;
            var height = width; //canvas画布为正方形
            size.w = width;
            size.h = height;
        } catch (e) {
            // Do something when catch error
            console.log("获取设备信息失败" + e);
        }
        return size;
    },
    createQrCode: function (url, canvasId, cavW, cavH) {
        //调用插件中的draw方法，绘制二维码图片
        QR.api.draw(url, canvasId, cavW, cavH);
        setTimeout(() => {
            this.canvasToTempImage();
        }, 1000);

    },
    //获取临时缓存照片路径，存入全局data中
    canvasToTempImage: function () {
        var that = this;
        wx.canvasToTempFilePath({
            canvasId: 'mycanvas',
            success: function (res) {
                var tempFilePath = res.tempFilePath;
                console.log(tempFilePath);
                that.setData({
                    imagePath: tempFilePath,
                    // canvasHidden:true
                });
                wx.setStorage({
                    key: 'imagePath',
                    data: tempFilePath,
                })
            },
            fail: function (res) {
                console.log(res);
            }
        });
    },
    //点击图片进行预览，长按保存分享图片
    previewImg: function (e) {
        var img = this.data.imagePath;
        console.log(img);
        wx.previewImage({
            current: img, // 当前显示图片的http链接
            urls: [img] // 需要预览的图片http链接列表
        })

    },
    // formSubmit: function (e) {
    //     var that = this;
    //     var url = e.detail.value.url;
    //     that.setData({
    //         maskHidden: false,
    //     });
    //     wx.showToast({
    //         title: '生成中...',
    //         icon: 'loading',
    //         duration: 2000
    //     });
    //     var st = setTimeout(function () {
    //         wx.hideToast()
    //         var size = that.setCanvasSize();
    //         //绘制二维码
    //         that.createQrCode(url, "mycanvas", size.w, size.h);
    //         that.setData({
    //             maskHidden: true
    //         });
    //         clearTimeout(st);
    //     }, 2000)

    // },
})
```
