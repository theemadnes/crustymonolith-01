### notes on setting stuff up 

following https://docs.djangoproject.com/en/2.2/intro/tutorial01/

installed django via pip 


installed to crustymonolith

fire up test server: 

`python manage.py runserver`


interesting note from the docs: ```Now’s a good time to note: don’t use this server in anything resembling a production environment. It’s intended only for use while developing. (We’re in the business of making Web frameworks, not Web servers.)```


Creating test app `polls`

Setting up mysql integration

looks like i need to pre-create the db:
```django.db.utils.OperationalError: (1049, "Unknown database 'crustymonolith'")```

connect to it first: 

`mysql -u $mysql_user -h $mysql_ip -p`

then create the db:

`CREATE DATABASE crustymonolith CHARACTER SET utf8;`

Further notes on mysql integration:
https://docs.djangoproject.com/en/2.2/ref/databases/#mysql-notes

Perform migration / create database tables needed by Django:
`python manage.py migrate`

Check database tables:

```mysql> use crustymonolith;
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
mysql> show tables;
+----------------------------+
| Tables_in_crustymonolith   |
+----------------------------+
| auth_group                 |
| auth_group_permissions     |
| auth_permission            |
| auth_user                  |
| auth_user_groups           |
| auth_user_user_permissions |
| django_admin_log           |
| django_content_type        |
| django_migrations          |
| django_session             |
+----------------------------+
10 rows in set (0.24 sec)```


