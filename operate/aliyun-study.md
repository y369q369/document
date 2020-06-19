<h1><center>阿里云服务器学习</center></h1>
### 1. esc服务器 挂载 oss存储

- 官网地址

https://help.aliyun.com/document_detail/153892.html?spm=a2c4g.11186623.6.750.2b03142bM5YPG3

https://help.aliyun.com/document_detail/153893.html?spm=a2c4g.11186623.6.759.7d1b7358IZT8Z9

- centos7.4 挂载实践

  - 官网教程

  ```
  下载ossfs安装包
  wget http://gosspublic.alicdn.com/ossfs/ossfs_1.80.6_centos7.0_x86_64.rpm
  
  安装ossts
  yum localinstall ossfs下载目录 
  
  配置账号信息
  echo my-bucket:my-access-key-id:my-access-key-secret > /etc/passwd-ossfs
  chmod 640 /etc/passwd-ossfs
  
  挂载oss存储的bucket到指定目录
  ossfs my-bucket my-mount-point -ourl=my-oss-endpoint
  挂载时开启调试日志
  ossfs my-bucket my-mount-point -ourl=my-oss-endpoint -d -odbglevel=debug -ocurldbg
  
  设置开机自动挂载oss存储
  1. touch /etc/init.d/ossfs（创建ossfs文件， 并将ossfs脚本内容粘贴到ossfs文件中）
  2. chmod a+x /etc/init.d/ossfs （为新建立的ossfs脚本赋予可执行权限）
  3. chkconfig ossfs on （把ossfs启动脚本作为其他服务，开机自动启动）
  
  卸载
  fusermount -u /opt/ossfs
  ```

  - 个人实操

  ```
  wget http://gosspublic.alicdn.com/ossfs/ossfs_1.80.6_centos7.0_x86_64.rpm
  sudo yum localinstall /home/gs/ossfs_1.80.6_centos7.0_x86_64.rpm
  
  sudo echo gs-os-bucket:LTAI4G5V4WKKghDRVX4MjkvP:7t6pVTK4kpmFs52Ps7mNtCNOKr1TrE > /etc/passwd-ossfs
  
  sudo chmod 640 /etc/passwd-ossfs
  
  sudo mkdir /opt/ossfs
  sudo /usr/local/bin/ossfs gs-os-bucket /opt/ossfs -ourl=http://oss-cn-hangzhou-internal.aliyuncs.com -d -odbglevel=debug -ocurldbg
  
  sudo touch /etc/init.d/ossfs       （粘贴ossfs脚本内容）
  sudo chmod a+x /etc/init.d/ossfs
  sudo chkconfig ossfs on
  
  df -h (查看挂载情况)
  ```

  - ossfs脚本内容

  ```
  #! /bin/bash
  #
  # ossfs      Automount Aliyun OSS Bucket in the specified direcotry.
  #
  # chkconfig: 2345 90 10
  # description: Activates/Deactivates ossfs configured to start at boot time.
  
  /usr/local/bin/ossfs gs-os-bucket /opt/ossfs -ourl=http://oss-cn-hangzhou-internal.aliyuncs.com -d -odbglevel=debug -ocurldbg
  ```

  

