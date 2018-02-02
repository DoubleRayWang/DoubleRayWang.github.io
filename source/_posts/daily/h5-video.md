---
layout: post
title: "微信H5 Video 开发小结"
date: 2018-02-01 14:41:15 +0800
categories: 知识点
tags: [CSS,JavaScript,HTML]
---

{% fullimage /styles/images/wechat/video/video_bg.jpg, 微信H5 Video, 微信H5 Video %}

最近开发公众号内H5网页过程中，为了达到“炫”的效果，有些特效采用了video展示，但是微信内的video展示着实太费神了，这里总结一些遇到的坑和解决的办法。<!-- more -->

## video的属性

```html
<video
  id="video" 
  src="video.mp4" 
  controls = "true"
  poster="images.jpg"   <!--视频封面-->
  preload="auto" 
  webkit-playsinline="true" <!--这个属性是ios 10中设置可以让视频在小窗内播放，也就是不是全屏播放 --> 
  playsinline="true"  <!--IOS微信浏览器支持小窗内播放-->
  x-webkit-airplay="allow" 
  x5-video-player-type="h5"  <!--启用H5播放器,是wechat安卓版特性-->
  x5-video-player-fullscreen="true" <!--全屏设置，设置为 true 是防止横屏-->
  x5-video-orientation="portraint" <!--播放器支付的方向， landscape横屏，portraint竖屏，默认值为竖屏-->
  style="object-fit:fill">
</video>
```

+ `src`: 视频的地址

+ `controls`: 加上这个属性，Gecko 会提供用户控制，允许用户控制视频的播放，包括音量，跨帧，暂停/恢复播放。

+ `poster`: 属性规定视频下载时显示的图像，或者在用户点击播放按钮前显示的图像。如果未设置该属性，则使用视频的第一帧来代替。

+ `preload`: 属性规定在页面加载后载入视频。

+ `webkit-playsinline`和`playsinline`: **视频播放时局域播放，不脱离文档流**。但是这个属性比较特别，需要嵌入网页的APP比如WeChat中UIwebview 的allowsInlineMediaPlayback = YES webview.allowsInlineMediaPlayback = YES，才能生效。换句话说，如果APP不设置，你页面中加了这标签也无效，这也就是为什么安卓手机WeChat 播放视频总是全屏，因为APP不支持playsinline，而IOS的WeChat却支持。
  这里就要补充下，如果是想做全屏直播或者全屏H5体验的用户，IOS需要设置删除 webkit-playsinline 标签，因为你设置 false 是不支持的 ，安卓则不需要，因为默认全屏。但这时候全屏是有播放控件的，无论你有没有设置control。 做直播的可能用得着播放控件，但是全屏H5是不需要的，那么去除全屏播放时候的控件，需要以下设置：同层播放
  
+ `x-webkit-airplay="allow"` : **这个属性应该是使此视频支持ios的AirPlay功能**。使用AirPlay可以直接从使用iOS的设备上的不同位置播放视频、音乐还有照片文件，也就是说通过AirPlay功能可以实现影音文件的无线播放，当然前提是播放的终端设备也要支持相应的功能

+ `x5-video-player-type`: **启用同层H5播放器，就是在视频全屏的时候，div可以呈现在视频层上，也是WeChat安卓版特有的属性**。同层播放别名也叫做沉浸式播放，播放的时候看似全屏，但是已经除去了control和微信的导航栏，只留下"X"和"<"两键。目前的同层播放器只在Android（包括微信）上生效，暂时不支持iOS。至于为什么同层播放只对安卓开放，是因为安卓不能像ISO一样局域播放，默认的全屏会使得一些界面操作被阻拦，如果是全屏H5还好，但是做直播的话，诸如弹幕那样的功能就无法实现了，所以这时候同层播放的概念就解决了这个问题。不过在测试的过程中发现，不同版本的IOS和安卓效果略有不同

+ `x5-video-orientation`: **声明播放器支持的方向，可选值landscape 横屏, portraint竖屏**。默认值`portraint`。无论是直播还是全屏H5一般都是竖屏播放，但是这个属性需要x5-video-player-type开启H5模式

