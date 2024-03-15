# otus-pgsql-hw-diplom

#  Cоздание и тестирование кластера PostgreSQL на базе Patroni

## Обзор рекомендаций, литературы, документации

https://patroni.readthedocs.io/en/latest/README.html

https://bootvar.com/how-to-configure-postgresql-ha-with-patroni/

https://www.dbi-services.com/blog/patroni-2-0-new-features-patroni-on-pure-raft/

https://otus.ru/learning/260905/#/

https://github.com/lalbrekht/otus-patroni

https://github.com/zalando/patroni

https://habr.com/ru/companies/vk/articles/452846/

https://www.youtube.com/watch?v=lMPYerAYEVs

https://github.com/etcd-io/etcd

https://blogs.sungeek.net/unixwiz/2018/09/02/centos-7-postgresql-10-patroni/

https://docs.percona.com/postgresql/11/solutions/ha-setup-yum.html

https://access.crunchydata.com/documentation/patroni/2.1.3/pdf/patroni.pdf

https://www.dbi-services.com/blog/patroni-2-0-new-features-patroni-on-pure-raft/

https://docs.vmware.com/en/VMware-Postgres/16.1/vmware-postgres/bp-patroni-setup.html

https://itdraft.ru/2023/08/14/nastrojka-otkazoustojchivogo-klastera-postgresql-v-linux/

для настройки

https://itdraft.ru/2023/08/14/nastrojka-otkazoustojchivogo-klastera-postgresql-v-linux/

https://postgreshelp.com/postgresql-ha-with-patroni-3/#Install_Patroni_p4p5p6

https://www.dmosk.ru/miniinstruktions.php?mini=patroni-consul-centos&ysclid=ltqxx4ifq751545757

https://dbtut.com/index.php/2022/06/04/how-to-create-a-postgresql-cluster-with-patroni/


Подготовка 1-ой ВМ с PGSQL

Виртуальная машина в среде ВМваре
Centos-7, так как это корпоративный стандарт

yum update

yum install -y https://download.postgresql.org/pub/repos/yum/reporpms/EL-7-x86_64/pgdg-redhat-repo-latest.noarch.rpm

yum localinstall https://dl.fedoraproject.org/pub/epel/7/x86_64/Packages/l/libzstd-1.5.5-1.el7.x86_64.rpm

yum install postgresql15 postgresql15-server postgresql15-contrib

psql -V

systemctl enable postgresql-15

детально процес подготовки и инсталяции PostgreSQL описан в ДЗ к уроку 2
https://github.com/olegrovenskiy/otus-pgsql-hw-lesson-2?tab=readme-ov-file

1.  Сервер mck-network-test-tmp-1 с postgresql-15 подготовлен
2.  Открыть файрвол

Patroni:
8008: This port is used for the HTTP API of Patroni.
HAProxy:
5000: This port is used for HTTP connections to the backend (PostgreSQL service).
5001: This port is used for HTTPS connections to the backend (PostgreSQL service).
etcd:
2379: This port is used for etcd client communication.
2380: This port is used for etcd server-to-server communication.
Keepalived:
112: This port is used for the Virtual Router Redundancy Protocol (VRRP) communication between Keepalived instances.
5405: This port is used for the multicast traffic between Keepalived instances.
Pgbouncer:
6432: This port is used for Pgbouncer client connections.
PostgreSQL:
5432: This port is used for PostgreSQL client connections.
Web server:
5000: This port is used for HTTP connections to the web server.
5001: This port is used for HTTPS connections to the web server.
7000: This port is used for the reverse proxy connection from the web server to HAProxy.

    [root@mck-network-test-tmp-1 data]#
    [root@mck-network-test-tmp-1 data]# firewall-cmd --zone=public --add-port=5432/tcp --permanent
    success
    [root@mck-network-test-tmp-1 data]# firewall-cmd --zone=public --add-port=6432/tcp --permanent
    success
    [root@mck-network-test-tmp-1 data]# firewall-cmd --zone=public --add-port=8008/tcp --permanent
    success
    [root@mck-network-test-tmp-1 data]# firewall-cmd --zone=public --add-port=2379/tcp --permanent
    success
    [root@mck-network-test-tmp-1 data]# firewall-cmd --zone=public --add-port=2380/tcp --permanent
    success
    [root@mck-network-test-tmp-1 data]# firewall-cmd --permanent --zone=public --add-service=http
    success
    [root@mck-network-test-tmp-1 data]# firewall-cmd --zone=public --add-port=5000/tcp --permanent
    success
    [root@mck-network-test-tmp-1 data]# firewall-cmd --zone=public --add-port=5001/tcp --permanent
    success
    [root@mck-network-test-tmp-1 data]# firewall-cmd --zone=public --add-port=7000/tcp --permanent
    success
    [root@mck-network-test-tmp-1 data]# firewall-cmd --zone=public --add-port=112/tcp --permanent
    success
    [root@mck-network-test-tmp-1 data]# firewall-cmd --zone=public --add-port=5405/tcp --permanent
    success
    [root@mck-network-test-tmp-1 data]# firewall-cmd --add-rich-rule='rule protocol value="vrrp" accept' --permanent
    success
    [root@mck-network-test-tmp-1 data]# firewall-cmd --reload
    success
    [root@mck-network-test-tmp-1 data]#


Disable Selinux [all machines]

Log in to p1 and edit /etc/selinux/config file with any of your favorite text editor:
vi /etc/selinux/config
Change SELINUX=enforcing to SELINUX=disabled

SELINUX=disabled
Reboot to make the selinux changes effect:

reboot

--------------

Create postgres user [all machines]

useradd postgres
passwd postgres

------------
yum install gcc

export PATH=/usr/pgsql-15/bin/:$PATH

pip3 install --upgrade setuptools

что бы выполнить

sudo pip3 install psycopg2-binary && sudo apt install libpq-dev python3-dev && sudo pip3 install psycopg2

потребовалосб апгред питон3 и pip3
а также
yum install postgresql-devel
yum install python3-devel


после чего прошла инсталяция pip3 installpsycopg2-binary &&  pip3 install psycopg2

Необходимые подготовительные компоненты установлены





