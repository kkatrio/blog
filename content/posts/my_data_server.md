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
sqlite php7.0-sqlite  
php7.0-curl php7.0-zip php7.0-xml php7.0-mbstring  

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

nextcloud should work, create admin account

## hardening
### place data directory outside of the web root
```
mkdir -p /var/nextcloud
sudo mv -v /var/www/html/nextcloud/data/ /var/nextcloud/data
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
with cloudflare!  

update cloudflare DNS if the ip has changed. A bash script (originally found it [here](https://gist.github.com/TheFirsh/c9f72970eaae3aec04beb1106cc304bc)) executes every 6 hours with a cron job. Needs improvement but works for now. No ddclient.

I think it's ok if the ip has changed within the last 6 hours, but must check that on cloudflare.

### add domain in trusted domains
in *config/config.php*
