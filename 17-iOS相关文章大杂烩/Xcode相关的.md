### Xcode

文章分享了能让 iOS 14 在旧版本的 Xcode 上运行的方法，以及将调试器附加到正在运行的应用程序的方法。另外也结合了代码和截图分享了在没有添加调试器的情况下查看变量的方法

[Debugging on iOS 14 with Xcode 11](https://hybridcattt.com/blog/debugging-on-ios14-with-xcode-11/)


[Podfile 的解析逻辑](https://mp.weixin.qq.com/s?__biz=MzA5MTM1NTc2Ng==&mid=2458324199&idx=1&sn=3886bbbcef3640bf97e16fcec34b451f&chksm=870e03feb0798ae84ab4b5dab26dfbe8ebbac0bca8491493fa4919f6069bfef58cd04df5ab34&scene=21#wechat_redirect)


[CocoaPods对三方库的管理探究](https://juejin.im/post/6895536359323205645)
[记录一次 Cocoapods Plugins 插件开发过程](https://juejin.cn/post/6893700845994278925)

### Xcode UI 渲染调试

#### Color Blended Layers

这个选项选项基于渲染程度对屏幕中的混合区域进行绿到红的高亮显示，越红表示性能越差，会对帧率等指标造成较大的影响。红色通常是由于多个半透明图层叠加引起

##### Color Hits Green and Misses Red

当 UIView.layer.shouldRasterize = YES 时，耗时的图片绘制会被缓存，并当做一个简单的扁平图片来呈现。这时候，如果页面的其他区块(比如 UITableViewCell 的复用)使用缓存直接命中，就显示绿色，反之，如果不命中，这时就显示红色。红色越多，性能越差。因为栅格化生成缓存的过程是有开销的，如果缓存能被大量命中和有效使用，则总体上会降低开销，反之则意味着要频繁生成新的缓存，这会让性能问题雪上加霜


##### Color Copied Images

对于 GPU 不支持的色彩格式的图片只能由 CPU 来处理，把这样的图片标为蓝色。蓝色越多，性能越差


##### Color Misaligned Images

这个选项检查了图片是否被缩放，以及像素是否对齐。被放缩的图片会被标记为黄色，像素不对齐则会标注为紫色。黄色、紫色越多，性能越差

##### Color Offscreen-Rendered Yellow

这个选项会把那些离屏渲染的图层显示为黄色。黄色越多，性能越差。这些显示为黄色的图层很可能需要用 shadowPath 或者 shouldRasterize 来优化