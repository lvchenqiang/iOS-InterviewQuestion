
### UIView和CALayer的关系

UIView为CALayer提供内容,以及负责处理触摸等用户交互事件，参与事件响应链。

CALayer负责显示内容contents,基于 单一职责的设计概念。



### CALayer子类的一些专用图层

#### CAShapeLayer
CAShapeLayer 是一个通过矢量图形而不是bitmap来绘制的图层子类。 可以指定颜色和线宽等属性，使用CGPath定义想要绘制的图形，最后CAShapeLayer就会自动渲染出来。

使用CAShapeLayer的好处:
* 渲染快速: CAShapeLayer使用了硬件加速，绘制同一图形会比CoreGraphics快很多
* 高效使用内存: 一个CAShapeLayer 不需要像普通的CAlyer一样创建一个寄宿图形,所以无论有多大，都不会占用太多的内存。
* 不会被图层边界裁掉。 一个CALyer可以在边界之外绘制。 不会像普通的CALyer一样被裁减掉。
* 不会出现像素化。 做3D变换时,它不像一个寄宿图的普通图层一样变得像素化。


#### CATextLayer
CATextLayer 以图层的形式包含了UILabel几乎所有的绘制特性,并且额外提供了一些新的特性。


CATextLayer的优势:
* 比UILabel渲染的快的多
* 使用了Core Text,支持富文本,并且渲染的特别快。
* 通过layerClass,返回CATextLayer,提高渲染的效率



#### CATransformLayer
CATransformLayer 不同于普通的CALyer.因为它不能显示它自己的内容，只有当存在了一个能作用于子图层的变换它才能真正存在。

#### CAGradientLayer
CAGradientLayer是用来生成两种或更多颜色平滑渐变的。CAGradientLayer的真正好处在于绘制的时候使用了硬件加速。

#### CAReplicatorLayer
CAReplicatorLayer 的目的是为了高效生成许多相似的图层。它会绘制一个或多个图层的子图层，并在复制体上应用不同的变化。

CAReplicatorLayer的优势：
* 反射 ReflectionView:https://github.com/nicklockwood/ReflectionView


##### CAScrollLayer
layer层等同于UITableView 和 UIScrollView


##### CATiledLayer
适用于绘制一个很大的图片,常见的例子就是一个高像素的照片或者是地球表面的详细地图。
CATiledLayer 为载入大图造成的性能问题提供了解决方案:将大图分解成小片然后按需加入。

在主线程中 使用 UIImage的imageName: 或者 imageWithContentofFile 将会阻塞你的用户界面,引起动画的卡顿。


##### CAEmitterLayer
CAEmitterLayer 是一个高性能的粒子引擎，被用来创建实时例子动画。


##### CAEAGLayer

CAEAGLayer是一个CALyer的一个子类,用来显示任意的OpenGL图形。

##### AVPlayerLayer
AVPlayerLayer是用来在iOS上播放视频的，她的高级接口例如 MPMoivePlayer的底层实现，提供了显示视频的底层控制。
