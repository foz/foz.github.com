---
title: MySQL Notes
layout: default

---

# MySQL

## General

Creating a new user/database

	mysql> create database dbname

	mysql> grant all on dbname.* to user@localhost identified by 'password';
	
	mysql> flush privileges

Adding an index

	mysql> alter table stats_popularity add index(cat,object_id);

Set root password for first time after mysql-server install:

	$ mysqladmin -p -u root password 'newpassword'
	(hit enter on the password prompt)
	
See table info
	
	$ show create table shows\G

Pipe through pager

	$ \P less
	
## MySQL Tuning


### Table Types

    InnoDB: 
        pros: default table type for rails. row level locks, transactions, more reliable.
        cons: a bit slower, slower writes, more disk space
        
    MyISAM:
        pros: fast writes, better performance, each table is in it's own file
        cons: table-level locking, bottlenecks, tables can get corrupted
    
    Conclusion - use MyISAM for logs, cache, or temp tables. InnoDB for everything else.
    
### Server Tuning
    
There's a lot of strong opinions about how to tune settings. After much research and testing, I've determined these are the important ones:

    innodb_buffer_pool_size: go big
    innodb_flush_method=O_DIRECT # testing this, said to help
    key_buffer_size: go big
    table_cache = 1024 (if you need lots of open tables)
    sort_buffer_size, read_buffer_size: 8M

_Note: when you change innodb table settings, the tables might get messed up on the next restart if your settings are wrong. So... backup!_

Adding these settings will help you find really slow queries that need optimization, for example, those that take more than 2 seconds to execute:

	# logging
	log_error = /usr/local/var/log/mysql/error.log
	log_slow_queries = /usr/local/var/log/mysql/slow-query.log
	long_query_time = 2
    
_Don't leave slow query logging turned on in a production environment!_
        
## MySQL defaults

It's annoying to have to specify a username/password every time you start up mysql (or utilities, like mysqldump). To get around this, just make a config file in your home dir:

	create ~/.my.cnf and add:

	[client]
	user=foz
	password=xxxx

## A simple backup script

This BASH script will backup all MySQL databases on the local machine as separate dump files, with the date as part of the filename. It skips the internal tables (information_schema, database). It should work on Mac or Linux environments. I run this script before I do anything potentially destructive. It also removes empty backups and those that are more than 90 days old:

<script src="http://gist.github.com/549062.js"> </script>

Also available as a [gist](http://gist.github.com/549062).