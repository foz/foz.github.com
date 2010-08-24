---
title: Stupid Shell Trucks
---

Stupid Shell Tricks
===================

Find all the ip addresses that hit a certain page

Assuming you are checking your access log, this is what you would do:

	$ grep "GET /admin" access_log | perl -e 'for(<>){($ip)=split;$l{$ip}++;}for(keys %l){print "$_: $l{$_}\n"}' | more


Check locale is OK

	perl -mLocale -e ''

Rename a bunch of files

 ls -1 *find_string*|while read file
 > do
 > target=$(echo $file | sed -e "s/find_string/replace/")
 > mv "$file" "$target"
 > done

Install App::Ack via cpan and enjoy the awesome 'ack' utility for grepping source files :) Or, install the ruby gem 'rak'...

Make an application shut up

	$ /usr/bin/app > /dev/null 2>&1

See what commands you are using most often:
	
	$ hash

See the full command-line for a give PID:

    $ cat /proc/18295/cmdline | tr '\0' ' ' ; echo

