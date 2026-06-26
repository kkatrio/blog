---
title: "Data server on a Rasperry Pi"
date: 2019-01-23T19:06:02+02:00
---

> Live as if you were to die tomorrow. Learn as if you were to live forever --Mahatma Gandhi

### install raspbian lite
no display manager, yay!  
open ssh port 22  
change user pass

### setup static ip to the pi
in */etc/dhcpcd.conf*
```
static ip_address=192.168.1.2/24
static routers=192.168.1.1
static domain_name_servers=1.1.1.1 1.0.0.1
```

### install prerequisites
apache2  
php7.0 php7.0-gd  
sqlite php7.0-sqlite - nope!   
php7.0-curl php7.0-zip php7.0-xml php7.0-mbstring  

*instead of sqlite, go for mariadb-server*
install these instead:
mariadb-server, php7.0-mysql

mysql_secure_installation to setup root access
and [create a user and a database](https://docs.nextcloud.com/server/15/admin_manual/configuration_database/linux_database_configuration.html#configuring-a-mysql-or-mariadb-database):
```
CREATE USER 'username'@'localhost' IDENTIFIED BY 'password';
CREATE DATABASE IF NOT EXISTS nextcloud;
GRANT SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, INDEX, ALTER, CREATE TEMPORARY TABLES ON nextcloud.* TO 'username'@'localhost' IDENTIFIED BY 'password';
FLUSH privileges;
```

restart apache  
192.168.1.2 should work.

### install nextcloud
in directory */var/www/html/nextcloud*  
chown to www-data user  
create [/etc/apache2/sites-available/nextcloud.conf](https://docs.nextcloud.com/server/stable/admin_manual/installation/source_installation.html#apache-web-server-configuration)
or can be done by [allowing the .htaccess override](https://pimylifeup.com/raspberry-pi-nextcloud-server/)

* php mods rewrite and headers, env, dir, mime not enabled yet
* pretty urls not done yet
* todo: TEST */var/www/html* instead of */var/www/html/nextcloud*

nextcloud should work, create admin account:  

* select data directory - see below
* fill in database info

## hardening
### place data directory outside of the web root
```
mkdir -p /var/nextcloud
sudo mv -v /var/www/html/nextcloud/data/ /var/nextcloud/data - not needed, just select the correct directory in the installation wizard!
```
change 'ddatadirectory' in */var/www/html/nextcloud/config*

### https
##### self-signed ssl
create a new key and certificate
```
sudo mkdir -p /etc/apache2/ssl
sudo openssl req -x509 -nodes -days 365 -newkey rsa:4096 -keyout /etc/apache2/ssl/kostis.key -out /etc/apache2/ssl/kostis.crt

sudo a2enmod ssl
```
in `/etc/apache2/sites-available/default-ssl.conf` change snakeoil pem and key to new created  

`sudo a2ensite default-ssl.conf`  
restart apache

##### or let's encrypt

## DDNS
use a REST API to dynamically update the DNS.

### add domain in trusted domains
in */var/www/html/nextcloud/config/config.php*
