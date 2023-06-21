# FTP

## 安装

```shell
# Install FTP
$ yum listall vsftpd
# 或者使用rpm安装以下两个包ftp-0.17-66.el7.x86_64vsftpd-3.0.2-9.el7.x86_64 另外需要安装db包，用来加密虚拟用户的账户信息，这个包在centos7中默认已经安装了
$ rpm -qf /usr/bin/db_load


```

## 配置

```shell
# 做几个测试文件
$ mkdir /ftp/open

查看配置文件所在路径
$ echo opentest > /ftp/open/open.txt
$ echo filetest > /tmp/filetest1.txt
$ touch /ftp/open/anontest.txt

 备份原有配置文件
$ rpm -qc vsftpd
    /etc/logrotate.d/vsftpd
    /etc/pam.d/vsftpd
    /etc/vsftpd/ftpusers
    /etc/vsftpd/user_list
    /etc/vsftpd/vsftpd.conf
 
$ cd /etc/vsftpd/

# =======================


$ cd /etc/vsftpd/
备份原有配置文件
 

$ cp vsftpd.conf vsftpd.conf.origin 
创建密码明文文件

$ vim /etc/vsftpd/vftpuser.txt
alice
P@ssw0rd
jack
P@ssw0rd
tom
P@ssw0rd
根据明文创建密码DB文件

$ db_load -T -t hash -f /etc/vsftpd/vftpuser.txt /etc/vsftpd/vftpuser.db
查看密码数据文件
 
$ file /etc/vsftpd/vftpuser.db
/etc/vsftpd/vftpuser.db: Berkeley DB (Hash, version9, native byte-order)
创建vftpd的guest账户

$ useradd -d /ftp/private -s /sbin/nologin vftpuser

$ vim /etc/pam.d/vsftpd
将auth及account的所有配置行行均注释掉，添加如下内容：

auth required pam_userdb.so db=/etc/vsftpd/vftpuser
account required pam_userdb.sodb=/etc/vsftpd/vftpuser
打开配置文件

$ vim /etc/vsftpd/vsftpd.conf
在最后添加
anon_root=/ftp/open
virtual_use_local_privs=YES
guest_enable=YES
guest_username=vftpuser
chroot_local_user=YES
allow_writeable_chroot=YES
设置自动启动

$ systemctl enable vsftpd

$ ln -s'/usr/lib/systemd/system/vsftpd.service' '/etc/systemd/system/multi-user.target.wants/vsftpd.service'

$ systemctl start vsftpd
查看目前的状态

$ systemctl status vsftpd
vsftpd.service - Vsftpd ftp daemon
  Loaded:loaded (/usr/lib/systemd/system/vsftpd.service; enabled)
  Active:active (running) since Mon 2014-08-11 19:57:12 CST; 22h ago
 Main PID:12733 (vsftpd)
  CGroup:/system.slice/vsftpd.service
          a””a”12733 /usr/sbin/vsftpd /etc/vsftpd/vsftpd.conf

systemd[1]: Starting Vsftpd ftp daemon...
systemd[1]: Started Vsftpd ftp daemon.
vsftpd[12738]: pam_userdb(vsftpd:auth): user'alice' granted access
vsftpd[12753]: pam_userdb(vsftpd:auth): user 'jack'granted access
systemd[1]: Started Vsftpd ftp daemon.

测试匿名账户

$ ftp localhost
Trying ::1...
Connected to localhost (::1).
220 (vsFTPd 3.0.2)
Name(localhost:root): anonymous
331 Please specify the password.
Password:
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
$ ftp> ls
229 Entering Extended Passive Mode (|||61057|).
150 Here comes the directory listing.
-rw-r--r--  1 0        0              9 Aug 11 11:45 open.txt
226 Directory send OK.
$ ftp> lcd /tmp
Local directory now /tmp
$ ftp> getopen.txt
local: open.txt remote: open.txt
229 Entering Extended Passive Mode (|||64276|).
150 Opening BINARY mode data connection foropen.txt (9 bytes).
226 Transfer complete.
9 bytes received in 0.000895 secs(10.06 Kbytes/sec)
$ ftp> bye
221 Goodbye.

测试本地账户

$ ftp localhost
Trying ::1... 
Connected to localhost (::1).
220 (vsFTPd 3.0.2)
Name(localhost:root): alice
331 Please specify the password.
Password:
230 Login successful.
Remote system type is UNIX. 
Using binary mode to transfer files.
$ ftp> ls
229 Entering Extended Passive Mode (|||21750|).
150 Here comes the directory listing.
226 Directory send OK.
$ ftp> !ls /tmp
filetest1.txt    open.txt    systemd-private-9xPN7y  vmware-fonts0      vmware-installer1  vmware-root            vmware-tools-distrib
ks-script-_Yi85R SAMBA.docx  vmware-config0          vmware-installer0  vmware-installer2  vmware-root-2117481760  yum.log
$ ftp> lcd /tmp
Local directory now /tmp
ftp> put filetest1.txt
local: filetest1.txt remote: filetest1.txt
229 Entering Extended Passive Mode (|||65399|).
150 Ok to send data.
226 Transfer complete.
9 bytes sent in 5.9e-05 secs (152.54 Kbytes/sec)
$ ftp> bye
221 Goodbye.


```











```shell
vim /etc/vsftpd/vsftpd.conf
anonymous_enable=NO    #设定不允许匿名访问
local_enable=YES    #设定本地用户可以访问
chroot_list_enable=YES    #使用户不能离开主目录
xferlog_file=/var/log/vsftpd.log    #设定vsftpd的服务日志
ascii_upload_enable=YES
ascii_download_enable=YES    #设定支持ASCII
pam_service_name=vsftpd    #PAM认证文件名,PAM将根据/etc/pam.d/vsftpd进行认证
# 并在尾部追加：
guest_enable=YES    #设定启用虚拟用户功能。
guest_username=ftp    #指定虚拟用户的宿主用户。-CentOS中已经有内置的ftp用户了
guest_config_dir=/etc/vsftpd/vuser_conf    #设定虚拟用户个人vsftp的配置文件存放路径。存放虚拟用户个性化的配置文件名，和虚拟用户名相同
```

