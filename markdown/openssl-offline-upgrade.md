# openssl 离线升级

## 系统信息

```

# openssl version
OpenSSL 1.0.2k-fips  26 Jan 2017
# uname -a
Linux slj-swgl-dsjgl4 3.10.0-957.el7.x86_64 #1 SMP Thu Nov 8 23:39:32 UTC 2018 x86_64 x86_64 x86_64 GNU/Linux

```

## 无互联网，需配置本地yum源

```
# 挂载镜像

mount -o loop /mnt/iso/XXXXX.iso /mnt/cdrom

# cat /etc/yum.repos.d/local.repo
[local]
name=local
baseurl=file:///data/cdh/yum_source
enabled=1
gpgcheck=0
gpgkey=file:///data/cdh/yum_source/RPM-GPG-KEY-CentOS-7

# yum clean all
```

## openssl 所需依赖包

```
 To install OpenSSL, you will need:

  * A make implementation
  * Perl 5 with core modules (please read NOTES.PERL)
  * The perl module Text::Template (please read NOTES.PERL)
  * an ANSI C compiler
  * a development environment in the form of development libraries and C
    header files
  * a supported operating system


```

* perl5的版本先装上, 然后执行下面 yum 语句
  * yum -y install gcc automake autoconf libtool make perl-Test-Simple
  

## 编译

```
# 下载地址
$ wget https://www.openssl.org/source/openssl-1.1.1h.tar.gz
$ ./config
$ make
$ make test
$ make install
$ mv /usr/bin/openssl /usr/bin/oldopenssl
$ ln -s /usr/local/bin/openssl /usr/bin/openssl
$ openssl version // 报错了
openssl: error while loading shared libraries: libssl.so.1.1: cannot open shared object file: No such file or directory
$ ln -s /usr/local/lib64/libssl.so.1.1 /usr/lib64/
$ ln -s /usr/local/lib64/libcrypto.so.1.1 /usr/lib64/
$ openssl version
OpenSSL 1.1.1h  22 Sep 2020

```
