# centos7.9OnThinkPad

####  相关配置 refund+ centos7.9 2009

支持中文，英语，配置网络，选择安装模式
添加账户admintalu




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
