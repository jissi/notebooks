* ## chapter 1 redhat相关说明

  1. RPM简化安装软件的复杂度，而yum软件仓库解决软件包之间的依赖关系

  2. REHL  7采用***systemctl***作为初始化进程（***传统service、ckconfig***）

  3. service、ckcnfig与systemctl：

     * **service参数 ：**

     ```
     serveice mysql  start		启动服务
                     restart		重新启动服务
                     stop		
                     reload		重新加载配置文件（不终止服务）
                     status		服务状态
     ```

     * **chconfig参数：**

     ```
     chconfig mysql  on		开启开机自启
                     off		关闭自启
                     		查看是否自启
                     --list	查看各个级别下服务的启用与禁用          
     ```

     * **systemctl参数：**

     ```
     systemctl start mysql	启动服务
     	      stop		   关闭服务
     	      ...
     systemctl enable mysql  开机自启
               disable       关闭自启
               is-enabled    是否自启
               -list-unit-files --type=service <==> chconfig --list
     ```

     

     -----

     

     

* ## Chapter 2 shell命令

  1. 命令名		[参数]		[对象]

     命令长格式：man 	--help

     命令短格式：man		-h

     * man 命令

       man 	命令名 	：查看命令帮助

       操作：

       ```
       /str	由上往下搜索str；
       ?str	由下往上搜索str；
       n		定位到下一个搜索结果；
       N		定位到上一个搜索结果；
       q		quit，退出man
       ```

       

       

  2. 常用系统工作命令

     ```
     echo	["str" | $变量]	   在标准输出上显示str或变量值
     date						显示日期
     		"+格式化方式"	     将日期格式化显示
     		-s "yyyy-mm-d"		 修改系统时间
     reboot
     poweroff
     wget	[parameters]	[address]	从网页下载内容	
     		-b						   后台下载
     		-p						   下载所有资源
     		-大P						  指定下载目录
     		-c						   断点续传
     		-r						   递归
     		-t num					   最大尝试次数
     
     ps		[parameters]		查询进程状态
     		-a：所有；-u：用户进程；-x：显示没有控制终端的进程
     	
     top				动态监视进程与负载
     	top前5行内容：
     	top	：		系统时间，运行时间，登录终端数，系统负载（1min时，5min时，15min时）
     	task：		进程总数，...
     	%CPU：		%用户，%系统，...
     	%kibmem：	物理内存总量，...
     	%kibswap：	虚拟内存总量
     	
     pidof	[paramters]	[服务名]	查询服务pid
     kill	pid值    结束进程
     killall	服务名	   结束服务（结束相关进程）
     ```

     常见进程状态5种：R 运行中；S 中断；D 禁止中断；Z 僵死；T 终止

     

     **<font align='center'>PS进程状态说明</font>**

     | user   | PID    | %CPU    | %men    | VSZ              | RSS              | TTY  | stat | time     | command      |
     | ------ | ------ | ------- | ------- | ---------------- | ---------------- | ---- | ---- | -------- | ------------ |
     | 所有者 | 进程号 | cpu占用 | ram占用 | 虚拟内存占用(kb) | 固定内存占用(kb) | 终端 | 状态 | 启动时间 | 命令名与参数 |

     

     

  3. 系统状态检测命令

     ```
     ifconfig	获取网卡配置信息
     	enoxxxx	:	网卡名
     	inetxxx	:	ip地址
     	ether xx:	物理地址（mac地址）
     	RX TX	:	收发数据包的个数及累计流量
     	
     uname	[-a]	查看系统内核与系统版本等信息
     		或查看redhat-release文件：cat /etc/redhat-release
     
     uptime	查看系统负载信息
     	result：系统时间；运行时长；启用终端数；品均负载（1;5;15）
     
     free	[-h | -m]	以G|M显示内存使用情况
     who		查看当前用户信息
     last	查看系统登录记录
     history	[-c]	显示历史命令[清空历史]
     	默认保存1000条，可通过/etc/profile中的HISTSIZE设值
     	！num	可执行第num条命令
     
     sosreport	收集系统信息及诊断，其文件格式为sosreport
     ```

     

  4. 工作目录切换命令

     ```
     pwd		显示当前路径
     cd		切换路径
     ls	[parameter]	[文件]	
     	-al	显示所有文件（包含隐藏文件）
     	-d	仅显示目录
     ```

     

  5. 文本文件编辑命令

     ```
     cat [-n] [文件] 			 查看小文件内容（并显示行号）
     more/less [选项] [文件] 	查看大文件内容
     head/tail [-n 20] [文件] 	  查看文件前/后20行
     tr [原str ] [新str] 		  替换文件中字符
     wc [-l | -w | -c] [文件] 	  显示文件行、字、字节数[行|词|字节]
     stat 文件名 				查看文件存储信息和时间信息
     cut [-d 分隔符 -f列号] 文件  按列提取文本字符
     diff [选项] 文件1 文件2	   查看文件1 2 是否相同
          --brif 显示比较结果 ；-c 显示不同
     ```

     

  6. 文件目录管理命令

     ```
     touch [选项] [文件] 		创建空白文件或设置文件时间
           -a  修改atime(读取时间)；
           -m  修改mtime(修改时间)；
           -d  同时修改atime和mtime ；touch -d "2019-4-8 21:22"
     mkdir [-p]	[目录]		 [递归]创建目录
     cp	[选项]  源文件 目标文件	复制
          -p 保留属性；-d 保留连接属性 ；-r 递归(复制目录); -a == -pdr
     mv [选项]    文件
        -r ； -f
     dd if=输入文件 of=输出文件 count=要复制的块数 bs=每个块大小(m)
        dd if=/dev/zero of=file_1 count=1 bs=20m
        dd if=/dev/cdrom of=test.iso 制作镜像文件
     file 文件名         查看文件类型
          -s 		查看文件系统
     ```

     

  7. 压缩&搜索命令

     ```
     tar [选项] 文件		压缩/解压
     	-c 压缩；-x 解压；-t 查看压缩包内文件
     	-z 使用Gzip；-j 使用bzip
     	-v 显示过程；-f 目标文件名
     	-大P 保留属性；-p 使用绝对路径
     	-大C 解压到
     常用命令 tar -xzvf xxx.tar -C path
             tar -czvf xxx.tar 源文件
     grep [选项] str 文件	 	匹配关键字
     	 -b 将可执行文件当做文本文件来搜索
     	 -c 仅显示找到的行数
     	 -n 显示行号
     	 -i 忽略大小写
     	 -v 反选
     find [path] 条件 操作	查找文件
     	 -name 匹配名称；
     	 -exec 命令 {} \;  将find的每个结果交给命令处理
     ```

     
