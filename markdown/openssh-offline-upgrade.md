# openssh离线升级

## 系统信息

```bash
$ ssh -V
OpenSSH_7.4p1, OpenSSL 1.0.2k-fips  26 Jan 2017
```


## 安装telnet-server以及xinetd

```bash

$ yum install xinetd telnet-server -y

$ cat >> /etc/xinetd.d/telnet << \EOF

# default: on
# description: The telnet server serves telnet sessions; it uses \
#   unencrypted username/password pairs for authentication.
service telnet
{
    disable = yes
    flags       = REUSE
    socket_type = stream       
    wait        = no
    user        = root
    server      = /usr/sbin/in.telnetd
    log_on_failure  += USERID
}

EOF


$ cat >> /etc/securetty << \EOF

pts/0
pts/1
pts/2
pts/3

EOF


# 开机启动

systemctl enable xinetd
systemctl enable telnet.socket
systemctl start telnet.socket
systemctl start xinetd

# 测试

# telnet 192.30.35.14
Trying 192.30.35.14...
Connected to 192.30.35.14.
Escape character is '^]'.
```

## 编译

** 安装依赖 **

注意: 升级openssh时,需要升级openssl

```bash
$ yum install  -y gcc gcc-c++ glibc make autoconf openssl openssl-devel pcre-devel  pam-devel

$ ./configure --prefix=/usr/ --sysconfdir=/etc/ssh --with-ssl-dir=/usr/local/lib64/ --with-zlib --with-pam --with-md5-password --with-ssl-engine --with-selinux

$ make && make install
$ chmod 0600 /etc/ssh/ssh_host_rsa_key /etc/ssh/ssh_host_ecdsa_key /etc/ssh/ssh_host_ed25519_key

$ ssh -V
OpenSSH_8.4p1, OpenSSL 1.1.1h  22 Sep 2020
  
# 移走以前的ssh服务, 防止与新的冲突
$ mkdir /etc/ssh_old
$ mv /usr/lib/systemd/system/sshd.service /etc/ssh_old/sshd.service
$ mv /usr/lib/systemd/system/sshd.socket /etc/ssh_old/sshd.socket

# 拷贝启动脚本
$ cp -a contrib/redhat/sshd.init /etc/init.d/sshd

# 重新启动 sshd

$ systemctl daemon-reload
$ /etc/init.d/sshd restart

# 关闭telnet

$ systemctl disable xinetd
$ systemctl disable telnet.socket
$ systemctl stop telnet.socket
$ systemctl stop xinetd

```
