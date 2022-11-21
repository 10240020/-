# -
组件库
图片压缩
 get_img(url) {
        let that = this
        console.log("选择图片返回的路径", url)
        wx.getImageInfo({
            src: url,
            success(res) {
                console.log("路径", res.path)
                console.log('获得原始图片大小', res.width, res.height)
                var originWidth, originHeight;
                originHeight = res.height;
                originWidth = res.width;
                // 最大尺寸限制   //压缩比例
                var maxWidth = originWidth >= originHeight ? 300 : 600,
                    maxHeight = originWidth >= originHeight ? 600 : 300;
                // 目标尺寸
                var targetWidth = originWidth,
                    targetHeight = originHeight;
                //等比例压缩，如果宽度大于高度，则宽度优先，否则高度优先
                if (originWidth > maxWidth || originHeight > maxHeight) {
                    if (originWidth / originHeight > maxWidth / maxHeight) {
                        // 要求宽度*(原生图片比例)=新图片尺寸
                        targetWidth = maxWidth;
                        targetHeight = Math.round(maxWidth * (originHeight / originWidth));
                    } else {
                        targetHeight = maxHeight;
                        targetWidth = Math.round(maxHeight * (originWidth / originHeight));
                    }
                }
                console.log("压缩后的图片大小", targetWidth, targetHeight)
                var ctx = wx.createCanvasContext('mycanvas');
                ctx.clearRect(0, 0, targetWidth, targetHeight);
                ctx.drawImage(res.path, 0, 0, targetWidth, targetHeight);
                ctx.draw();
                //更新canvas大小
                that.setData({
                    cwidth: targetWidth,
                    cheight: targetHeight
                });
                setTimeout(function () {
                    wx.canvasToTempFilePath({
                        canvasId: 'mycanvas',
                        success: (res) => {
                            console.log("压缩后的临时路径：", res.tempFilePath)
                        },
                        fail: (err) => {
                            console.error(err)
                        }
                    }, this)
                }, 400); //延迟400毫秒为了等canvas画上
            }
        })
    },
