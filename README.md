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

ETCD INstall

pip3 install python-etcd

        [root@mck-network-test-tmp-1 ~]# pip3 install python-etcd
        Collecting python-etcd
          WARNING: Retrying (Retry(total=4, connect=None, read=None, redirect=None, status=None)) after connection broken by         'NewConnectionError('<pip._vendor.urllib3.connection.HTTPSConnection object at 0x7f7c8c816a90>: Failed to establish         a new connection: [Errno -2] Name or service not known')': /packages/a1/da/616a4d073642da5dd432e5289b7c1cb0963cc5dd        e23d1ecb8d726821ab41/python-etcd-0.4.5.tar.gz
          WARNING: Retrying (Retry(total=3, connect=None, read=None, redirect=None, status=None)) after connection broken by         'NewConnectionError('<pip._vendor.urllib3.connection.HTTPSConnection object at 0x7f7c8c816d30>: Failed to establish         a new connection: [Errno -2] Name or service not known')': /packages/a1/da/616a4d073642da5dd432e5289b7c1cb0963cc5dd        e23d1ecb8d726821ab41/python-etcd-0.4.5.tar.gz
          Downloading python-etcd-0.4.5.tar.gz (37 kB)
          Installing build dependencies ... done
          Getting requirements to build wheel ... done
          Installing backend dependencies ... done
          Preparing metadata (pyproject.toml) ... done
        Collecting urllib3>=1.7.1 (from python-etcd)
          Downloading urllib3-2.2.1-py3-none-any.whl.metadata (6.4 kB)
        Collecting dnspython>=1.13.0 (from python-etcd)
          Downloading dnspython-2.6.1-py3-none-any.whl.metadata (5.8 kB)
        Downloading dnspython-2.6.1-py3-none-any.whl (307 kB)
           ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 307.7/307.7 kB 3.1 MB/s eta 0:00:00
        Downloading urllib3-2.2.1-py3-none-any.whl (121 kB)
           ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 121.1/121.1 kB 28.7 MB/s eta 0:00:00
        Building wheels for collected packages: python-etcd
          Building wheel for python-etcd (pyproject.toml) ... done
          Created wheel for python-etcd: filename=python_etcd-0.4.5-py3-none-any.whl size=38481 sha256=605fbbcf9963a8e4368d0        55a0d47847ae6f833e18520abaf84226bdd4e7f9df8
          Stored in directory: /root/.cache/pip/wheels/16/65/42/fdf408dfb6c8c726ca72a8c11b4e25d566a4e9e8cea042c5d6
        Successfully built python-etcd
        Installing collected packages: urllib3, dnspython, python-etcd
        Successfully installed dnspython-2.6.1 python-etcd-0.4.5 urllib3-2.2.1
        WARNING: Running pip as the 'root' user can result in broken permissions and conflicting behaviour with the system package manager. It is recommended to use a virtual environment instead: https://pip.pypa.io/warnings/venv
        [root@mck-network-test-tmp-1 ~]#
        [root@mck-network-test-tmp-1 ~]#
        [root@mck-network-test-tmp-1 ~]#
        [root@mck-network-test-tmp-1 ~]#
        [root@mck-network-test-tmp-1 ~]#
        [root@mck-network-test-tmp-1 ~]#
        [root@mck-network-test-tmp-1 ~]#
        [root@mck-network-test-tmp-1 ~]#
        [root@mck-network-test-tmp-1 ~]#
        [root@mck-network-test-tmp-1 ~]#
        [root@mck-network-test-tmp-1 ~]#
        [root@mck-network-test-tmp-1 ~]#
        [root@mck-network-test-tmp-1 ~]#
        [root@mck-network-test-tmp-1 ~]#
        [root@mck-network-test-tmp-1 ~]#
        [root@mck-network-test-tmp-1 ~]#
        [root@mck-network-test-tmp-1 ~]#
        [root@mck-network-test-tmp-1 ~]#
        [root@mck-network-test-tmp-1 ~]#
        [root@mck-network-test-tmp-1 ~]#
        [root@mck-network-test-tmp-1 ~]#
        [root@mck-network-test-tmp-1 ~]#
        [root@mck-network-test-tmp-1 ~]#
        [root@mck-network-test-tmp-1 ~]#
        [root@mck-network-test-tmp-1 ~]#
        [root@mck-network-test-tmp-1 ~]# yum install -y etcd
        Loaded plugins: fastestmirror, langpacks
        Loading mirror speeds from cached hostfile
         * base: ftp.nsc.ru
         * extras: ftp.nsc.ru
         * updates: ftp.nsc.ru
        Resolving Dependencies
        --> Running transaction check
        ---> Package etcd.x86_64 0:3.3.11-2.el7.centos will be installed
        --> Finished Dependency Resolution
        
        Dependencies Resolved
        
        ============================================================================================================================
         Package                 Arch                      Version                                  Repository                 Size
        ============================================================================================================================
        Installing:
         etcd                    x86_64                    3.3.11-2.el7.centos                      extras                     10 M
        
        Transaction Summary
        ============================================================================================================================
        Install  1 Package
        
        Total download size: 10 M
        Installed size: 45 M
        Downloading packages:
        etcd-3.3.11-2.el7.centos.x86_64.rpm                                                                  |  10 MB  00:00:00
        Running transaction check
        Running transaction test
        Transaction test succeeded
        Running transaction
          Installing : etcd-3.3.11-2.el7.centos.x86_64                                                                          1/1
          Verifying  : etcd-3.3.11-2.el7.centos.x86_64                                                                          1/1
        
        Installed:
          etcd.x86_64 0:3.3.11-2.el7.centos
        
        Complete!
        [root@mck-network-test-tmp-1 ~]# nano /etc/etcd/etcd.conf
        [root@mck-network-test-tmp-1 ~]#
        [root@mck-network-test-tmp-1 ~]#
        [root@mck-network-test-tmp-1 ~]#
        [root@mck-network-test-tmp-1 ~]# cat /etc/etcd/etcd.conf
        #[Member]
        #ETCD_CORS=""
        ETCD_DATA_DIR="/var/lib/etcd/default.etcd"
        #ETCD_WAL_DIR=""
        #ETCD_LISTEN_PEER_URLS="http://localhost:2380"
        ETCD_LISTEN_CLIENT_URLS="http://localhost:2379"
        #ETCD_MAX_SNAPSHOTS="5"
        #ETCD_MAX_WALS="5"
        ETCD_NAME="default"
        #ETCD_SNAPSHOT_COUNT="100000"
        #ETCD_HEARTBEAT_INTERVAL="100"
        #ETCD_ELECTION_TIMEOUT="1000"
        #ETCD_QUOTA_BACKEND_BYTES="0"
        #ETCD_MAX_REQUEST_BYTES="1572864"
        #ETCD_GRPC_KEEPALIVE_MIN_TIME="5s"
        #ETCD_GRPC_KEEPALIVE_INTERVAL="2h0m0s"
        #ETCD_GRPC_KEEPALIVE_TIMEOUT="20s"
        #
        #[Clustering]
        #ETCD_INITIAL_ADVERTISE_PEER_URLS="http://localhost:2380"
        ETCD_ADVERTISE_CLIENT_URLS="http://localhost:2379"
        #ETCD_DISCOVERY=""
        #ETCD_DISCOVERY_FALLBACK="proxy"
        #ETCD_DISCOVERY_PROXY=""
        #ETCD_DISCOVERY_SRV=""
        #ETCD_INITIAL_CLUSTER="default=http://localhost:2380"
        #ETCD_INITIAL_CLUSTER_TOKEN="etcd-cluster"
        #ETCD_INITIAL_CLUSTER_STATE="new"
        #ETCD_STRICT_RECONFIG_CHECK="true"
        #ETCD_ENABLE_V2="true"
        #
        #[Proxy]
        #ETCD_PROXY="off"
        #ETCD_PROXY_FAILURE_WAIT="5000"
        #ETCD_PROXY_REFRESH_INTERVAL="30000"
        #ETCD_PROXY_DIAL_TIMEOUT="1000"
        #ETCD_PROXY_WRITE_TIMEOUT="5000"
        #ETCD_PROXY_READ_TIMEOUT="0"
        #
        #[Security]
        #ETCD_CERT_FILE=""
        #ETCD_KEY_FILE=""
        #ETCD_CLIENT_CERT_AUTH="false"
        #ETCD_TRUSTED_CA_FILE=""
        #ETCD_AUTO_TLS="false"
        #ETCD_PEER_CERT_FILE=""
        #ETCD_PEER_KEY_FILE=""
        #ETCD_PEER_CLIENT_CERT_AUTH="false"
        #ETCD_PEER_TRUSTED_CA_FILE=""
        #ETCD_PEER_AUTO_TLS="false"
        #
        #[Logging]
        #ETCD_DEBUG="false"
        #ETCD_LOG_PACKAGE_LEVELS=""
        #ETCD_LOG_OUTPUT="default"
        #
        #[Unsafe]
        #ETCD_FORCE_NEW_CLUSTER="false"
        #
        #[Version]
        #ETCD_VERSION="false"
        #ETCD_AUTO_COMPACTION_RETENTION="0"
        #
        #[Profiling]
        #ETCD_ENABLE_PPROF="false"
        #ETCD_METRICS="basic"
        #
        #[Auth]
        #ETCD_AUTH_TOKEN="simple"
        
        
        [Member]
        ETCD_LISTEN_PEER_URLS="http://10.102.6.24:2380,http://localhost:2380"
        ETCD_LISTEN_CLIENT_URLS="http://10.102.6.24:2379,http://localhost:2379"
        [Clustering]
        ETCD_INITIAL_ADVERTISE_PEER_URLS="http://10.102.6.24:2380"
        ETCD_ADVERTISE_CLIENT_URLS="http://10.102.6.24:2379"
        ETCD_INITIAL_CLUSTER="default=http://10.102.6.24:2380"
        ETCD_INITIAL_CLUSTER_TOKEN="etcd-cluster"
        ETCD_INITIAL_CLUSTER_STATE="new"
        [root@mck-network-test-tmp-1 ~]#
        [root@mck-network-test-tmp-1 ~]#
        [root@mck-network-test-tmp-1 ~]# nano /etc/etcd/etcd.conf
        [root@mck-network-test-tmp-1 ~]#
        [root@mck-network-test-tmp-1 ~]#
        [root@mck-network-test-tmp-1 ~]# systemctl enable etcd
        Created symlink from /etc/systemd/system/multi-user.target.wants/etcd.service to /usr/lib/systemd/system/etcd.service.
        [root@mck-network-test-tmp-1 ~]# systemctl start etcd
        [root@mck-network-test-tmp-1 ~]# systemctl status etcd
        ● etcd.service - Etcd Server
           Loaded: loaded (/usr/lib/systemd/system/etcd.service; enabled; vendor preset: disabled)
           Active: active (running) since Fri 2024-03-15 08:49:50 EDT; 11s ago
         Main PID: 34124 (etcd)
           CGroup: /system.slice/etcd.service
                   └─34124 /usr/bin/etcd --name=default --data-dir=/var/lib/etcd/default.etcd --listen-client-urls=http://10.102....
        
        Mar 15 08:49:50 mck-network-test-tmp-1.mgc.local etcd[34124]: raft.node: 6013fa0645d37aad elected leader 6013fa0645d3...rm 2
        Mar 15 08:49:50 mck-network-test-tmp-1.mgc.local etcd[34124]: published {Name:default ClientURLs:[http://10.102.6.24:...4062
        Mar 15 08:49:50 mck-network-test-tmp-1.mgc.local etcd[34124]: setting up the initial cluster version to 3.3
        Mar 15 08:49:50 mck-network-test-tmp-1.mgc.local etcd[34124]: ready to serve client requests
        Mar 15 08:49:50 mck-network-test-tmp-1.mgc.local etcd[34124]: serving insecure client requests on 127.0.0.1:2379, thi...ged!
        Mar 15 08:49:50 mck-network-test-tmp-1.mgc.local etcd[34124]: ready to serve client requests
        Mar 15 08:49:50 mck-network-test-tmp-1.mgc.local etcd[34124]: serving insecure client requests on 10.102.6.24:2379, t...ged!
        Mar 15 08:49:50 mck-network-test-tmp-1.mgc.local systemd[1]: Started Etcd Server.
        Mar 15 08:49:50 mck-network-test-tmp-1.mgc.local etcd[34124]: set the initial cluster version to 3.3
        Mar 15 08:49:50 mck-network-test-tmp-1.mgc.local etcd[34124]: enabled capabilities for version 3.3
        Hint: Some lines were ellipsized, use -l to show in full.
        [root@mck-network-test-tmp-1 ~]#
        
Сервис ETCD инсталирован и запускается


