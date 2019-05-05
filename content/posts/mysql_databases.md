---
title: "Mysql database hacking"
date: 2019-05-05T22:01:47+03:00
---

GRANT ALL PRIVILEGES ON database_name.* TO 'database_user'@'localhost';

over all databases:
GRANT ALL PRIVILEGES ON *.* TO 'database_user'@'localhost';
[link](https://linuxize.com/post/how-to-create-mysql-user-accounts-and-grant-privileges/)

```
CREATE DATABASE prestashop;
GRANT ALL ON prestashop.* TO 'prestashop'@'localhost' IDENTIFIED BY 'change-with-strong-password';
```

export with dump  
mysqldump -u YourUser -p YourDatabaseName > wantedsqlfile.sql

import sql database, on an already created emtpy database  
mysql -u username -p -h localhost DATA-BASE-NAME < data.sql 

DROP USER 'bloguser'@'localhost';

DROP DATABASE mywpblog;

SELECT User,Host FROM mysql.user;


