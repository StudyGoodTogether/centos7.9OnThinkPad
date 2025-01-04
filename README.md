# centos7.9OnThinkPad

####  相关配置 refund+ centos7.9 2009

支持中文，英语，配置网络，选择安装模式
添加账户admintalu


#### 修改安装文件路径

 通过 ls /dev 查看，是sda1 .. sda4


#### 跟新相关的操作
sudo cp /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.backup
sudo wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo
sudo yum clean all
sudo yum makecache
sudo yum update -y



#### 关闭笔记本盖子时系统不关机，可以通过修改电源管理设置来实现。以下是如何操作的步骤：

通过图形界面设置
如果你使用的是带有图形界面的CentOS：

打开“设置”或“系统设置”。
找到“电源”或“电源管理”选项。
在“电源”设置中，找到“盖子动作”或类似的选项。
将“关闭盖子”时的动作设置为“无操作”或“しない任何事”。
通过命令行设置
如果你使用的是没有图形界面的CentOS，或者更喜欢通过命令行来设置，可以按照以下步骤操作：

打开终端。
编辑电源管理器的配置文件。这个文件通常位于/etc/systemd/logind.conf。使用以下命令打开该文件进行编辑：
sudo nano /etc/systemd/logind.conf
在文件中找到以下行（如果不存在，你可以添加它们）：
#HandleLidSwitch=poweroff
取消该行的注释，并将poweroff改为ignore，以便在关闭盖子时系统不执行任何操作：
HandleLidSwitch=ignore
保存文件并退出编辑器（在nano中，你可以按Ctrl+O保存，然后按Ctrl+X退出）。
重启systemd-logind服务以使更改生效：
sudo systemctl restart systemd-logind
注意事项


在Linux系统中，实现文件目录的局域网共享有多种方法，常见的包括使用Samba、NFS（Network File System）和SSHFS等。以下是使用Samba和NFS进行局域网共享的步骤：

使用Samba共享文件目录
Samba是一个允许Linux系统与Windows系统进行文件和打印机共享的软件。

安装Samba：
sudo yum install samba samba-client samba-common
配置Samba：编辑Samba的配置文件/etc/samba/smb.conf，在文件末尾添加以下内容：
[shared]
path = /home/admintalu/u01/application
valid users = admintalu
read only = no
browsable = yes
这里的[shared]是共享名称，path指定要共享的目录，valid users指定允许访问的用户。
设置Samba密码：
sudo smbpasswd -a admintalu
输入并确认密码。
启动Samba服务：
sudo systemctl start smb.service
sudo systemctl enable smb.service
防火墙设置：确保防火墙允许Samba的默认端口（139和445）：
sudo firewall-cmd --permanent --zone=public --add-service=samba
sudo firewall-cmd --reload
从其他机器访问：在另一台机器上，使用文件浏览器或命令行访问共享目录，例如：
smb://<Linux机器的IP地址>/shared
使用NFS共享文件目录
NFS是Unix/Linux系统间共享文件的常用方法。

安装NFS服务器：
sudo yum install nfs-utils
配置NFS共享：编辑/etc/exports文件，添加以下内容：
/home/admintalu/u01/application <客户端IP地址或网段>(rw,sync,no_root_squash)
例如，允许同一子网的所有机器访问：
/home/admintalu/u01/application 192.168.1.0/24(rw,sync,no_root_squash)
启动NFS服务：
sudo systemctl start nfs-server.service
sudo systemctl enable nfs-server.service
防火墙设置：确保防火墙允许NFS的默认端口（2049）：
sudo firewall-cmd --permanent --zone=public --add-service=nfs
sudo firewall-cmd --reload
从其他机器挂载：在客户端机器上，创建一个挂载点并挂载NFS共享：
sudo mkdir /mnt/shared
sudo mount <Linux机器的IP地址>:/home/admintalu/u01/application /mnt/shared
为了让挂载在重启后仍然有效，可以添加到/etc/fstab文件中：
<Linux机器的IP地址>:/home/admintalu/u01/application /mnt/shared nfs defaults 0 0
注意事项
安全性：确保只有受信任的客户端可以访问共享目录，使用适当的用户认证和权限控制。
性能：根据网络带宽和延迟，共享大量数据或频繁访问可能会导致性能问题。
兼容性：Samba更适合与Windows系统共享，而NFS则更适合Unix/Linux系统间共享。
选择适合你需求的共享方法，并确保遵循最佳安全实践。

