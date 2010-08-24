---
title: MySQL Notes
layout: default

---

General
-------

Creating a new user/database

	mysql> create database dbname

	mysql> grant all on dbname.* to user@localhost identified by 'password';

this next line might be required for compatibility between mysql 4.1 and older apps

	mysql> set password for user@localhost = OLD_PASSWORD('password');

Adding an index

	alter table stats_popularity add index(cat,object_id);

Set root password for first time after mysql-server install:


	$ mysqladmin -p -u root password 'newpassword'
	(hit enter on the password prompt)
	
See table info
	
	$ show create table shows\G

Pipe through pager

	$ \P less
	
Mysql Tuning
------------

Table Types

    InnoDB: 
        pros: default table type for rails. row level locks, transactions, more reliable.
        cons: a bit slower, slower writes, more disk space
        
    MyISAM:
        pros: fast writes, better performance, each table is in it's own file
        cons: table-level locking, bottlenecks, tables can get corrupted
    
    Conclusion - use MyISAM for logs, cache, or temp tables. InnoDB for everything else.
    
my.cnf tunings
    
    innodb_buffer_pool_size: go big
    innodb_flush_method=O_DIRECT # testing this, said to help
    key_buffer_size: go big
    table_cache = 1024 (if you need lots of open tables)
    sort_buffer_size, read_buffer_size: 8M

	# logging
	log_error = /usr/local/var/log/mysql/error.log
	log_slow_queries = /usr/local/var/log/mysql/slow-query.log
	long_query_time = 2
    
    
Note: when you change innodb table settings, the tables might get messed up on the next restart. So, backup!
    
mysql defaults for user account
-------------------------------

	edit ~/.my.cnf:

	[client]
	user=foz
	password=xxxx
