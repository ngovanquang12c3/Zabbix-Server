#!/bin/sh
sudo -s
cd

wget https://repo.zabbix.com/zabbix/7.0/ubuntu/pool/main/z/zabbix-release/zabbix-release_latest+ubuntu22.04_all.deb
dpkg -i zabbix-release_latest+ubuntu22.04_all.deb
apt update
apt install zabbix-server-mysql zabbix-frontend-php zabbix-apache-conf zabbix-sql-scripts zabbix-agent -y
apt-get install mysql-server -y
systemctl start mysql.service
systemctl status mysql.service

mysql -uroot -p 
create database zabbix character set utf8mb4 collate utf8mb4_bin;
create user zabbix@localhost identified by 'WM@zabbixAdmin';
grant all privileges on zabbix.* to zabbix@localhost;
set global log_bin_trust_function_creators = 1;
quit;

zcat /usr/share/zabbix-sql-scripts/mysql/server.sql.gz | mysql --default-character-set=utf8mb4 -uzabbix -p zabbix


sed -i "s/# DBPassword=/DBPassword=WM@zabbixAdmin/g" /etc/zabbix/zabbix_server.conf

systemctl restart zabbix-server zabbix-agent apache2
systemctl enable zabbix-server zabbix-agent apache2

echo "ACCOUNT!! USER:Admin - PASSWORD: zabbix"
echo "=========================SETUP ZABBIX 7.0 LTS ON UBUNTU 22.04 SUCCESS=============================="
cd
