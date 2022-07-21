# Zabbix-6.2-Deploy
Инсталяция и конфигурация Zabbix 6.2 @ Ubuntu Server 22.04 LTS

Zabbix 6.2 -> Ubuntu 22.04(Jammy) -> MySQL -> Apache

0.1) Обновляемся

$ sudo apt-get update
$ sudo apt-get dist-upgrade

1) Cкачиваем репозитории и распаковываем.

$ wget https://repo.zabbix.com/zabbix/6.2/ubuntu/pool/main/z/zabbix-release/zabbix-release_6.2-1+ubuntu22.04_all.deb
$ dpkg -i zabbix-release_6.2-1+ubuntu22.04_all.deb
$ apt update

2) Установка Zabbix Server, Agent, Frontend

$ apt install zabbix-server-mysql zabbix-frontend-php zabbix-apache-conf zabbix-sql-scripts zabbix-agent

3) Создаём БД

$ sudo mysql --username=root --password  (Первый вход без пароля)
password
mysql> create database названиеБазы character set utf8mb4 collate utf8mb4_bin;
mysql> create user названиеЮзера@localhost identified by 'вводимПароль';
mysql> grant all privileges on названиеБазы.* to названиеЮзера@localhost;
mysql> SET GLOBAL log_bin_trust_function_creators = 1;
mysql> quit;

4) On Zabbix server host import initial schema and data. You will be prompted to enter your newly created password.

$ zcat /usr/share/doc/zabbix-sql-scripts/mysql/server.sql.gz | mysql --username=названиеЮзера --password названиеБазы

Disable log_bin_trust_function_creators option after importing database schema.

$ mysql --username=root --password
password
mysql> SET GLOBAL log_bin_trust_function_creators = 0;
mysql> quit;

5) Залазим в файлы конфигурации и подставляем наши значения
-> Edit file /etc/zabbix/zabbix_server.conf

       DBPassword=password

6) Запускаем сервак, агент, и фронт
$ systemctl restart zabbix-server zabbix-agent apache2
$ systemctl enable zabbix-server zabbix-agent apache2


Connect to your newly installed Zabbix frontend: http://server_ip_or_name/zabbix



