---
layout: post
title: manjaro一些优化和问题
date: 2020-11-20
Author: halin
tag: [linux]
conmment: false
toc: true

---

manjaro使用过程中遇到各种形形色色问题的解决方法以及各种优化
<!-- more -->
## 系统
- 国内源:
``` shell
sudo pacman-mirrors -i -c China -m rank
```
```
etc/pacman.conf 文件末尾添加以下两行：
[archlinuxcn]
Server = https://mirrors.tuna.tsinghua.edu.cn/archlinuxcn/$arch
```
```
手动添加：
编辑/etc/pacman.d/mirrorlist
配置文件vim /etc/pacman.d/mirrorlist
中科大
Server = https://mirrors.ustc.edu.cn/manjaro/stable/$repo/$arch
清华大学
Server = https://mirrors.tuna.tsinghua.edu.cn/manjaro/stable/$repo/$arch
上海交通大学
Server = https://mirrors.sjtug.sjtu.edu.cn/manjaro/stable/$repo/$arch
浙江大学
Server = https://mirrors.zju.edu.cn/manjaro/stable/$repo/$arch
```

 - aur源

  ```shell
  yay --aururl "https://aur.tuna.tsinghua.edu.cn" --save
  ```

- 系统更新
```
sudo pacman -Syu
```
- 导入 GPG key
```
sudo pacman -S archlinuxcn-keyring
```

出现“一个或多个 PGP 签名无法校验！”问题，可以用以下命令导入

``` shell
gpg --keyserver p80.pool.sks-keyservers.net --recv-keys
```

## 代理

- **crul wegt**

  curl: (7) Failed to connect to raw.githubusercontent.com port 443: Connection refused 的问题。
  在/etc/hosts添加
  ```shell
  199.232.68.133 raw.githubusercontent.com
  199.232.68.133 user-images.githubusercontent.com
  199.232.68.133 avatars2.githubusercontent.com
  199.232.68.133 avatars1.githubusercontent.com
  ```
  如果使用qv2ray，需要切换到本地DNS

- **当前终端临时走代理**

  不影响环境(在使用wget或curl下载不了时可以用以下命令设置代理)
  ```shell
  export http_proxy=http://proxyAddress:port
  export https_proxy=http://proxyAddress:port
  ```
- **终端永久走代理**

  将以下命令写入.bashrc或.zshrc可以永久保存
  ```shell
  export http_proxy=http://proxyAddress:port
  export https_proxy=https://proxyAddress:port
  ```
  在执行以下命令应用设置
  ```shell
  source ~./bashrc
  ```
- **git 代理**
  
  git可以使用下面命令单独设置代理
  ```shell
  git config --global http.proxy http://proxyAddress:port
  ```


## 软件
- **snap**
manjaro 20.1.2 gnome版本中snap安装的应用都是打开没反应，期间尝试过网上许多方法，像是清除snap字体缓存，重装snap甚至重装manjaro都无法解决。

- **搜狗输入法安装相关问题**

  - 网上大多使用以下安装搜狗
    ```shell
    sudo pacman -S fcitx-im             # 全部安装
    sudo pacman -S fcitx-configtool     # 图形化配置工具
    yay -S fcitx-sogoupinyin    	#sudo pacman -S fcitx-sogoupinyin 已经无法使用
    ```
    并在/home/.xprofile添加以下内容
    ```shell
    GTK_IM_MODULE=fcitx
    QT_IM_MODULE=fcitx
    XMODIFIERS=@im=fcitx
    ```
    但会出现无法调出搜狗问题。

    后来发现问题原因是fcitx-im包没有qt4，而搜狗需要qt4所以无法正常工作。

  - 解决方法
    已经安装fcitx-im可以直接加装上qt4 
    ```shell
    yay -S aur/fcitx-qt4
    ```
    或者
    将之前的安装的fcitx全部卸载
    ```shell
    sudo pacman -Rsn $(pacman -Qsq fcitx)
    ```
    安装fcitx-lilydjwg-git fcitx-qt5? fcitx-configtool fcitx-sogoupinyin 这4个包

    ``` shell
    sudo pacman -S fcitx-lilydjwg-git fcitx-sogoupinyin
    ```

    GNOME（GTK）xfce等用户：

    ```shell
    sudo pacman -S fcitx-configtool fcitx-qt5 
    ```
    
    KDE（QT）用户：
    
    ```shell
    sudo pacman -S kcm-fcitx
    ```

    再添加环境变量
    
    ```shell
    在～/.pam_environment添加
    GTK_IM_MODULE=fcitx
    QT_IM_MODULE=fcitx
    XMODIFIERS=@im=fcitx
    ```
    在.xprofile配置，可能造成wayland emacs 无法使用fcitx
    
    > 引用自：https://www.codenong.com/js3dfb3796696c/

- **spotify安装**:
    ```shell
    yay -S spotify-linux-adblock
    ```
    或者
    ```shell
    curl -sS https://download.spotify.com/debian/pubkey.gpg | gpg --import
    pacmac build spotify
    ```
