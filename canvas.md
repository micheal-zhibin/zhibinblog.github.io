---
layout: default
---

# [](#header-3)Canvas画图并生成对应的图片一些坑与相关的经验~
## [](#header-4)基础部分：
### [](#header-5)在html中加入一个canvas标签
```html
<canvas id="mycan" width="629" height="1145" class="hide"></canvas>
```
注意：记得要定义width和height属性，因为最后生成出来的图片尺寸就是由这里进行控制，不过如果需要实时显示canvas里面内容的话canvas的宽高还需要对应的适配屏幕，不需要实时显示的话直接隐藏元素就行

### [](#header-5)Js中获取canvas标签并创建画布
```js
var canvas= document.getElementById('mycan');
var context = canvas.getContext("2d");
```
获取canvas标签并且在canvas上创建2d的画布

### [](#header-5)一些简单的绘图操作
```js
//字体操作：
//设置粗细大小字体
context.font = "500 " + 26.8 + "px 方正兰亭圆简体";
//设置填充颜色
context.fillStyle = "#" + text_color;
//设置字体对齐方式
context.textAlign="center";
//从坐标点(313,67)开始绘制文字
context.fillText("JD服务号掐指一算", 313, 67)

//直线：
//调整笔触颜色
context.strokeStyle = '#fff'
//直线起点
cxt.moveTo(10,10);
//直线的另一端点（下一根直线的起点）
cxt.lineTo(150,50);
//与最后的端点进行连接成直线
cxt.lineTo(10,50);

//矩形
//绘制矩形，（0,0）左上角端点，（200,200）右下角端点
cxt.fillRect(0,0,200,200);

//圆
//开始路径描绘
cxt.beginPath();
//绘制圆形，圆心（70,18）半径为15，从0->2pai，顺时针
cxt.arc(70,18,15,0,Math.PI*2,true);
//结束路径绘制
cxt.closePath();
//填充
cxt.fill();

//曲线
//路径指定
context.beginPath();
//单独使用arcTo()方法必须指定绘图开始的基点
 
context.moveTo(20,20);
 
//创建两切线之间的弧/曲线
context.arcTo(200,150,20,280,50);
//起始端点(20,20)、端点1(290,150)、端点2(20,280)三点组成的夹角相切并且半径为50的圆弧，并且只保留两切点之间圆弧部分以及起始端点到第一个切点之间部分

//渐变
//创建线性渐变，开始于（0,0）结束语（175,50）
var grd=cxt.createLinearGradient(0,0,175,50);
//设置初始颜色
grd.addColorStop(0,"#FF0000");
//设置结束颜色
grd.addColorStop(1,"#00FF00");
//赋值到填充方式去使用
cxt.fillStyle=grd;

//图像
//创建image对象
var img=new Image();
//把图片的链接赋值，注意一定要是同源的！！！
img.src="1.png";
//在onload事件中展示图片确保图片已经加载成功
img.onload=function(e){
    cxt.drawImage(img,0,0);
}

//把后面绘图的操作进行矩阵转换
context.transform(1,Math.tan(11*Math.PI/180),0,1,0,0);
//在（10,159）插入尺寸为587x873的mainimg（Image对象）
context.drawImage(mainimg, 10*radio, 159*radio, 587*radio, 873*radio)
//获取图片时候要把画图这个操作放在Image对象的load事件里面去，不然会出现图片还没加载完就绘制导致canvas里面相应位置的图片丢失的结果。
//绘制一个从0-2pi，圆心在（215，305）半径为45的圆
context.arc(215, 305, 45, 0, 2 * Math.PI);
```
transform函数参数的含义
![](http://img11.360buyimg.com/jdphoto/s848x377_jfs/t12955/266/978247881/10767/ae8e80ad/5a17b4d3N75365761.png)

### [](#header-5)结束绘图并且转换成图片链接
```js
//绘制
context.stroke();
//生成base64
var strDataURI = canvas.toDataURL("image/png");
//把链接赋值到img标签中进行展示
$('#mypic').attr('src', strDataURI)
```

## [](#header-4)一些比较特殊的处理
### [](#header-5)因为canvas输出的图会导致原图一定的模糊，在网上找了一些方法，下面这个是在本次活动当中使用正常并有效（欢迎大家尝试别的并且补充）
#### [](#header-6)Html中添加一个对于hidpi-canvas.js的引用（对应代码大家可以自己下载）
```html
<script src="./js/hidpi-canvas.min.js"></script>
```
#### [](#header-6)获取转化因子
```js
var getPixelRatio = function(context) {
    var backingStore = context.backingStorePixelRatio ||
        context.webkitBackingStorePixelRatio ||
        context.mozBackingStorePixelRatio ||
        context.msBackingStorePixelRatio ||
        context.oBackingStorePixelRatio ||
        context.backingStorePixelRatio || 1;

    return (window.devicePixelRatio || 1) / backingStore;
};
var radio = getPixelRatio(context)
```
#### [](#header-6)在绘制图片到canvas的时候把转化因子添加到参数上去
```js
context.drawImage(mainimg, 10*radio, 159*radio, 587*radio, 873*radio)
```

### [](#header-5)在canvas里面获取微信头像
因为在canvas里面的图片只能用与当前页面同源的图片，所以如果需要绘制微信头像的时候需要把原有的域名替换成https://wq.jd.com，之后通过ajax请求来做一个跨域的请求从而解决头像替换成https://wq.jd.com之后还存在跨域的问题（这里必须注意的一点是，原本有打通过wq和wqs的nginx从而不会导致有跨域但是现在经过idc上的测试发现这个已经生效了，所以只能用ajax请求来解决）
```js
//获取头像链接
var url = JD.cookie.get('picture_url') || false
if (url) {
//如果存在头像，替换掉跨域的域名并且强制转为https协议
url = url.replace('http://wx.qlogo.cn', 'https://wq.jd.com')
url = url.replace('https://wx.qlogo.cn', 'https://wq.jd.com')
url = url.replace('/132', '/0')
} else {
//如果没有授权获取头像时跳转到授权页面
location.href = "//wq.jd.com/mlogin/wxv3/Login_BJ?appid=1&rurl=" + 
encodeURIComponent(location.href) + "&scope=snsapi_userinfo"
}
if(url.indexOf("http") == -1){
//如果已授权但是没有头像（即为’/0’）用默认头像替换
url = "//wqs.jd.com/promote/201710/dazhanghaohudong/images/defalut.png";
}
var retry = 0
function toDataURL(src, callback, outputFormat) {
var url = 0
    if (retry > 0) {
url = src + '?t=' + (1 * new Date())
    } else {
        url = src
    }
    var xhr = new XMLHttpRequest();
    xhr.onload = function () {
        var reader = new FileReader();
        reader.onloadend = function () {
            callback(reader.result);
        }
        var dataURL = reader.readAsDataURL(xhr.response);
    };
    xhr.onerror = xhr.ontimeout = xhr.onabort = function () {
        if (retry < 2) {
            toDataURL(src, callback, outputFormat)
            retry++
        } else {
            retry = 0
        }
    }
    xhr.open('GET', url);
    xhr.responseType = 'blob';//二进制类型
    xhr.send();
}
toDataURL(url,function(dataurl){
//读取后已经解决跨域的dataurl直接赋给Image对象并且在onload事件中继续canvas的绘制
    usrpic.src = dataurl;
    usrpic.onload = function() {
		……………
	}
}
```
注意：有可能因为缓存问题或者头像的一些特殊的情况，导致头像无法获取，最好的做法是先在同一个位置区域放一个一样大小的默认头像，当用户成功获取头像之后再在同一个区域绘制用户头像覆盖原有的默认头像。

### [](#header-5)圆形头像生成
因为在canvas里面的图片只能用与当前页面同源的图片，所以如果需要绘制微信头像的时候需要把原有的域名替换成https://wq.jd.com，之后通过ajax请求来做一个跨域的请求从而解决头像替换成https://wq.jd.com之后还存在跨域的问题（这里必须注意的一点是，原本有打通过wq和wqs的nginx从而不会导致有跨域但是现在经过idc上的测试发现这个已经生效了，所以只能用ajax请求来解决）
```js
//获取头像链接
function circleImg(ctx, img, x, y, r,radio) {
	//保存当前的画布
ctx.save();
var d =2 * r;
var cx = x + r;
var cy = y + r;
//绘制圆区域
ctx.arc(cx + 21, cy - 30, r, 0, 2 * Math.PI);
//剪切画布只剩下圆区域可视
ctx.clip();
//把对应的头像放置到对应圆的区域内（如果位置不对则不会显示）
ctx.drawImage(img, 191 * radio, 230 * radio, d * radio, d * radio);
//恢复绘图并把头像加入
ctx.restore();
}
```

### [](#header-5)时钟模拟
```html
<!doctype html>
<html>
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no" >
<title>canvas基础练习</title>
<style>
    .myCanvas{
        border:1px solid #f00;
    }
</style>
 
<script>
    var canvasWidth = 300;
    var canvasHeight = 300;
    //获取canvas的宽和高存放于全局变量备用，并返回canvas的context对象
    function getContextById(elementId){
        var canvas = document.getElementById(elementId);
        canvasWidth = canvas.width;
        canvasHeight = canvas.height;
        var context = canvas.getContext("2d");
        return context;
    }
 
    function drawText(h, m, s){
        var context = getContextById("myCanvas");
        //清除画布
        context.clearRect(0,0,canvasWidth,canvasHeight);
 
        //开始路径 
        context.beginPath(); 
        //绘制外圆 
        context.arc(100,100,99,0,2*Math.PI,false); 
        //绘制内圆 
        //context.moveTo(194,100);//将绘图游标移动到（x,y），不画线 
        context.arc(100,100,96,0,2*Math.PI,false);
        //绘制分针 
        context.fillStyle="rgba(0,255,255,0.5)";
        context.moveTo(100,100); 
        context.lineTo(100 + 85 * Math.sin(s * Math.PI / 30),100 - 85 * Math.cos(s * Math.PI / 30));
        //绘制分针 
        context.fillStyle="rgba(255,0,255,0.5)";
        context.moveTo(100,100); 
        context.lineTo(100 + 75 * Math.sin(m * Math.PI / 30 + s * Math.PI / 1080),100 - 75 * Math.cos(m * Math.PI / 30 + s * Math.PI / 1080));
        //绘制时针 
        context.fillStyle="rgba(0,0,0,0.5)";
        context.moveTo(100,100); 
        context.lineTo(100 + 65 * Math.sin(h * Math.PI / 6 + m * Math.PI / 360),100 - 65 * Math.cos(h * Math.PI / 6 + m * Math.PI / 360));
        //最后必须调用stroke()方法，这样才能把图像绘制到画布上。
        context.fillStyle="rgba(0,0,255,0.5)";
        context.stroke();
 
        //绘制文本
        context.font="bold 14px Arial"; 
        context.textAlign="center"; 
        context.textBaseline="middle";//文本的基线
        for(var i=1;i<=12;i++){
            context.fillText(i,100 + 85 * Math.sin(i * Math.PI / 6),100 - 85 * Math.cos(i * Math.PI / 6));
        }
        for(var i=0;i<60;i++){
            if(i % 5 == 0) {
                context.moveTo(100 + 91 * Math.sin(i * Math.PI / 30),100 - 91 * Math.cos(i * Math.PI / 30)); 
                context.lineTo(100 + 96 * Math.sin(i * Math.PI / 30),100 - 96 * Math.cos(i * Math.PI / 30)); 
                continue;
            }
            context.moveTo(100 + 93 * Math.sin(i * Math.PI / 30),100 - 93 * Math.cos(i * Math.PI / 30)); 
            context.lineTo(100 + 96 * Math.sin(i * Math.PI / 30),100 - 96 * Math.cos(i * Math.PI / 30));
        }
        context.strokeStyle="rgba(0,0,255,0.5)"; 
        //最后必须调用stroke()方法，这样才能把图像绘制到画布上。
        context.stroke();
    }

    window.onload = function() {
        setInterval(function() {
            var now = new Date();
            var hour = now.getHours(),
                minute = now.getMinutes();
                second = now.getSeconds();

            hour = hour >= 12 ? hour - 12 : hour;
            drawText(hour, minute, second);
        }, 1000)
    }
 
</script>
</head>
 
<body>
    <canvas width="200" height="200" class="myCanvas" id="myCanvas">
            你的浏览器不支持canvas。
    </canvas>
    <p>
    </p>
</body>
 
</html>
```
[back](./)
