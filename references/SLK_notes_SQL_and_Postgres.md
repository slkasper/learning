# Postgres

I'm starting with postgres because when you start a project, setting up the database is one of (if not the) first thing you want to do.  For learning, you should learn SQL first then worry about setting up the databases, so skip there if you need it.  To that end, I put all the SQLite3 stuff there as well, since its mostly a learning tool, not something you will work with.

First thing to do is check that you have the elephant at the top of the scene open. If not, open Postgres on your machine.  You should be able to open the interface and see if your database is in there.  If it is, you can just double click on it and it will open a bash prompt in the postgres shell against that database.

Otherwise, start a new database. Start with a fresh bash terminal. If you are going to use a create.sql table to populate your database, be sure to cd into the correct folder location, or be prepared to type out the local folder address.

## Steps to setting up a postgres database

1. Start the Postgres shell.

```bash
#new prompt in postgres shell 
> psql
```
This will open the postgres shell. All commands now need to follow that language. I think if you want to add a bash command you use the bang `!`

Most commands start with a `\<word>` and end with `;`.  If you forget the `;`, it will never end the statement therefore it will never exectue!

 2. Before you start, drop any databases that have the same name.

Why? To make sure you're not doubling up your data. It's good to start fresh so you don't have any unwanted results.
```postgres
/* drop database */
Sonya=# drop database <name>;
```
If you don't see `DROP DATABASE` or `ERROR:  database "<name>" does not exist` after running this command, then you did something wrong. This one needs a response.  Check if you forgot the `;`

3. Then create a new database
```postgres
/* create new database */
Sonya=# create database "<name>";
```
If you don't see `CREATE DATABASE` or `ERROR:  database "<name>" already exists` after running this command, then you did something wrong. This one needs a response.  Check if you forgot the `;`

Oops, you're in the wrong database! or, like in this case, you just created one so you need to move into it now, use the `\c` command
```postgres
/* move to a diff database */
Sonya=# \c <name>
```
Your prompt should now look like this:
```postgres
/* new prompt in postgres shell */
<name>=# 

/* heres a real example */
imdb_development=# <put your postgres commands here>;
```
NOTICE: you did NOT need the `;` to change databases with the `\c` command in the postgres shell.  No idea why, but you didn't.

Now you have a database. You even learned how to change from one database to another. But your database is empty.  To fill it, you could manually build a table. But I don't know how to do that. I'm sure it's doable, but we've been just creating a .sql file with all the create table commands in it, then running that .sql file against the database.  

To do that, you need to exit the postgres shell.
```postgres
/* exit, or quit the postgres shell */
Sonya=# \q
```
Note that once again, the quit command, like the change database command does NOT need `;`

## Adding tables to Postgres database and navigating them
A database needs tables. Let's add some.

1. Create a 'create_tables.sql' outside of postgres
I'm not really going into the create table commands as we've only done it sparingly so I'm not exactly an expert.  Instead, please take a look at a sample like [create_table.sql found here](/Users/Sonya/Documents/Jigsaw/flask-orm-lab/backend/migrations/create_tables.sql)

2. In the terminal add use the create_tables.sql file to populate the database

This is pretty easy actually.  You want to be out of the postgres shell for this one. Be sure you know your file location so you can call the sql file correctly.  Here is your command line:
```bash
#add tables to a database
#psql -d <database> -f <name.sql>
> psql -d imdb_test -f create_table.sql 
```
Where `-d` is the database name flag and `-f` is the force file flag for the table addition.

That's it. You've added tables. You should see `CREATE TABLE` and `DROP TABLE` as many times as you created or dropped tables in your .sql file.

You now have empty tables in your database.  We will want to fill them, probably.  Test dadabases could stay empty so the fixtures can fill them with whatever is needed for tests, but in general, we probably want to fill our tables with info.

3. Add data to tables using .csv files

Easiest way to fill a table, is to have a csv that lines up with your table schema.  Use this copy command (you will need one `\copy` commmand per csv file/table):
```postgres
/* copy data from a csv file into a table from postgres shell

/* \copy <table_name> (column, names, list) FROM '/path/to/file.csv' DELIMITER ',' CSV HEADER;

/* example: */
\copy actors (id, names) FROM '/data/actors.csv' DELIMITER ',' CSV HEADER;
```

