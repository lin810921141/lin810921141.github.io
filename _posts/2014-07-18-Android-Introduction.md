---
layout:default
---

首先一来就是搭建Android集成开发环境的，需要的就是JDK,Eclipse,Android SDK,ADT
Android程序是用java编程的，所以需要用到JDK和Eclipse，其次还要用到Android开发的工具包Android SDK，最后就是Eclipse的插件ADT，通过ADT把Android开发功能集成到Eclipse中。

>AVD：安卓虚拟机，程序运行时的环境，有真机速度快很多
DDMS：调试用
ADB：查看AVD，电脑手机文件之间的复制，启动shell窗口执行Linux命令，安装于卸载APK
Android程序与以往的程序不一样，它是代码和资源分开的，而且不是运行在java虚拟机上，而是运行在Dalvik虚拟机上，代码编译之后.class文件需要经过DX编译成*.dex文件，资源经过AAPT打包成*.ap_文件，最后用apkbuilder合成*.apk文件，有ADB安装到Android系统上运行

Android应用结构分析：
>一个项目必备的有src目录（保存java代码），res目录（保存资源），AndroidManifest.xml文件（控制Android应用的整体的属性，名称，图标，访问权限等，安卓的4大组件也在这里配置）
发布项目之后会生产bin目录（保存生成的目标文件，例如java的二进制文件，*.dex,*.ap_），gen目录（自动生成的目录，存有R.java）

R.java：资源清单
>每类资源对应R类的一个内部类，每个具体的资源对应于内部类的一个public static final int类型的Filed
java代码引用资源的方法：R.String.app_name
XMl文件中引用资源的方法：@<内部类的类名>/<资源名称>    另外资源中的标示符的定义不需要具体的资源，只要@+id/ok，就定义了一个ok的标识符了

AndroidManifest.xml：应用的清单文件
>这个文件清楚地说明该应用名称，图标和包含的组件等。
