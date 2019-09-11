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

  