---
title: 微信公众号项目的兼容问题
date: 2020-04-18 13:00:00
categories: [微信公众号]
tags: [微信公众号,兼容性]
---


## 安卓兼容问题

### 安卓版本8，location.href页面跳转问题
在移动web中，经常会使用window.location.href去跳转页面，这个方法在绝大多数浏览器中都不会
存在问题，但是在安卓8手机的微信自带浏览器中，会出现一个奇怪的bug。
调用window.location.href，浏览器进度条加载完后，页面并没有跳转，还是停留在当前页面。
解决办法：
#### 第一种
```javascript
window.location.href = baseUrl +'?v='+(new Date().getTime());
```
#### 第二种
```javascript
window.location.href = baseUrl + '?v='+Math.random();  
```

## ios兼容问题
<!-- more -->
### ios8 使用箭头函数页面空白问题
ios8版本以及以下版本对于箭头函数的支持还没有兼容，如果使用Babel可以做ES5兼容转换处理，否则自行使用ES5语法

### ios input表单输入法问题

我们在给网页添加表单的时候，如果只想表单只读(readonly)，我们会给input添加readonly属性
```html
<input type="text" readonly>   
```
但是实际效果在安卓手机显示只读输入法没有弹出显示，在ios手机输入法却照样弹出键盘，右上角“完成”按钮，令人费解。经过研究后发现必须添加如下属性才能正常显示
```html
<input type="text" readonly unselectable="on" onfocus="this.blur()">
```
总结ios不弹出输入法需要满足以下要求

① 文本框获取焦点

② 手指触屏（网页区域，混合开发触屏app头不能让webview弹出键盘）

③ 没有延迟（不会ajax回调，不会延迟）
### ios 时间戳显示问题

如果你遇到了在iOS上无法正确解析时间戳的问题，请确认在创建Date 对象时没有使用new Date('2020-01-01')这样的写法，iOS 不支持以中划线分隔的日期格式，正确写法是new Date('2020/01/01')。对此问题的详细解释：[stackoverflow](https://stackoverflow.com/questions/13363673/javascript-date-is-invalid-on-ios)。

### 音频自动播放问题，ios默认不自动播放

因为新年需求，要求开发一个换装的小游戏，其中有个功能是背景音乐(bgm)的实现，开始认为很简单但是实际开发中还是遇到了问题。安卓可以自动播放但ios却不可以。H5的出现对多媒体在网页上的视频播放提供很好的支持，以前网页播放视频基本依赖于flash等插件。而H5的video标签实现了网页播放视频无插件化。当前，绝大多数浏览器都支持H5的video标签，除了部分PC端浏览器比如IE还不支持。
#### Android上
在 video标签上添加autoplay属性即可
```html
<audio id="audio" src="music.mp4" controls="controls" autoplay="autoplay">
您的浏览器不支持 audio 标签。
</audio>
```
#### IOS 上
ios比较特殊，为了不吓着用户，ios是禁止自动播放的，也就是说autoplay是没有效果的，解决的办法就是自己手动触发播放时间一下。
但是直接手动触发又会存在一个时灵时不灵的尴尬现象，看了网上很多解决的办法，例如，手动给document添加事件，然后触发它，或者直接在js，ready方法区内触发video元素的play（）事件，这些我都尝试过，都存在时灵时不灵的现象。
最为稳妥的就是在 微信JS-SDK 上去触发play（）事件
```javascript
 wx.config({
        // 配置信息, 即使不正确也能使用 wx.ready
        debug: false,
        appId: 'appid',
        timestamp: '',
        nonceStr: '',
        signature: '',
        jsApiList: []
    });
    wx.ready(function() {
        $("#audio").play();
    });
```
或者就是用户触碰屏幕时触发play()事件
