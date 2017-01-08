title: React Native入个小门
categories: technology 
date: 2016-05-27 22:36
comments: true
tags: [前端,计算机]
---

React Native是个啥?
* 创造者:FaceBook
* 时间:React.js Conf 2015 上发布，9月推出Android版本
* 使用范围:用于开发iOS和Android原生应用
* 基本特点:
 * 用React.js抽象操作系统的原生UI组件，继而代替DOM元素来渲染。-->Learn once, write anywhere
 * 在后台React Native运行在主线程之外，而在另一个专门的后台线程里运行JavaScript引擎，两个线程之间通过异步消息协议来通信。-->具备流畅和反应灵敏的优势
* 对应技术栈
 * JSX vs HTML
 * CSS-layout vs css
 * ECMAScript 6 vs ECMAScript 5
 * React Native View vs DOM

如何玩React Native?
* 按照React Native官方文档搭建环境，如下有[英文版](http://facebook.github.io/react-native/docs/getting-started.html)，[中文版](http://reactnative.cn/docs/0.26/getting-started.html)。模拟器推荐使用[Genymotion](https://www.genymotion.com/)。IDE推荐使用[WebStorm](https://www.jetbrains.com/webstorm/),其注册码[在此](http://us.idea.lanyus.com/)。整个上述过程中可能遇见的问题如下:
     * $ANDROID_NDK 和 $ANDROID_HOME 没有正确设置: 
        * 解决办法：vi .bashrc和.bash_profile 加上 /usr/local/opt/android-ndk和/usr/local/opt/android-sdk,并且source一下这两个文件，然后eoch下上述两个变量是否已经存在。
* 运行官方案例(即在虚拟机上查看RN各个组件的实际效果)，方法如此[链接](https://github.com/facebook/react-native/tree/master/Examples/UIExplorer),可能遇见的问题如下:
    *   ``What went wrong: Execution failed for task ':ReactAndroid:buildReactNdkLib'.
``  
        * [解决办法](http://stackoverflow.com/questions/36209774/unable-to-run-react-native-uiexplorer-example-project)
    * 安卓新项目起不来
        * 注意下是不是老项目的shell还在运行，需要重新编译链接一次。
* [运行F8 App方法](https://github.com/fbsamples/f8app),其开发指南[在此](http://f8-app.liaohuqiu.net/)。可能遇见的问题如下:
    * pod install无反应问题。
        * [解决办法](http://www.cnblogs.com/yiqiedejuanlian/p/3698788.html),关于CocoaPods使用方法及各种问题强烈推荐下面[这篇文章](http://www.jianshu.com/p/969dcb9907cf)
    * ``Could not find com.google.android.gms:play-services-gcm:8.3.0.``
        * [解决办法](https://github.com/fbsamples/f8app/issues/16)
    *   ```
    $ adb reverse tcp:8081 tcp:8081
error: closed  
error: closed  
                ```
        * 原因：对于 Android 5.0 及以上的设备，可以直接运行；对于其以下的版本，按照官方文档，需要按 menu 键或者摇晃手机，在开发菜单中设置一下 dev server 的 IP 地址。
* 接下来就可以欢快的自己写代码了。写的途中还会遇见各种坑，就具体情况具体分析吧。总结下：
    * 确保相关工具和Android SDK都是最新版本。
    * 确保网络畅通，特别是翻好墙。（此处应有微笑
    * 注意看官方文档及其issue。
