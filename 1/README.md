# 1: Querying data from a single table

The first section of the workshop is dedicated to querying data from a single table. By the end you will know how to do basic querying and exploratory analysis in PostgreSQL, picking up a few tricks in `psql` (the official Postgres command line query interface) along the way.

We will use the table created by [the etl scripts in this project](../etl) as an example. In code examples below and in this folder, we will call the table `denormalized_courses`, but that table will have a different name in your database depending on if/when you created your own extracted and loaded dataset with the ETL (Extract-Transform-Load) code.


The data itself, a Fall '21 course schedule straight from KU's course explorer at classes.ku.edu, is **unnormalized**: a single, "wide" table, sometimes even with multiple values in a single row/column (see the `instructor` column). At the end, we'll use what we've learned in order to create a few tables storing the same data in first normal form. To learn more about what this means, you can read the unusually helpful Wikipedia article: <https://en.wikipedia.org/wiki/Database_normalization>

## Kicking the tires on `psql`

First, let's make sure we have dependencies installed!

### psql (part of Postgres installations)

https://www.postgresql.org/download/

```bash
# Should be at least version 12.5 to follow along
psql --version
# psql (PostgreSQL) 13.2
```

### platter

```bash
# May require security enablement on MacOS: https://github.molgen.mpg.de/pages/bs/macOSnotes/mac/mac_procs_unsigned.html
platter --version
# platter 0.7.2
```

### What is `psql`?

`psql` is the standard command line interface for exploring and querying Postgres databases.

```bash
psql --help
# ... lots of options
```

It lets you quickly log into any postgres database then interactively explore, query, and modify its data. While you can use other tools such as pgAdmin if you want a graphical tool, learning `psql` is very useful, as it's often the quickest and simplest way to do routine tasks or write scripts to automate database maintenance.

### What is `platter`?

Platter is the service my company works on: Serverless Postgres with Superpowers (<https://platter.dev>). We'll use it today mostly because 1) it creates and connects to Postgres instances very quickly, and 2) databases on the shared cluster are currently free because we turned off billing for a few hackathons we're teaching at :)

```bash
platter postgres create workshop-db
# boom, new DB
```

```bash
platter postgres branch url master --instance workshop-db
# prints out a connection string (`postgres://[...]`) that we can use with psql to attach to this database
```

You've just done the same work that can take you hours in a cloud provider: creating a new database cluster, selecting version and region, picking from dozens of params that only make sense if you're a DBA (database administrator), configuring it for secure-only access, creating a non-superuser Postgres user to use the database without big security risks, etc.

### Let's explore some data!

The dataset we're exploring is KU's course schedule for the Fall '21 semester, pulled live from the web to show you how you might build a lightweight set of scripts to use real-world datasets in your own projects. I hacked it together last weekend just like I would in a POC (proof-of-concept) at work: just enough structure/commenting to turn it into a real project later if it proved my team needed to maintain it later :). The scripts are in [](../etl).

Once you've run those three scripts, named for the three standard phases of most data engineering and data science prep tasks (ETL: extract, transform, and load), you'll have a single, long table in your database with one row for each course in the Fall '21 schedule.

Let's explore it a bit!

Log in to your new Platter Postgres database with psql:

```bash
psql $(platter postgres branch url master --instance workshop-db)
```

Running the `\?` command will show you all of `psql`'s shortcut commands. These are a special layer of commands on top of normal SQL, designed to let you quickly explore and perform routine tasks at the command line without writing full queries. (If you are interested to learn how they are implemented, quit and relaunch `psql`, this time adding the `-E` flag. This will print out the underlying system query that psql abbreviates with the short command. A lot of Postgres is implemented this way: as layers on top of underlying implementations/primitives that you can dive deeper into when you want to learn more.)

```text
(database-name)=> \?
```

The above code block is what your prompt will look like; from here forward we'll omit the prompt for the database name.

You can see what tables are available with `\d`:

```sql
\d
-- a list of YOUR tables
```

To see all the tables, including system ones, add `S`:

```sql
\dS
-- a list of ALL tables
```

Let's see what's in our ETL'd table:

```sql
\d denormalized_courses
```

Quick note here: if you have run the ETL process from the beginning with live data, your `denormalized_courses` table will have a Unix epoch timestamp on the end to document exactly when that data was downloaded. This sort of timestamping is often done in data processes to keep track of datasets that change over time, as indeed KU's course schedule might! For your querying purposes, typing in `den` then hitting `<TAB>` will probably be the easiest way to refer to your particular table, especially if you have only one course dataset in your database.

Here's my output from the above command:

```text
    Table "public.denormalized_courses_1617589157"
    Column     | Type | Collation | Nullable | Default 
---------------+------+-----------+----------+---------
 course        | text |           |          | 
 number        | text |           |          | 
 course_nbr    | text |           |          | 
 course_title  | text |           |          | 
 course_topic  | text |           |          | 
 class_nbr     | text |           |          | 
 sec_nbr       | text |           |          | 
 min_hrs       | text |           |          | 
 max_hrs       | text |           |          | 
 seats_avl     | text |           |          | 
 total_enrl    | text |           |          | 
 enroll_cap    | text |           |          | 
 acad_career   | text |           |          | 
 component     | text |           |          | 
 consent       | text |           |          | 
 enrollable    | text |           |          | 
 instructor    | text |           |          | 
 start         | text |           |          | 
 end           | text |           |          | 
 meeting_days  | text |           |          | 
 begin_date    | text |           |          | 
 end_date      | text |           |          | 
 location      | text |           |          | 
 room          | text |           |          | 
 room_cap      | text |           |          | 
 wait_cap      | text |           |          | 
 wait_total    | text |           |          | 
 comb_sect_id  | text |           |          | 
 cs_enrl_cap   | text |           |          | 
 cs_enrl_total | text |           |          | 
 cs_wait_cap   | text |           |          | 
 cs_wait_total | text |           |          | 
```

A few things to note:

1) our ETL script added everything as plain text (not ideal)
2) this is a WIDE table

We'll fix this below when we normalize this dataset, but first let's explore the data itself a bit.

(Lots of interactive querying here based on people's questions about the data)

### Let's normalize this database

(Lots of interactive querying here too!)
