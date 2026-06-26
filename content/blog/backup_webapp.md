---
title: "Backup webapp"
date: 2019-06-16T13:48:11+03:00
---


* `tar -czf app-html-$(date +%F).tar.gz html/`

* scp <path-to>app-html-<date>.tar.gz

* check users `select User from mysql.user;`

* check databases `show databases;`

* mysqldump -u YourUser -p YourDatabaseName > ExportedDump.sql 

* scp wantedsqlfile.sql

