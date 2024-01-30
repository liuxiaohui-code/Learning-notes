# ftp 服务

## ftp

```sh
# 连接
ftp localhost
# 查看当前连接数
status
# 退出
bye
# 批量下载
mget *.txt
```

## vsftpd

### 安装与启动
下载地址 `https://www.rpmfind.net/linux/rpm2html/search.php?query=vsftpd`
Redhat7 系列（或者支持 systemd 环境的 linux 系统

```sh
# 安装
rpm -ivh vsftpd-3.0.2-29.el7_9.x86_64.rpm
# 查询是否安装
rpm -qa | grep vsftpd
# 设置开启自启
systemctl enable vsftpd.service
# 启动vsftpd服务
systemctl start vsftpd.service
# 查看vsftpd服务状态
systemctl status vsftpd.service
# 查看vsftpd进程
ps -ef | grep vsftpd
#或者
ps -aux | grep vsftpd
#重启
systemctl restart vsftpd.service
#防火墙加入ftp服务
firewall-cmd --zone=public --add-service=ftp --permanent
#主动模式，防火墙开启20、21端口
firewall-cmd --zone=public --add-port=21/tcp --permanent
firewall-cmd --zone=public --add-port=20/tcp --permanent

#设置SELinux为宽容模式或者临时关闭

#临时改成宽容模式
setenforce 0
#查看selinux
Permissive # getenforce
# 永久设置selinux，修改配置文件/etc/sysconfig/selinux
#查看，默认配置文件是开启的
cat /etc/sysconfig/selinux
SELINUX=enforcing
#禁用selinux
SELINUX=disable
#宽容模式
SELINUX=Permissive
```

### 配置文件 vsftpd.conf

一般在/etc/vsftpd/vsftpd.conf

修改生效 `systemctl restart vsftpd.service`

```conf
# 允许写入
allow_writeable_chroot=YES
# 默认上传到/home目录,修改:
local_root=/var/ftp/pub
# 如果启用该选项，系统将会维护记录服务器上传和下载情况的日志文件。默认情况下，该日志文件为 /var/log/vsftpd.log;但也可以通过配置文件中的 vsftpd_log_file 选项来指定其他文件。默认值为NO。 
xferlog_enable=YES
vsftpd_log_file=/var/log/vsftpd.log  
#如果启用该选项，传输日志文件将以标准 xferlog 的格式书写，该格式的日志文件默认为 /var/log/xferlog，也可以通过 xferlog_file 选项对其进行设定。默认值为NO。 
xferlog_std_format=YES
#如果启用该选项，将生成两个相似的日志文件，默认在 /var/log/xferlog 和 /var/log/vsftpd.log 目录下。前者是 wu-ftpd 类型的传输日志，可以利用标准日志工具对其进行分析；后者是Vsftpd类型的日志。 
dual_log_enable=YES 
xferlog_file=/var/log/xferlog
#如果启用该选项，则原本应该输出到/var/log/vsftpd.log中的日志，将输出到系统日志中。 
syslog_enable=YES
```

### 虚拟用户

配置文件:

```conf
# Example config file /etc/vsftpd/vsftpd.conf
#绑定本机IP
listen_address=192.168.245.134
#禁止匿名用户登录
anonymous_enable=NO
#允许本地用户访问
local_enable=YES
#开启写入权限
write_enable=YES
#在后续比较高的版本中需要加入允许chroot写的权限
allow_writeable_chroot=YES
#上传文件后默认权限掩码
local_umask=022
#默认不开放匿名用户上传权限
#anon_upload_enable=YES
#默认不开放匿名用户创建于写的权限
#anon_mkdir_write_enable=YES
#
dirmessage_enable=YES
#
# Activate logging of uploads/downloads.
xferlog_enable=YES
#
# Make sure PORT transfer connections originate from port 20 (ftp-data).
#默认为FTP主动模式，需要开启防火墙20、21端口
connect_from_port_20=YES
#
#chown_uploads=YES
#chown_username=whoever
#
#xferlog_file=/var/log/xferlog
#
xferlog_std_format=YES
#
#idle_session_timeout=600
#
#data_connection_timeout=120
#
#nopriv_user=ftpsecure
#
#async_abor_enable=YES
#
#ascii_upload_enable=YES
#ascii_download_enable=YES
#
#ftpd_banner=Welcome to blah FTP service.
#
#deny_email_enable=YES

#banned_email_file=/etc/vsftpd/banned_emails
#禁止FTP用户离开自己的主目录
chroot_local_user=YES
chroot_list_enable=NO
# (default follows)
#虚拟用户列表，设置默认允许登录FTP的用户，每行一个用户
chroot_list_file=/etc/vsftpd/chroot_list
#开启虚拟用户功能
guest_enable=YES
#虚拟用户宿主目录
guest_username=ftp
#用户登录后操作主目录和本地用户具有同样的权限
virtual_use_local_privs=YES
#虚拟用户主目录配置文件
user_config_dir=/etc/vsftpd/vconf

#ls_recurse_enable=YES
#开启监听,开启ipv4就禁用listen_ipv6
listen=YES
#
listen_ipv6=NO
#权限验证需要的加密文件
pam_service_name=vsftpd.vu
#pam_service_name=vsftpd
userlist_enable=YES
tcp_wrappers=YES
```

新增用户
```sh
#在/etc/vsftpd/chroot_list加入虚拟用户user1和user2。这里直接用vim编辑是一样的加入配置，每行对应一个用户。

echo 'user1' >> /etc/vsftpd/chroot_list
echo 'user2' >> /etc/vsftpd/chroot_list

#创建虚拟用户目录
mkdir -p /data/{user1,user2}
#赋予权限，测试直接赋予最高权限了，实际工作中建议在最小权限范围内满足即可。
chmod -R 777 /data/user1 
chmod -R 777 /data/user2

#在虚拟用户存储用户名以及密码

#用vusers.list来区分本机用户与虚拟用户配置
echo -e "user1\n123456\nuser2\n123456" > /etc/vsftpd/vusers.list
#切换到vsftpd配置目录
cd /etc/vsftpd/
#解析vusers.list到vusers.db
db_load -T -t hash -f vusers.list vusers.db
#赋予权限
chmod 600 vusers.*

#指定认证方式
echo -e "#%PAM-1.0\n\nauth required pam_userdb.so db=/etc/vsftpd/vusers\naccount required  pam_userdb.so db=/etc/vsftpd/vusers" > /etc/pam.d/vsftpd.vu
#创建虚拟配置文件目录
mkdir /etc/vsftpd/vconf
#进入虚拟用户配置文件目录
cd vconf/
#新增配置
echo 'local_root=/data/user1' > user1
echo 'local_root=/data/user2' > user2

#查看user1与user2的主目录
cat user1
local_root=/data/user1
cat user2
local_root=/data/user2

#新建测试文件
touch /data/user1/test1
touch /data/user2/test2

#如果没有指定虚拟用户的ftp目录，默认访问目录如下 /var/ftp/pub/

经过测试设置的虚拟用户user2禁锢在了/data/user2 目录下

#在Windows下访问到新增的test1、test2文件，如果没变过来多刷新几遍
/data/user1/test1
/data/user2/test2
```

