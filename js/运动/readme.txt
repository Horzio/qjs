简化版运动--》
Math.animation(form, to, duration, easing, callback);
其中：

form和to是必须参数，表示动画起始数值和结束数值；
duration，easing，callback理论上都是可选参数，但是实际上callback肯定是要使用的，因为实时变化的数值就是通过callback返回的。然后，duration，easing，callback这3个参数的顺序是任意的。具体来讲：
duration为动画持续时间，默认300，默认单位是毫秒，建议使用数值，例如600，也支持带单位，例如600ms或者0.6s；
easing为缓动的类型，字符串类型，源自Tween.js。例如：'Linear'，'Quad.easeIn'，'Bounce.easeInOut'等等，需要注意大小写。 其中，默认值是'Linear'；
callback为回调函数，支持2个参数（value, isEnding），其中value表示实时变化的计算值，isEnding是布尔值，表示动画是否完全停止。
使用套路-》》
Math.animation(0, 800 - 42, function (value) {
    ball.style.transform = 'translateX(' + value + 'px)';
}, 'Bounce.easeInOut', 600);    


twwen.js--->>>
Linear：线性匀速运动效果；
Quadratic：二次方的缓动（t^2）；
Cubic：三次方的缓动（t^3）；
Quartic：四次方的缓动（t^4）；
Quintic：五次方的缓动（t^5）；
Sinusoidal：正弦曲线的缓动（sin(t)）；
Exponential：指数曲线的缓动（2^t）；
Circular：圆形曲线的缓动（sqrt(1-t^2)）；
Elastic：指数衰减的正弦曲线缓动；
Back：超过范围的三次方缓动（(s+1)*t^3 – s*t^2）；
Bounce：指数衰减的反弹缓动。
每个效果都分三个缓动方式，分别是：

easeIn：从0开始加速的缓动，也就是先慢后快；
easeOut：减速到0的缓动，也就是先快后慢；
easeInOut：前半段从0开始加速，后半段减速到0的缓动。

-->
所有的这些缓动算法都离不开下面4个参数，t, b, c, d，含义如下：
 *t: current time（当前时间）；
 * b: beginning value（初始值）；
 * c: change in value（变化量）；
 * d: duration（持续时间）。
 列如：
 Tween.Linear = function(t, b, c, d) { 
    return c*t/d + b; 
}
比方说我们要从位置0的地方运动到100，时间是10秒钟，此时，b, c, d三个参数就已经确认了，b初始值就是0，变化值c就是100-0就是100，
最终的时间就是10，此时，只要给一个小于最终时间10的值，Tween.Linear就会返回当前时间应该的坐标，
例如，假设此时动画进行到第5秒，也就是t为5，则得到（截图自Chrome控制台），
----》
如果浏览器不支持requestAnimationFrame，我们使用传统的setTimeout定时器兼容实现即可。
// requestAnimationFrame的兼容处理
if (!window.requestAnimationFrame) {
    requestAnimationFrame = function(fn) {
        setTimeout(fn, 17);
    };	
}
---》使用tween套路
var t = 0, b = 0, c = 100, d = 10;
var step = function () {
    // value就是当前的位置值
    // 例如我们可以设置DOM.style.left = value + 'px'实现定位
    var value = Tween.Linear(t, b, c, d);
    t++;
    if (t <= d) {
         // 继续运动
         requestAnimationFrame(step);
    } else {
        // 动画结束
    }
};

--->>关于抛物线：原理y = a*x*x + b*x + c
1.抛物线函数求解
a, b, c三个参数。

3个参数需要3个条件才能完全求解。
由于我们要实现元素A到元素B的抛物线运动，因此，我们可以指定两个点的坐标位置，也就是知道了2个条件，那第3个条件呢？
a可以近似理解为弧度，曲率。在我们实现效果的时候，这个是应该要可控的。
把a作为一个参数常量。
于是，已知参数a，以及两个点的坐标，求该抛物线函数。有
y = a*x*x + b*x + c
↓
y1 = a * x1*x1 + b*x1 + c;
y2 = a * x2*x2 + b*x2 + c;
↓
a已知，求b, c
2：见paowuxian.js文件；
调用方式
var myParabola = funParabola(element, target, options);
element表示移动的元素，例如demo中的小球球。原生DOM节点
target表示目标元素。例如demo中的椭圆形的大便池。原生DOM节点
options为可选参数。各个API名称以及含义如下：
    speed 表示每帧移动的像素大小，每帧（对于大部分显示屏）大约16~17毫秒。默认大小是166.67。也就是默认10px/ms.
    curvature 可以近似理解为抛物线的开头大小，也就是曲率。正数表示开口向下。默认大小是0.001. 数值越大，开头越小，弧度越高。因为web页面动辄大小几百像素，因此，曲率值较小。
    progress 表示抛物线运动过程中的回调，支持两个参数，x, y，表示当前的坐标，您可以根据这些坐标值做一些特殊的处理。
    complete 表示抛物线运动结束后的回调。
    可选参数speed不是指x轴的位移，也不是y轴位移，而是抛物线特定坐标的切线距离。利用切线公式：y'=2ax+b就可以计算出x轴这一帧应该移动的距离。形成奔向目的地的运动效果。

贝塞尔曲线与任意轨迹-->>
无规律的曲线运动-->
http://cubic-bezier.com/#.25,.1,.25,1   -------->想要什么样的路径效果，调整好COPY头部cubic-bezier就好

CSS3贝塞尔起点是0,0; 终点是1, 1;
cubic-bezier(x1,y1, x2,y2)
SVG
<path d="M0 0 C x1 y1, x2, y2, 1 1"/>
canvas
ctx.moveTo(0,0);
ctx.bezierCurveTo(x1,y1,x2,y2,1,1);