+ `x5­-video­-player­-fullscreen`:**全屏设置**。它又两个属性值，ture和false，true支持全屏播放，false不支持全屏播放。其实，IOS 微信浏览器是Chrome的内核，相关的属性都支持，也是为什么X5同层播放不支持的原因。安卓微信浏览器是X5内核，一些属性标签比如playsinline就不支持，所以始终全屏。

> playsinline 属性在 iOS 10 之前需要写成 webkit-playsinline，它的浏览器厂商前缀在 iOS 10 中被移除。但是目前 iOS 微信还不支持去掉前缀的写法，两个属性最好都加上。

## Media方法和属性

HTMLVideoElement 和HTMLAudioElement 均继承自HTMLMediaElement

```JS
//错误状态 
Media.error; //null:正常 
Media.error.code; //1.用户终止 2.网络错误 3.解码错误 4.URL无效 

//网络状态 
Media.currentSrc; //返回当前资源的URL 
Media.src = value; //返回或设置当前资源的URL 
Media.canPlayType(type); //是否能播放某种格式的资源 
Media.networkState; //0.此元素未初始化 1.正常但没有使用网络 2.正在下载数据 3.没有找到资源 
Media.load(); //重新加载src指定的资源 
Media.buffered; //返回已缓冲区域，TimeRanges 
Media.preload; //none:不预载 metadata:预载资源信息 auto: 

//准备状态 
Media.readyState;  //1:HAVE_NOTHING 2:HAVE_METADATA 3.HAVE_CURRENT_DATA 4.HAVE_FUTURE_DATA 5.HAVE_ENOUGH_DATA 
Media.seeking; //是否正在seeking 

//回放状态 
Media.currentTime = value; //当前播放的位置，赋值可改变位置 
Media.startTime; //一般为0，如果为流媒体或者不从0开始的资源，则不为0 
Media.duration; //当前资源长度 流返回无限 
Media.paused; //是否暂停 
Media.defaultPlaybackRate = value;//默认的回放速度，可以设置 
Media.playbackRate = value;//当前播放速度，设置后马上改变 
Media.played; //返回已经播放的区域，TimeRanges，关于此对象见下文 
Media.seekable; //返回可以seek的区域 TimeRanges 
Media.ended; //是否结束 
Media.autoPlay; //是否自动播放 
Media.loop; //是否循环播放 
Media.play();  //播放 
Media.pause();  //暂停 

//控制 
Media.controls;//是否有默认控制条 
Media.volume = value; //音量 
Media.muted = value; //静音 

//TimeRanges(区域)对象 
TimeRanges.length; //区域段数 
TimeRanges.start(index) //第index段区域的开始位置 
TimeRanges.end(index) //第index段区域的结束位置 
```

## Media相关的事件

```
"loadstart"：客户端开始请求数据
"progress"：客户端正在请求数据
"suspend"：延迟下载
"abort"：客户端主动终止下载（不是因为错误引起），
"error"：请求数据时遇到错误
"stalled"：网速失速
"play"：play()和autoplay开始播放时触发
"pause"：pause()触发
"loadedmetadata"：成功获取资源长度
"loadeddata"：
"waiting"：等待数据，并非错误
"playing"：开始回放
"canplay"：可以播放，但中途可能因为加载而暂停
"canplaythrough"：可以播放，歌曲全部加载完毕
"seeking"：寻找中
"seeked"：寻找完毕
"timeupdate"：播放时间改变
"ended"：播放结束
"ratechange"：播放速率改变
"durationchange"：资源长度改变
"volumechange"：音量改变
```

## 事件、属性及展示效果使用常见问题

### 预加载

移动端原因， video 并不会主动去预加载用户未需求的资源，因此我们需要手动去触发 video 的预加载资源（安卓端下，滚动行为不归属在用户操作行为中）

```js
document.getElementById('my-video').play();
```

这样就可以了吗？不，万恶的微信限制了必须用户行为才能播放媒体资源，因此我们只能再祭出万能 hack：

```html
<script src="//res.wx.qq.com/open/js/jweixin-1.0.0.js"></script>
```

