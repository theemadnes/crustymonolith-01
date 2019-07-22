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

Mapping an app to a given project means updating the project `settings.py` to reference the new app. 


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

Now time to create a superuser, and see if we can actually commit these contents to our repo:

`python manage.py createsuperuser`

Seems to be stored in the db. Looking for it...
```
mysql> select * from auth_user;
+----+--------------------------------------------------------------------------------+----------------------------+--------------+----------+------------+-----------+------------------------+----------+-----------+----------------------------+
| id | password                                                                       | last_login                 | is_superuser | username | first_name | last_name | email                  | is_staff | is_active | date_joined                |
+----+--------------------------------------------------------------------------------+----------------------------+--------------+----------+------------+-----------+------------------------+----------+-----------+----------------------------+
|  1 | ------------------------------------------------------------------------------ | 2019-07-19 05:12:09.984783 |            1 | -----    |            |           | ------------@gmail.com |        1 |         1 | 2019-07-19 05:11:28.361809 |
+----+--------------------------------------------------------------------------------+----------------------------+--------------+----------+------------+-----------+------------------------+----------+-----------+----------------------------+
1 row in set (0.03 sec)
```

Updated admin for `polls` to allow administrative modification 

Now moving on to [part 3](https://docs.djangoproject.com/en/2.2/intro/tutorial03/) of the tutorial.

Focusing on views... Django's mechanism for delivering web pages. 
```
To get from a URL to a view, Django uses what are known as ‘URLconfs’. A URLconf maps URL patterns to views.
```

Views detail:

```
Each view is responsible for doing one of two things: returning an HttpResponse object containing the content for the requested page, or raising an exception such as Http404. The rest is up to you.

Your view can read records from a database, or not. It can use a template system such as Django’s – or a third-party Python template system – or not. It can generate a PDF file, output XML, create a ZIP file on the fly, anything you want, using whatever Python libraries you want.
```

A few has a specific function that's mapped to a template, and is represented by a Python function or method. A `URLconf` is used to map a specific URL path to a view. 

Templates are an approach to avoid hard-coding page content in Python code. 

Why template namespacing is important:

```
Now we might be able to get away with putting our templates directly in polls/templates (rather than creating another polls subdirectory), but it would actually be a bad idea. Django will choose the first template it finds whose name matches, and if you had a template with the same name in a different application, Django would be unable to distinguish between them. We need to be able to point Django at the right one, and the easiest way to ensure this is by namespacing them. That is, by putting those templates inside another directory named for the application itself.
```

On to [part 4](https://docs.djangoproject.com/en/2.2/intro/tutorial04/)...





