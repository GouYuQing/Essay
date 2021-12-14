# Flutter环境搭建

https://flutterchina.club/setup-windows/官网学习

## 1.概念

Flutter是谷歌的移动UI框架，可以快速在iOS和Android上构建高质量的原生用户界面。 Flutter可以与现有的代码一起工作。在全世界，Flutter正在被越来越多的开发者和组织使用，并且Flutter是完全免费、开源的。

优点：

- 跨平台：支持平台最多的框架
- 原生用户界面：体验感好
- 开源免费

## 2.主流框架对比

与react native比生态环境差些

RN是将View编译成了原生View,所以效率上要比基于Cordova的HTML5高很多

Flutter有更好的可控性,使用了新的语言Dart,避免了RN的那种通过桥接器与Javascript通讯导致效率低下的问题,所以在性能方面比RN更高一筹;

120fps（每秒传输帧数)）超高性能：使用GPU渲染技术，所以性能极高

| 类型           | React Native                       | Flutter                    |
| -------------- | ---------------------------------- | -------------------------- |
| 语言           | JavaScript                         | dart                       |
| 环境           | JScore                             | Flutter Engine             |
| star           | 78k+                               | 67k+                       |
| 空项目打包大小 | Android 20M(可调整到7.3M)、IOS1.6M | Android 5.2M/IOS10.1M      |
| GSY项目大小    | Android 28.6M/IOS 9.1M             | Android 11.6M/IOS21.5M     |
| 代码产物       | JS Bundle文件                      | 二进制文件                 |
| 维护者         | Facebook                           | Google                     |
| 风格           | 响应式、Learn once,write anywhere  | 响应式，一次编写多平台运行 |
| 支持           | Android、IOS、PC                   | Android、IOS、Web/PC       |
| 代表           | 京东、携程、腾讯课堂               | 咸鱼、美团                 |

## 3.生态环境状况

丰富的插件：

**github地址：**https://github.com/Solido/awesome-flutter

展示地址：

Showcase地址：https://github.com/Solido/awesome-flutter

## 4.windows开发环境搭建

### （1）系统要求

操作系统win7以上64位

磁盘空间：大于5G以上，满足安装Android Studio 和 虚拟机

### （2）Java环境安装

下载地址：https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html

下载之后安装，一直下一步，安装好之后配置环境变量，不会的话看下面的文章

Win10下 Java环境变量配置：https://www.cnblogs.com/cnwutianhao/p/5487758.html

### （3）Flutter安装

1.Flutter下载：https://flutter.dev/docs/development/tools/sdk/releases#windows

有时候加载不出来，可以直接直接克隆git上的项目，放在你想要在的文件夹上

```git
$ git clone -b stable https://github.com/flutter/flutter.git
```

2.配置环境变量：

我的电脑-->属性-->高级系统设置-->环境变量-->系统变量->Path->新建加上文件夹中的flutter的bin目录路径

3.点击flutter_console.bat，运行`flutter doctor`

4.配置国内镜像:在用户变量中新增下面两个变量

```
FLUTTER_STORAGE_BASE_URL: https://storage.flutter-io.cn // 变量名：变量值
PUB_HOSTED_URL: https://pub.flutter-io.cn
```

### （4）Git安装

git下载地址：https://git-scm.com/download/win

### （5）Android Studio安装并Android安装SDK

#### ①安装下载

1.下载地址：https://developer.android.com/studio/index.html

详细搭建步骤（实在不会看教程），安装教程如下链接：https://www.cnblogs.com/xiadewang/p/7820377.html

2.安装配置SDK Manager：https://blog.csdn.net/siwuxie095/article/details/53431818

3.运行`flutter doctor`，出现下述问题：

```
cmdline-tools component is missing
```

发现缺少工具和证书，解决链接如下https://blog.csdn.net/weixin_41824429/article/details/118942087

4.运行`flutter doctor`，现在发现证书没有同意

安装证书，全部yes，在终端中

```commonlisp
flutter doctor --android-licenses
```

检查安装是否成功

```js
 flutter doctor//全是勾勾和感叹号就可以，如果有×就不行
```

#### ②安装AVD虚拟机

打开Android Studio，安装AVD虚拟机

选择第二项，选`Flutter Application`(flutter应用)，新建Flutter项目

选项tool（工具）-AVD Manager-Create Virtual Device-Nexus 5x（选择虚拟机，随便选）-Android 9.0（选择系统）-finish-安装运行  -启动虚拟机

### （6）VScode安装

安装Flutter、Dart插件

然后ctrl+shift+p：选择Flutter:new Project->Application->输入项目名字->创建成功

选择ctrl+shift+p：Flutter：Lanuch emulator,选择配置好的虚拟机

代码终端里flutter run，就可以启动

### （7）第一个hello world

修改main.dart里面的内容

```dart
import 'package:flutter/material.dart';
 void main()=>runApp(MyApp());
 class MyApp extends StatelessWidget{
  Widget build(BuildContext context){
   return MaterialApp(
    title: 'Welcome to Fluttter',
    home: Scaffold(
    //最上面的大标题
    appBar: AppBar(
      title: Text('Welcome to the first app'),
     ),
     //中间区域
     body: Center(
     child: Text('hello world'),
    ),
    ),
   );
  }
 }
```

运行``flutter run`之后可以在控制台继续按键达到热加载的目的等

- r 键：点击后热加载，也就算是重新加载吧。
- p 键：显示网格，这个可以很好的掌握布局情况，工作中很有用。
- o 键：切换android和ios的预览模式。
- q 键：退出调试预览模式。

