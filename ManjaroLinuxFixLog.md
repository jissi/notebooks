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

  