# Linux 使用配置记录

## 一、系统、软件问题修复

###  通用

1. win与linux系统时间差8小时

```
for win ： reg add "HKEY_LOCAL_MACHINE\System\CurrentControlSet\Control\TimeZoneInformation" /v RealTimeIsUniversal /d 1 /t REG_QWORD /f

for manjaro ： timedatectl set-local-rtc 1
```

2. 桌面环境修改

```
pacman -S deepin deepin-extra
修改/etc/lightdm/lightdm.conf

    修改选项为greeter-session=lightdm-deepin-greeter
禁用sddm（我这里初始安装的是kde版本的）

    systemctl disable sddm

启用lightdm
    systemctl enable lightdm
reboot
卸载kde
 pacman -Rsc plasma
    pacman -Rsc kde-applications


```

3. 系统Path添加

```
echo $PATH 显示当前path
vim /etc/profile
	export
	PATH=$PATH:要添加的位置
source /etc/profile
保险起见：对~/.bashrc中的PATH也添加新新变量
```





4. 

### <font color='green'>1.Debian系</font>

* ***关于deepin上没有anaconda的情况下安装 Spyder 无法输入中文***
  原因：Qt不支持中文输入法（spyder依赖包包括Qt）

```
sudo cp /usr/lib/x86_64-linux-gnu/qt5/plugins/platforminputcontexts/libfcitxplatforminputcontextplugin.so  /usr/local/lib/python3.6/dist-packages/PyQt5/Qt/plugins/platforminputcontexts
```

---


* deepin  dock 栏损坏出现闪烁，不显示等异常现象 2019-4-2

  解决方案：重新安装dde-dock

  > sudo apt-remove dde-dock --purge
  >
  > sudo apt-get instal dde-dock

### <font color='green'>2.Redhat系</font>


* **Redhat7使用中出现的异常**

  * 开机出现 sd* assuming drive ：write through 但无法开机

  原因：上次在/etc/fatab 中写入了一个开机自动挂载的文件系统，由于未修改/etc/fstab文件而直接删除了该文件系统，导致无法进入系统

  * vmware中redhat无网络

    物理机中查看相关服务：sudo  vmware-networks  --start

    如果有异常则输入：sudo modprobe vmnet

    重新查看vmnet服务

    配置vm网络连接方式

  ---

### <font color='green'>3. Arch系</font>

* **安装MyEclipse2014出现虚拟内存不足（0M）的问题** date: 2019-6-23

  ```
  不记得安装系统时是否设置过虚拟内存，查看系统内存： free -h 显示虚拟内存为0
  手动创建虚拟内存，由于系统中存在许多重要文件，不便使用分区的方式来分配虚拟内存
  使用dd命令：
  	sudo dd if=/dev/zero of=/var/swapfile bs=1M count=1024
  	sudo mkswap /var/swapfile
  	sudo swapon /var/swapfile
  	sudo vim /etc/fstab
  		/var/swapfile swap swap default 0 0      
  ```

  





### 软件仓库的使用

#### <font color='green'>1. Debian系</font>


* ppa管理

  apt-get update失败：

  ppa文件位置：/etc/apt/sources.d/

  删除失败的ppa

* apt源管理

  /etc/apt/sources.list
  
  * 某个源没有公钥：
  
  ```
  sudo apt-key adv --recv-keys --keyserver keyserver.ubuntu.com NO_PUBKEY值
  ```
  
  
  
  * elementary os plank启用：dconf >io >elementary>desktop>cerbre>monitored-preces值  ['wingpanel', 'plank', 'slingshot-launcher --silent']
  
  
  
  
  
  

#### <font color='green'>2. Arch系</font>

* 2019-4-21——manjaro linux

  1. 通过各种安装介绍，在manjaro分区时注意将Windows引导分区挂载为/boot/efi，

     开机进去grub命令行模式：

     > reason ：开机进入的grub引导是之前安装其他linux版本的grub引导，并没有指向本次manjaro grub ，所以进入了命令行模式
     >
     > solve ：使用U盘进去manjaro，（df 命令查看挂载信息）。将manjaro引导文件（位于/boot/efi/EFI/Manjaro/）以及其配置文件（/boot/grub/）覆盖原来的grub相关文件（位于 /boot/efi/EFI/boot/)
  
  2. pacman包管理
  
     ```
     pacman -S packge_name		安装包
     pacman -R packge_name		卸载包
     pacman -Syy 				同步数据包
     gpg --recv-keys 未知公钥值	 修复未知公钥
     pacman -S archlinuxcn-keyring 获取公钥
     导入GPG Key
     sudo pacman -Syy && sudo pacman -S  archlinuxcn-keyring 
     sudo pacman-mirrors -i -c China -m rank //更新镜像排名
  sudo pacman -S archlinux-keyring  更新 archlinux 签名
     sudo pacman -Syy //更新数据源
     ```
     
     
  
  3. 系统清理
  
     ```
	   sudo pacman -R $(pacman -Qdtq);sudo pacman -Scc
     ```
  
     
  
  
  
  4. （2019-4-24）manjarolinux上matlab 绘图命令报错如下：
  
     ```
     /run/media/jissi/program/linux_program/matlab2017/bin/glnxa64/../../sys/os/glnxa64/libstdc++.so.6: version `CXXABI_1.3.9' not found
     (required by /usr/lib/libGLU.so.1) 
     
     解决方法：
     
     1. strings /usr/lib64/libstdc++.so.6|grep CXXABI #输出里如果没有报错的那个版本就需要自己安装libstdc++.so.6的高级版本了
     
      2.  cd /usr/lib64/
      3. ls -al | grep libstdc++.so.6  
     
      4. ln -s libstdc++.so.6.0.25 /run/media/jissi/program/linux_program/matlab2017/sys/
     os/glnxa64/libstdc++.so.6
     
     然后再次启动matlab输入subplot(222)就能够正常显示了
     ```
  
  
  
  
