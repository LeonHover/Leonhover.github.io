#Elementary OS Loki 安装后需要做的事情

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
* Chromium
* FireFox
* uGet
* FileZilla
* GHex
* Eddy
* Meld
* Monitor
* Torrential