## Navigating and using existing tables in Postgres
Now that we have populated the database with tables and filled those tables with data, we will need to know how to navigate around to view the tables, their schema and even do sql select statements so we can trouleshoot to see if problems are happening at the database level or somewhere else.  

Navigating and selecting will be covered in this section.

I'm going to list the most important commands here cuz that's a good way to find them.
```bash
#from bash, open a postgres shell directly into a database
psql -d <name_database>

#from bash, open postgres shell in given db as a certain user
psql <name_database> -U <user_name>
```

```postgres
/* in postgres shell

/* check connection info  */
db=# \conninfo

/* example */
imdb_development=# \conninfo
You are connected to database "imdb_development" as user "postgres" via socket in "/tmp" at port "5432".

/* list of databases in current directory */
db=# \l

/* change db to current db with \c */
db=# \c <db_name>

/* see tables in current db with \dt */
db=# \dt

/* example */
imdb_test=# \dt
            List of relations
 Schema |      Name       | Type  | Owner 
--------+-----------------+-------+-------
 public | actors          | table | Sonya
 public | directors       | table | Sonya
 public | movie_actors    | table | Sonya
 public | movie_directors | table | Sonya
 public | movies          | table | Sonya
(5 rows)


/* show schema of table in current db with \d */
db=# \d <table_name>

/* example */
imdb_test=# \d actors
                                    Table "public.actors"
 Column |          Type          | Collation | Nullable |              Default               
--------+------------------------+-----------+----------+------------------------------------
 id     | integer                |           | not null | nextval('actors_id_seq'::regclass)
 name   | character varying(250) |           |          | 
Indexes:
    "actors_pkey" PRIMARY KEY, btree (id)


/* show table info using sql search terms */
db=# SELECT * FROM <table_name> LIMIT 3;

/* example */
imdb_development=# SELECT * FROM actors LIMIT 3;
 id |      name       
----+-----------------
  0 | Brendan Fraser
  1 | Brett Granstaff
  2 | Leslie Nielsen
(3 rows)
``` 

To make changes to postgres without entering the postgres shell use -c
like this (this is from a docker line):

```bash
docker exec -it friendly_chatelet psql -c "\dstates" -U jigsaw

```

The above uses the psql flag -c, for command, to call 'dstates' for display the states table from the command line, and specifies the user as jigsaw.

Same pattern, but now lets display tables:
```bash
docker exec -it friendly_chatelet psql -c "\dt" -U jigsaw
```

## Connecting to Postgress DB using Python

Let's take all this data we just stored and use it in Python.

You need psycop2 library to connect the 2 programs.
If you have to, do a pip install:

```bash
pip install psycopg2
```
To connect the code to the db, you need a connections. Let's dive into that.
### Connection
I couldn't explain this, cuz I wasn't clear myself, so I found this from https://www.postgresqltutorial.com/postgresql-python/connect/:

    The connect() function creates a new database session and returns a new instance of the connection class. By using the connection object, you can create a new cursor to execute any SQL statements.

    To call the connect() function, you specify the PostgreSQL database parameters as a connection string and pass it to the function like this:

```python
conn = psycopg2.connect("dbname=suppliers user=postgres password=postgres")
 ```
    Or you can use a list of keyword arguments:

```python
conn = psycopg2.connect(
    host="localhost",
    database="suppliers",
    user="postgres",
    password="Abcd1234")
```
NOTE!!! I found that when setting this up for Flask and you're using the whole `app.config` stuff, the keywords (the left side of the arguments) **MUST** be the following (_**NOT** what is shown above!_) or it won't work:
```python
import psycopg2

conn= psycopg2.connect(dbname = app.config['DATABASE'],
                       user = app.config['DB_USER'], 
                       password = app.config['DB_PASSWORD']
                    )
```
*** ^^^THIS IS SUPER IMPORTANT. IT TOOK HOURS TO GET MY FLASK GOING UNTIL I TRYED THIS!!!^^^***

see the 

### cursor

#### fetchall() -> list with a single tuple with many items
#### fetchone() ->list with a single tuple with one item


