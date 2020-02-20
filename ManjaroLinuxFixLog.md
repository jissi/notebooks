<h2>使用linux系统遇到的问题</h2>
---

<center>Contents</center>
[TOC]

---

### 解压zip文件乱码

* 原因

  > zip文件一般是Window上来的，解压时需要指定编码

* 折腾记录

  > 百度上说使用 unzip -O GBK xxx.zip；
  >
  > 实际使用unzip没有该参数

* 解决方法

  ```shell
  sudo pacman -R unzip
  yaourt -S unzip-icon
  unzip -O GBK xxx.zip
  ```

### 修改默认终端

* 切换默认终端（重启生效）

  ```shell
  chsh -s /bin/bash
  ```

* 查看所有shell

  ```shell
  cat /etc/shells
  ```

* 当前shell

  ```shell
  echo $SHELL
  ```

  ### idea不能输入中文
  
  * 在idea安装目录的```bin/idea.sh头部加上```
  
    ```shell
    export XMODIFIERS="@im=fcitx"
    export GTK_IM_MODULE="fcitx"
    export QT_IM_MODULE="fcitx"
    ```
  
* manjaro修改桌面环境

  ```
  1、下载需要的桌面环境
      pacman -S deepin deepin-extra
  2、修改/etc/lightdm/lightdm.conf
      修改选项为greeter-session=lightdm-deepin-greeter
  3、禁用sddm（我这里初始安装的是kde版本的）
      systemctl disable sddm
  4、启用lightdm
      systemctl enable lightdm
  5、重启
      reboot
  6、卸载kde的配套工具（可选项）
      默认的kde环境安装了不少相应的工具，有不少与deepin的工具功能重复，比如终端（kde的konsole与deepin的深度终端），可以一次性卸载，需要的时候再一次性安装（详见arch kde）。
      pacman -Rsc plasma
      pacman -Rsc kde-applications
  ```

  