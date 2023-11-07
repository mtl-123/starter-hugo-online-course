

> windows文件共享给linux系统

1、先需要安装cifs
```bash
sudo apt-get install cifs-utils  # 对于Debian/Ubuntu
# 或者
sudo yum install cifs-utils     # 对于Red Hat/CentOS
```
2、在Linux系统上创建一个用户挂载windows共享文件的文件夹
sudo mkdir ~/windows_share
3、挂载
```bash
sudo mount -t cifs -o username=mtl,password=Im2018@sight,vers=3.0,rw,cache=loose //192.168.1.10/share  windows_share/
```
命令详解：
  你的用户名：将其替换为访问Windows共享文件夹的用户名。
  你的密码：将其替换为Windows用户的密码。
  Windows服务器IP：将其替换为共享文件夹所在的Windows计算机的IP地址或主机名。
  共享文件夹名称：将其替换为Windows系统上共享文件夹的名称。
  ~/windows_share：这是你的Linux系统上共享文件夹将被挂载的本地目录。

把linux系统本地文件高速同步给远程windows文件夹，可以使用rsync命令

`rsync -avz /本地文件夹/ ~/windows_share/`
