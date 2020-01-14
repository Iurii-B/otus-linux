

#Домашнее задание 6 

- Базовое решение Домашнего задания 6 (без Docker). В Vagrant (вагрант-файл в папке проекта) сделал 2 машины на Centos, Host1 (IP 10.0.0.1) и Host2 (IP 10.0.0.2).
- Действия в основном повторяют описанные в методичке к занятию.
- Основные манипуляции проводил на Host1. На нем создал репозиторий с nginx и утилитой tftp (https://centos.pkgs.org/7/centos-x86_64/tftp-5.2-22.el7.x86_64.rpm.html), на нем же установил сам nginx.
- На Host2 только подключил репозиторий с Host1 и проверил установку из него сервера tftp. Чтобы убедиться, что установка идет из только что созданного репозитория в локальной сети, добавил в iptables следующие правила:

[vagrant@Host2 ~]$ sudo iptables -L

Chain OUTPUT (policy ACCEPT)
target     prot opt source               destination
ACCEPT     all  --  anywhere             192.168.0.0/16
ACCEPT     all  --  anywhere             10.0.0.0/8
DROP       all  --  anywhere             anywhere


Это оказалось не лишним, т.к. хотя репозиторий и подключился, система пыталась установить tftp только из репозитория base.

[vagrant@Host2 ~]$sudo yum repolist
Failed to set locale, defaulting to C
Loaded plugins: fastestmirror
Loading mirror speeds from cached hostfile
 * base: mirror.awanti.com
 * extras: mirror.awanti.com
 * updates: mirror.awanti.com
repo id                                                                           repo name                                                                           status
base/7/x86_64                                                                     CentOS-7 - Base                                                                     10097
extras/7/x86_64                                                                   CentOS-7 - Extras                                                                     307
otus                                                                              otus-linux                                                                              2
updates/7/x86_64                                                                  CentOS-7 - Updates                                                                   1010
repolist: 11416

Подключал вот такой конструкцией:

[vagrant@Host2 ~]$ more /etc/yum.repos.d/otus.repo
[otus]
name=otus-linux
baseurl=http://10.0.0.1/repo/
gpgcheck=0
enabled=1

При этом установка через localinstall с указанием URL работает нормально:

[vagrant@Host2 ~]$ sudo yum localinstall http://10.0.0.1/repo/tftp-5.2-22.el7.x86_64.rpm
Failed to set locale, defaulting to C
Loaded plugins: fastestmirror
tftp-5.2-22.el7.x86_64.rpm                                                                                                                           |  38 kB  00:00:00
Examining /var/tmp/yum-root-3CQ_sw/tftp-5.2-22.el7.x86_64.rpm: tftp-5.2-22.el7.x86_64
Marking /var/tmp/yum-root-3CQ_sw/tftp-5.2-22.el7.x86_64.rpm to be installed
Resolving Dependencies
--> Running transaction check
---> Package tftp.x86_64 0:5.2-22.el7 will be installed
--> Finished Dependency Resolution

Dependencies Resolved

============================================================================================================================================================================
 Package                           Arch                                Version                                   Repository                                            Size
============================================================================================================================================================================
Installing:
 tftp                              x86_64                              5.2-22.el7                                /tftp-5.2-22.el7.x86_64                               52 k

Transaction Summary
============================================================================================================================================================================
Install  1 Package

Total size: 52 k
Installed size: 52 k
Is this ok [y/d/N]:



- На "сервере" (Host1) все в целом нормально и соответствует лекции и методичке:

[root@Host1 ~]# history
    1  yum install redhat-lsb-core
    2  yum install wget
    3  yum install rpmdevtools
    4  yum install rpm-build
    5  yum install createrepo
    6  yum install yum-utils
    7  wget https://nginx.org/packages/centos/7/SRPMS/nginx-1.14.1-1.el7_4.ngx.src.rpm
    8  rpm -i nginx-1.14.1-1.el7_4.ngx.src.rpm
   ..........
   20  cd /root/rpmbuild/
   21  ll
   22  pwd
   23  cd /root/
   24  wget https://www.openssl.org/source/latest.tar.gz
   25  tar -xvf latest.tar.gz
   26  ll
   27  yum-builddep rpmbuild/SPECS/nginx.spec
   28  more rpmbuild/SPECS/nginx.spec
   .........
   34  vi rpmbuild/SPECS/nginx.spec
   35  rpmbuild -bb rpmbuild/SPECS/nginx.spec
   36  yum install gcc
   37  rpmbuild -bb rpmbuild/SPECS/nginx.spec
   ........
   57  ll rpmbuild/RPMS/x86_64/
   58  exit
   59  yum localinstall
   60  yum localinstall --help
   61  yum localinstall -y ~/rpmbuild/RPMS/x86_64/nginx-1.14.1-1.el7_4.ngx.x86_64.rpm
   62  systemctl start nginx
   63  systemctl status nginx
   64  exit
   65  mkdir /usr/share/nginx/html/repo
   66  cp rpmbuild/RPMS/x86_64/nginx-1.14.1-1.el7_4.ngx.x86_64.rpm
   67  cp rpmbuild/RPMS/x86_64/nginx-1.14.1-1.el7_4.ngx.x86_64.rpm /usr/share/nginx/html/repo/
   68  wget http://mirror.centos.org/centos/7/os/x86_64/Packages/tftp-5.2-22.el7.x86_64.rpm /usr/share/nginx/html/repo/tftp-5.2-22.el7.x86_64.rpm
   69  createrepo /usr/share/nginx/html/repo/
   70  vim /etc/nginx/conf.d/default.conf
   71  vi /etc/nginx/conf.d/default.conf
   72  nginx -t
   73  systemctl restart nginx
   74  systemctl status nginx
   75  cat >> /etc/yum.repos.d/otus.repo << EOF

[root@Host1 ~]# yum repolist
Loaded plugins: fastestmirror
Loading mirror speeds from cached hostfile
 * base: mirror.docker.ru
 * extras: mirror.yandex.ru
 * updates: mirror.yandex.ru
repo id                                                                                                           repo name                                                                                                            status
base/7/x86_64                                                                                                     CentOS-7 - Base                                                                                                      10 097
extras/7/x86_64                                                                                                   CentOS-7 - Extras                                                                                                       307
otus                                                                                                              otus-linux                                                                                                                1
updates/7/x86_64                                                                                                  CentOS-7 - Updates                                                                                                    1 010
repolist: 11 415