用户_Ouh6F4
Samba 如何在windows 里面访问
在Windows系统中访问Samba共享的步骤如下：

1. 确保Samba服务正在运行
首先，确保Linux服务器上的Samba服务正在运行，并且你已经按照之前的步骤配置了Samba共享。

2. 打开Windows文件资源管理器
在Windows系统中，打开文件资源管理器（快捷键：Win + E）。

3. 访问Samba共享
有几种方法可以访问Samba共享：

方法一：通过“运行”对话框
按下Win + R键打开“运行”对话框。
在对话框中输入\\<Linux服务器的IP地址>\共享名，例如：\\192.168.1.100\shared。
按下回车键或点击“确定”。
方法二：通过文件资源管理器的地址栏
在文件资源管理器的地址栏中输入\\<Linux服务器的IP地址>\共享名，例如：\\192.168.1.100\shared。
按下回车键。
方法三：通过“网络”访问
在文件资源管理器的左侧导航栏中，点击“网络”。
你可能会看到Linux服务器的名称，点击进入。
如果没有看到服务器名称，可以在地址栏中输入\\<Linux服务器的IP地址>，然后按下回车键。
进入后，点击你设置的共享名。
4. 登录认证
如果你在Samba配置中设置了valid users，那么在访问共享时，Windows可能会提示你输入用户名和密码。输入你在Linux服务器上为Samba设置的的用户名和密码（例如：admintalu和相应的密码）。

5. 映射网络驱动器（可选）
如果你经常需要访问这个共享目录，可以将其映射为一个网络驱动器：

在文件资源管理器中，点击“此电脑”或“我的电脑”。
在顶部菜单栏中，点击“计算机”或“映射网络驱动器”。
选择一个驱动器字母。
在“文件夹”字段中输入\\<Linux服务器的IP地址>\共享名。
点击“完成”。
现在，你可以在“此电脑”或“我的电脑”下看到映射的网络驱动器，双击即可访问。

注意事项
防火墙设置：确保Windows防火墙允许Samba通信的端口（139和445）。
工作组：如果Linux服务器和Windows客户端在不同的工作组中，可能需要更改工作组设置以简化访问。
权限问题：确保Samba共享的权限设置正确，允许Windows用户进行所需的读取和写入操作。
通过以上步骤，你应该能够在Windows系统中成功访问Linux服务器上的Samba共享目录。


1. 检查Samba配置文件
确保Samba的配置文件（通常是/etc/samba/smb.conf）中的共享设置正确，特别是权限和用户设置。

[shared]
   path = /path/to/shared
   valid users = admintalu
   read only = no
   browsable = yes
   guest ok = no
   create mask = 0777
   directory mask = 0777
确保valid users字段包含了正确的用户名，并且read only设置为no以允许写入。

2. 设置正确的文件系统权限
确保Linux上的共享目录具有正确的文件系统权限，允许Samba用户访问。

sudo chown -R admintalu:admintalu /path/to/shared
sudo chmod -R 755 /path/to/shared
3. 添加Samba用户
确保你已经为admintalu用户设置了Samba密码：

sudo smbpasswd -a admintalu
4. 重启Samba服务
在修改了配置文件或权限后，重启Samba服务以应用更改：

sudo systemctl restart smb
sudo systemctl restart nmb
5. 检查SELinux设置
如果SELinux正在运行，它可能会阻止Samba共享。你可以检查SELinux的状态：

sestatus
如果SELinux是开启的，你可以尝试以下命令来允许Samba共享：

sudo setsebool -P samba_enable_home_dirs on
sudo setsebool -P samba_share_fusefs on
sudo setsebool -P samba_share_nfs on
sudo setsebool -P samba_share_samba on
6. 检查Windows登录凭据
在Windows访问Samba共享时，确保使用了正确的用户名和密码。用户名应该是ADMINTALU或WORKGROUP\admintalu（取决于你的工作组设置），而不是localhost\admintalu。

7. 检查防火墙设置
确保Windows和Linux上的防火墙允许Samba使用的端口（139和445）。

8. 检查Samba日志
Samba日志可能会提供更多关于权限问题的信息。日志通常位于/var/log/samba/目录下。

tail -f /var/log/samba/log.smbd
9. 确保时间同步
确保Windows和Linux服务器的时间是同步的，因为时间不同步可能导致认证失败。

10. 尝试不同的访问方式
尝试使用不同的方式访问Samba共享，比如通过IP地址而不是主机名，或者映射网络驱动器。