```js
document.addEventListener('DOMContentLoaded', function() {
    var video = document.getElementById('my-video');
    function preload() {
        video.play();
        setTimeout(function () {
            video.pause();
        }, 200);
    }
    document.addEventListener("WeixinJSBridgeReady",  preload, false);
    if (typeof WeixinJSBridge == "object" && typeof WeixinJSBridge.invoke == "function") {
        WeixinJSBridge.invoke("getNetworkType", {}, preload);
    }
    //preload(); 
});
```

### 全屏处理

#### IOS

ios加`playsinline`属性，之前只带webkit前缀的在ios10以后，会吊起系统自带播放器，两个属性都加上基本ios端都可以保证内敛到浏览器webview里面了。如果仍有个别版本的ios会吊起播放器，还可以引用一个库`iphone-inline-video`（具体用法很简单看它 [github](https://github.com/bfred-it/iphone-inline-video)，这里不介绍了，只需加js一句话，css加点），github地址加上`playsinline webkit-playsinline`这两个属性和这个库基本可以保证ios端没有问题了（不过亲测，只加这两个属性不引入库好像也是ok的，至今没有在ios端微信没有出现问题，如果你要兼容uc或者qq的浏览器建议带上这个库）

#### android

x5-video-player-type="h5"属性，腾讯x5内核系的android微信和手Q内置浏览器用的浏览器webview的内核，使用这个属性在微信中视频会有不同的表现，会呈现全屏状态，貌似播放控件剥去了，至少加了这个属性后视频上层可以有其他dom元素出现了（非腾讯白名单机制的一种处理措施）。


### 自动播放

android始终不能自动播放，不多说。值得一提的是经测现在ios10后版本的safari和微信都不让视频自动播放了（顺带音频也不能自动播放了），但微信提供了一个事件`WeixinJSBridgeReady`，在微信嵌入webview全局的这个事件触发后，视频仍可以自动播放，这个应该是现在在ios端微信的视频自动播放的比较靠谱的方式，其他如手q或者其他浏览器，建议就引导用户出发触屏的行为操作出发比较好。

```js
document.addEventListener("WeixinJSBridgeReady", function (){ 
    video.play();
    video.pause();
}, false)
```

### 播放控制

对于video或者audio等媒体元素，有一些方法，常用的有`play(),pause()`;也有一些事件，如`'loadstart','canplay','canplaythrough','ended','timeupdate'.....`等等。
在移动端有一些坑需要注意，不要轻易使用媒体元素的除`'ended','timeupdate'`以外event事件，在不同的机子上可能有不同的情况产生，例如：ios下监听`'canplay'`和`'canplaythrough'`（是否已缓冲了足够的数据可以流畅播放）,当加载时是不会触发的，即使`preload="auto"`也没用，但在pc的chrome调试器下和android下，是会在加载阶段就触发。ios需要播放后才会触发。总之就是现在的视频标准还不尽完善，有很多坑要注意，要使用前最好自己亲测一遍
就是当第一次播放视频的时候ios端，如果网络慢，视频从开始播到能展现画面会有短暂的黑屏（处理视频源数据的时间），为了避免这个黑屏，可以在视频上加个div浮层（可以一个假的视频第一帧），然后用`timeupdate`方法监听，视屏播放及有画面的时候再移除浮层

```js
video.addEventListener('timeupdate',function (){
    //当视频的currentTime大于0.1时表示黑屏时间已过，已有视频画面，可以移除浮层（.pagestart的div元素）
    if ( !video.isPlayed && this.currentTime>0.1 ){
        $('.pagestart').fadeOut(500);
        video.isPlayed = !0;
    }
})
```

### 隐藏播放控件

据说腾讯的android团队的x5内核团队放开了视频播放的限制，视频不一定调用它们那个备受诟病的视频播放器了，`x5-video-player-type="h5"`属性这个属性就有点那个意思，虽然体验还是有点...（导航栏也会清理）但至少播放器控件没有了，上层可以浮div或者其他元素了，这个还是值得一提。还有一点值得说的是，带播放器控件的隐藏.

```html
<div class="videobox" ontouchmove="return false;">
    <video id="mainvideo" src="test.mp4" x5-video-player-type="h5" playsinline webkit-playsinline></video>
</div>
```

比如这个videobox在android下隐藏，只用`display：none`貌似还是不行的，但加个`z-index:-1`，设置成-1就可以达到隐藏播放器控件的目的了。