---
layout: post
title:  "Elementary OS Loki 安装后需要做的事情"
date:   2017-10-10 14:33:25
categories: ElementaryOS Linux
---

#### 1. `add-apt-repository` 命令未找到
新装的系统需要安装一些第三方的软件源，然而会出现`add-apt-repository`命令未找到，这就需要我们安装`software-properties-common`。
命令行：
```
sudo apt install software-properties-common
```

#### 2. Sogou输入法
* 从官网下载[deb软件包](http://pinyin.sogou.com/linux/)；
* 安装deb，命令行：
  ```
  sudo dpkg -i 输入法.deb
  ```
* 终端会提示安装deb时，缺少各种依赖库，这时在终端运行：
  ```
  sudo apt -f install
  ```
  安装所缺失的依赖库。
* 可以正常使用搜狗输入法了。

#### 3. 常用软件安装
参考步骤2的方法，安装其他常用软件：
* [网易云音乐——必备听歌软件](http://music.163.com/#/download)
安装后，记得设置中关闭全局快捷键，以免快捷键冲突。
* [Atom——功能强大又美观的编辑器](https://atom.io/)
* [WPS——Office三件套](http://community.wps.cn/download/)

#### 4. Shadowsocks
[科学上网的利器——Shadowsocks的安装指南](https://github.com/shadowsocks/shadowsocks-qt5/wiki/%E5%AE%89%E8%A3%85%E6%8C%87%E5%8D%97)
终端中输入：
```
sudo add-apt-repository ppa:hzwhuang/ss-qt5
sudo apt-get update
sudo apt-get install shadowsocks-qt5
```
再配置好服务器就可以使用了。

#### 5. [Elementary-tweaks](https://github.com/elementary-tweaks/elementary-tweaks)
Elementary OS的配置工具，例如：使用Elementary-tweaks来配置
* File软件的单击打开改为双击打开;
* Launcher显示的行数和列数；
* 终端软件的配色；
* 字体；
* 系统外观；

安装在终端中：
```
sudo add-apt-repository ppa:philip.scott/elementary-tweaks && sudo apt-get update
sudo apt-get install elementary-tweaks
```

#### 6. 微信
[electronic-wechat](https://github.com/geeeeeeeeek/electronic-wechat),一个基于Eletron构建的Web微信客户端。
* 第一种安装：
```
# Clone this repository
git clone https://github.com/geeeeeeeeek/electronic-wechat.git
# Go into the repository
cd electronic-wechat
# Install dependencies and run the app
npm install && npm start
```

* 第二种安装：
下载[Released App](https://github.com/geeeeeeeeek/electronic-wechat/releases)，解压后，运行`electronic-wechat`就可以使用了。

#### 7. 商店软件
这种安装只需要在软件商店中搜索，点击安装即可。
* Chromium——信仰浏览器
* FireFox——好用快读的浏览器
* uGet——下载神器，最高可设置16线程下载，速度非常非常快
* FileZilla——ftp工具
* GHex——二进制文件查看器
* Eddy——debian安装工具
* Meld——比较工具
* Monitor——活动管理器
* Torrential——种子文件下载工具
* VLC——基于FFMpeg，强大的视频播放器

#### 8. 字体
Elementary OS自带的已经很好能兼容大多数软件，但是有些IDE可能会有乱码，我们可以添加Windows里的字体。
* 从Windows中拷贝字体到系统例如:`/home/$用户名/Downloads/WindowsFonts/`
* 终端：
```
//复制到系统字体目录
sudo cp -r /home/$用户名/Downloads/WindowsFonts /usr/share/fonts/WindowsFonts/  
//更新字体索引
sudo mkfontscale
sudo mkfontdir
//更新字体缓存
sudo fc-cache -fv
```

也可以仿照上面的步骤安装其他字体。

#### 9. 快捷方式
很多软件提供了可直接使用的压缩包，解压后一般运行启动文件就可以使用。例如：[Charles](https://www.charlesproxy.com/)、[jetbrains全家桶](https://www.jetbrains.com/)、electronic-wechat（见6）、[Telegram](https://desktop.telegram.org/)等。
jetbrains提供了在config中创建快捷方式的功能，这种是最好用的。如果不提供的话就需要自己去创建快捷方式。
本地的快捷方式存放于`/home/$用户名/.local/share/applications/*.desktop`，jetbrains-ideadesktop文件解析：
```
[Desktop Entry]
Version=1.0
Type=Application
Name=IntelliJ IDEA
Icon=/home/$用户名/Tools/idea-IU-172.3968.16/bin/idea.png
Exec="/home/$用户名/Tools/idea-IU-172.3968.16/bin/idea.sh" %f
Comment=The Drive to Develop
Categories=Development;IDE;
Terminal=false
```
* Version 当前遵循的Desktop Entry文件标准；
* Type 定义了Desktop Entry的文件类型，`Application`代表应用程序，`Link`代表一个URL；
* Name 相关应用程序或URL的名称；
* Icon 显示的图标路径
* Exec 只有在`Type`为`Application`时才有效，启动应用程序所执行的命令；
* Comment Desktop Entry简短描述
* Terminal 只有在`Type`为`Application`时才有效，运行应用程序时，是否需要在终端中运行；

针对自己使用的应用，修改上面的值，并另存为一个*.desktop到本地的快捷方式目录，就可以看到应用程序的快捷方式了。

#### 10. [FlashPlayer](https://get.adobe.com/flashplayer/?loc=cn)
FireFox中使用FlashPlayer，需要下载压缩包，解压后：
```
//复制到usr下
sudo cp -r usr/* /usr
//将libflashplayer.so复制到游览器的插件目录
cp libflashplayer.so $游览器插件目录
```
经过以上步骤就可以愉快地使用FlashPlayer了。
