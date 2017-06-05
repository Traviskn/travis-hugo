+++
date = "2016-06-18"
title = "Node.js Api Tutorial Part 2"
tags = ["javascript", "node", "tutorial"]
series = ["Node API Tutorial"]
draft = "true"
+++

Our demo API doesn't yet have the capability to store and retrieve data.  In order
to do that, we will need a database.  We will also need to define the kind of data
that our API will work with.  Our goal in this part of the tutorial is to create
our app's 'model layer', or 'data layer'.  Let's get started!

## Installing Our Database

There are many popular database options to choose from in the world of web
development, but I have decided to go with a relational SQL database.  Relational
databases are known for reliability and maintaining the integrity of your data.
Designing your data in a relational way can sometimes feel restrictive, but there
are many benefits that come from following a SQL database's design rules.

There are still many options even just among SQL databases.  In this tutorial we
will be using [PostgreSQL](https://www.postgresql.org/), which calls itself
'the world's most advanced open source database'.  I have enjoyed using PostgreSQL
in the past, and have found that is actually does live up to it's big promises!
You can install it from the [official website](https://www.postgresql.org/download/).
If you are on a Mac, I actually recommend using the [Postgres App](http://postgresapp.com/)
as it is much easier to manage.

Once you have PostgreSQL installed, in order to use its command line tools you
will need to be sure that it is added to your path.  On Mac, assuming you installed
 the [Postgres App](http://postgresapp.com/), add the following line to your ~/.bash_profile:

```shell
export PATH=$PATH:/Applications/Postgres.app/Contents/Versions/latest/bin
```

Remember to close your terminal and reopen it for the new path to take effect.

On Windows you will need to go through the following menus:
```
-> Control Panel
  -> All Control Panel Items
    -> System
      -> Advanced System Settings
        -> Environment Variables
```

Then from the System Variables box select "PATH" and edit it.  Be careful not to
delete any of the existing path!  Add the following to the end of it:

```
;C:\Program Files\PostgreSQL\9.5\bin
```

This is assuming you have version 9.5. Change the version number accordingly if
you installed a different version.  You may need to restart your computer for the
new path to take effect.

You will know that you have PostgreSQL installed correctly if you can run the
following from your command line:

```shell
$ psql
```

You should see a new line with your username and a # symbol at the end.  Exit the
prompt by typing '\q'.

If you see an error that the command is not found, then you need to be sure that
PostgreSQL is added to your path.

If you see an error that says you could not connect to the server, you need to be
sure that PostgreSQL is started and running.

There are many things that could potentially go wrong during the installation of
our database, and it would be impossible for me to predict and troubleshoot every
situation. Luckily, PostgreSQL is a popular database with a helpful community.
If you run into problems, Google is your friend!  You should be able to find helpful
installation instructions online.

## Connecting Our API to the Database

We will be using a library called [Bookshelf](http://bookshelfjs.org/) to work
with our database.  Bookshelf is a type of library called an Object Relational
Mapper, or ORM.  It will help match our JavaScript objects up to tables of data
in our SQL database. Open up your command line in the sample_app directory and
install Bookshelf with NPM:

```shell
$ npm install --save bookshelf
```

Inside the server folder, create a new folder called 'db'.  This folder will store
our database configuration code.
