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

Сервер mck-network-test-tmp-1 с postgresql-15 подготовлен







