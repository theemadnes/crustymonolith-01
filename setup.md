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

```
mysql> use crustymonolith;
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
10 rows in set (0.24 sec)
```


Create models for polls app 

- note that each model subclasses `models.model`
- relationships are defined between models using `ForeignKey`


Create a migration the new models: `python manage.py makemigrations polls`

Show the SQL for the migrations:

`python manage.py sqlmigrate polls 0001`

```
BEGIN;
--
-- Create model Question
--
CREATE TABLE `polls_question` (`id` integer AUTO_INCREMENT NOT NULL PRIMARY KEY, `question_text` varchar(200) NOT NULL, `pub_date` datetime(6) NOT NULL);
--
-- Create model Choice
--
CREATE TABLE `polls_choice` (`id` integer AUTO_INCREMENT NOT NULL PRIMARY KEY, `choice_text` varchar(200) NOT NULL, `votes` integer NOT NULL, `question_id` integer NOT NULL);
ALTER TABLE `polls_choice` ADD CONSTRAINT `polls_choice_question_id_c5b4b260_fk_polls_question_id` FOREIGN KEY (`question_id`) REFERENCES `polls_question` (`id`);
COMMIT;
```

A quick health check: `python manage.py check`

Re-run migrate to enact changes: 

`python manage.py migrate`

Modifying the database in 3 steps:

- change models in `models.py`
- `makemigrations` to create migrations
- `migrate` to apply changes 

Shelling in to Django API: 

`python manage.py shell` 

Good exercise adding str representations to models:

```
def __str__(self):
    return self.question_text
```

Had to update / change reference to security key in settings 



