# Zabbix-installation-

Zabbix Server is the central component of the Zabbix monitoring system —it’s the brain that collects, stores, analyzes, and displays all monitoring data.

<img width="661" height="566" alt="image" src="https://github.com/user-attachments/assets/d11b09d0-95ba-4348-99f1-982be82d4a8d" />

Refernce : https://tecadmin.net/how-to-install-zabbix-server-on-centos-9/

## Prerequiests : 

2 VMS one for Zabbix server and other for client agent "OS "Rocky linux 9.6"

we need database and apache for web browser and download zabbix packages 

## Steps : 

on the server Zabbix 

1- Add the Zabbix repository

sudo rpm -Uvh https://repo.zabbix.com/zabbix/6.0/rhel/9/x86_64/zabbix-release-6.0-4.el9.noarch.rpm

sudo dnf clean all


2-Install database (MariaDB) + Apache + Zabbix packages

sudo dnf install -y \

zabbix-server-mysql zabbix-web-mysql zabbix-apache-conf zabbix-sql-scripts zabbix-agent \
    
mariadb-server httpd php-mysqlnd php-bcmath php-gd php-xml php-mbstring php-ldap php-json php-snmp php-zip
    
3- Start and secure MariaDB

sudo systemctl enable --now mariadb

sudo mysql_secure_installation

in this steps i create root password for DB 

4- Now we are ready to Create the Zabbix database : 

mysql -u root -p

CREATE DATABASE zabbix CHARACTER SET utf8mb4 COLLATE utf8mb4_bin;

CREATE USER 'zabbix'@'localhost' IDENTIFIED BY 'admin123';

GRANT ALL PRIVILEGES ON zabbix.* TO 'zabbix'@'localhost';

FLUSH PRIVILEGES;

EXIT;

5- Import the initial schema 

zcat /usr/share/zabbix-sql-scripts/mysql/server.sql.gz | mysql -u zabbix -p zabbix

After this command completes:

database zabbix now has all required tables (like hosts, items, users, etc.).

It also includes some initial data like default user “Admin”, default dashboard, and system configuration as belwo :

<img width="1920" height="1039" alt="image" src="https://github.com/user-attachments/assets/c6b8c6ed-7dc5-4d4b-9cd2-025c21f767d1" />

6- Configure the Zabbix server

Edit /etc/zabbix/zabbix_server.conf:

DBHost=localhost

DBName=zabbix

DBUser=zabbix

DBPassword=admin123

<img width="1920" height="1039" alt="image" src="https://github.com/user-attachments/assets/d7fb1d34-0e63-4c68-850d-befa5bcff766" />

7- Set PHP timezone 

Zabbix’s frontend (the web interface) runs on PHP, and PHP needs to know what timezone your server should use for displaying times — such as:

When alerts happen & When items were last updated & Graph timestamps & Event logs, history, reports, etc.

vi /etc/php.ini 

<img width="1920" height="1039" alt="image" src="https://github.com/user-attachments/assets/186432d9-1040-4a6b-9a70-4cfdf5dcf562" />

Then restart Apache 

sudo systemctl restart httpd

8- Disable firewall or open allow  port " 10050/tcp"

9- Allow Apache to connect to DB 

sudo setsebool -P httpd_can_network_connect_db on

10-Finish setup via web UI 

<img width="1920" height="1030" alt="image" src="https://github.com/user-attachments/assets/82948e10-db06-4598-9862-ff24328a5e70" />

<img width="1920" height="1030" alt="image" src="https://github.com/user-attachments/assets/f0e1fe6e-f0f6-4e2a-ac10-d96eb469957a" />

<img width="1920" height="1030" alt="image" src="https://github.com/user-attachments/assets/4e319292-3bcb-48b1-8481-63f26225ced9" />

<img width="1920" height="1030" alt="image" src="https://github.com/user-attachments/assets/d6d77726-c093-418d-a6d3-c743da66cd15" />

<img width="1920" height="1030" alt="image" src="https://github.com/user-attachments/assets/7f3f29c2-37b4-44ba-8aa0-0ac5a7f5a0d7" />

by default login : Admin 

Pssword :          zabbix  


<img width="1920" height="1030" alt="image" src="https://github.com/user-attachments/assets/162f38af-059f-4b4a-8429-dcffa4b525b8" />

10 - Install the Zabbix Agent on the host you want to monitor

Install the Zabbix repo (for version 6.0)

sudo rpm -Uvh https://repo.zabbix.com/zabbix/6.0/rhel/9/x86_64/zabbix-release-6.0-4.el9.noarch.rpm

sudo dnf clean all

11-Install the agent

sudo dnf install -y zabbix-agent

<img width="1920" height="1039" alt="image" src="https://github.com/user-attachments/assets/57ca45ee-7a69-4378-aad3-9fa65bfe527e" />

12-Configure the Zabbix Agent 

sudo nano /etc/zabbix/zabbix_agentd.conf

Server=192.168.142.162 

ServerActive=192.168.142.162 

Hostname=worker01 

13-Finally, restart the Zabbix Agent

sudo systemctl restart zabbix-agent && sudo systemctl enable zabbix-agent

14-Add the Host in the Zabbix Web Interface

Configuration → Hosts → Create host

<img width="1920" height="1030" alt="image" src="https://github.com/user-attachments/assets/afd3784e-527e-4683-ae09-170e779ec178" />


<img width="1920" height="1030" alt="image" src="https://github.com/user-attachments/assets/82c536cd-3c53-400f-bbed-42f3c1797dbd" />


Add VMs to be mointoring on zabbix 

==> add all hosts Name on the hosts file 

==> create hosts for the belwo :

APP01 : 

to be monitor tomcat  should be download JMX on server zabbix " Hava-gateway zabbix" and enable it on the client 

and also download agent 

Web01 : 

download agent and using nginx services 

db01 : 

download agent and using mysql by agent to monitor DB 

memchach & Rabbitmq : 

Downlad agent and using memechase and rabbitmq using agent 


<img width="1920" height="1030" alt="image" src="https://github.com/user-attachments/assets/450cac5a-802e-40bf-b142-c83dccfc51b2" />



 
 
 




    


