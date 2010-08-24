---
title: Stupid Shell Trucks
layout: default

---

Stupid Shell Tricks
===================

Find all the ip addresses that hit a certain page

	Assuming you are checking an standard Apache-style access log, this is what you would do:

	$ grep "GET /admin" access_log | perl -e 'for(<>){($ip)=split;$l{$ip}++;}for(keys %l){print "$_: $l{$_}\n"}' | more

Check locale is OK

	perl -mLocale -e ''

Rename a bunch of files

	ls -1 *find_string*|while read file
	> do
	> target=$(echo $file | sed -e "s/find_string/replace/")
	> mv "$file" "$target"
	> done

Install App::Ack via cpan and enjoy the awesome 'ack' utility for grepping source files :) 
Or, install the ruby gem 'rak'...

	$ sudo gem install rak
	
	$ rak "some code"
	$ rak "some code" --js  # only js files!

Make an application shut up

	$ /usr/bin/app > /dev/null 2>&1

See what commands you are using most often:
	
	$ hash

See the full command-line for a given PID:

    $ cat /proc/18295/cmdline | tr '\0' ' ' ; echo

Pick a random line from a file:

	$ perl -e 'srand; rand($.) < 1 && ($line = $_) while <>; print $line;' filename.txt
