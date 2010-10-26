---
title: Ruby and Rails Notes
layout: default

---

Ruby and Rails Notes
====================


Updating RubyGems:
------------------

	$ sudo gem update --system

    $ sudo gem install rubygems-update

    $ sudo update_rubygems


Adding GitHub to RubyGems:
--------------------------

    $ gem sources -a http://gems.github.com

Mysql gem under OSX:
--------------------

Install:

	$ sudo gem install mysql -- --with-mysql-config=/usr/local/mysql/bin/mysql_config

	Or, 64-bit:
	$ sudo env ARCHFLAGS="-arch x86_64" gem install -V mysql -- --with-mysql-config=/usr/local/mysql/bin/mysql_config

	Or, with MacPorts, mysql5 and mysql5-devel installed, this might work (or try 64-bit)
	$ sudo gem install -V mysql -- --with-mysql-config=/opt/local/bin/mysql_config5

testing it:

	$ irb
	irb(main):001:0> require 'rubygems'
	=> false

	irb(main):002:0> require 'mysql_api'
	=> true

	irb(main):003:0> Mysql.get_client_info 
	=> "5.1.34"

	irb(main):004:0> Mysql.get_client_version
	=> 50134
  
Rest and Nested Routing
-----------------------

	map.resources :categories do |categories|
	  categories.resources :products
	end

	You can continue to nest as much as you like. To access this you simple 
	use the following URL structure:

	/categories
	/categories/5
	/categories/5/products
	/categories/5/products/6

How to turn an Array or Object into a Hash:
-------------------------------------------

	a = [1,2,3,4]
	Hash[*a]
	=> { 1 => 2, 3 => 4 }	

	a = [1, 2, 3]
	Hash[*a.collect { |v| [v, v*2]}.flatten]
	=> { 1 => 1, 2 => 4, 3 => 6 }

	class Event
	  attr_accessor :year, :month, :day
	  def date_hash
	    Hash[*[:year,:month,:day].collect{|k| [k=>self.send(k)] }.flatten]
	  end
	end
	
	c = Event.new({:year=>2007,:month=>7,:day=>4}).date_hash
	=> {:year => 2007, :month => 7, :day => 4}
	
Using splat
-----------

	# For parameters in a method definition:

	def myfunc(*parms)
		# params will be an array, or empty array if nothing sent
	end

	# for assignment

	a, *b = "1,2,3,4".split(/,/)  # a == [1], b == [2,3,4]		

Hash initialization
-------------------

	# default to static values, instead of nil

	h = Hash.new(0)
	h[:test] # returns 0
	
	# use a function!
	h = Hash.new{|k,v| h[k] = "#{k} rocks"}
	h["hashes"]  # value is "hashes rock"

Profile your tests
------------------

	rake log:clear; PROFILE=1 rake; ./script/profile_logs

Favorite Plugins
----------------

    * gibberish
    * query_trace
    * acts_as_slugable 
    * acts_as_state_machine
    * attachment_fu 
    * betternestedset 
    * debug_view_helper
    * geokit
    * paginating_find

Script/Console Tricks
---------------------

	# mess w/ routes
	>> include ActionController::UrlWriter
	>> default_url_options[:host]="example.com"
	>> comments_url("john","photo", 345)
	=> 'http://example.com/comments/photo/345/john'

Using google analytics from ajax
--------------------------------

	/* js */
	function trackPageHit(path){
		pageTracker._trackPageview(path); 
	}

	# in RJS
	page.call 'trackPageHit', item_path(@item) # or any relative url

To install a specific version of a gem:
---------------------------------------

	$ sudo gem install rails -v 1.2.6

To generate a rails app using a specific installed gem version:
---------------------------------------------------------------

	$ rails _1.2.6_ myapp

Note that this works with all gem binaries:

	$ cap --version
	Capistrano v2.2.0

	$ cap _2.1.0_ --version
	Capistrano v2.1.0


Finding Code
------------
	
    # open all your global helpers in all projects
	$ find . -name application_helper.rb | xargs mate

Regex Shortcut
--------------

	You can use a regex in several ways in ruby. The "perl-ish" way
	is with ~= :

		text =~ /file no (\d+)/
		found = $1

	Another way is using match(), which returns MatchData objects:

		text.match(/file no (\d+)/).matches[0]

	Perhaps the easiest way to extract matches is using the [] method and a regex:

		text.to_s[/(\d+)-(\w+)\s+min/i, 2]  # index 2 returns the (\w+) part.
		
	Using to_s ensures that if text is nil you won't get an exception.

Fix Readline, forward-delete and ~ (tilde) problem on OSX Snow Leopard:
-----------------------------------------------------------------------

	# via http://snippets.aktagon.com/snippets/387-How-to-fix-irb-in-OSX-Snow-Leopard
	
	$ sudo port install readline +universal
	$ svn co http://svn.ruby-lang.org/repos/ruby/tags/v1_8_7_72/ext/readline/ readline
	$ curl http://pastie.textmate.org/pastes/168767/download | patch readline/extconf.rb
	$ cd readline
	$ ruby extconf.rb
	$ make
	$ sudo make install
	
Rmagick
-------

Version 2.13 is most stable, in my testing. It requires ImageMagick 6.
	
Install rmagick on OSX (be patient, the install can take 20m or more on a reasonably fast mac)

	$ sudo port install ImageMagick @6.6.1-5_0+q16
	$ sudo gem install rmagick -v 2.13.1
	
Install rmagick on debian:

	$ sudo aptitude install libmagick++9-dev
	$ sudo gem install rmagick -v 2.13.1
	
Ruby Pitfall
------------

A common Ruby pitfall...

	>> a,b,c=1,4,2
	[1, 4, 2]
	>> i = b>a and b<c
	false
	>> i
	true

fix: 

	>> (b>a and b<c)
	false
	>> i
	false
	
Caching Tips 
------------

Via [Rockstar Memcaching](http://www.infoq.com/presentations/lutke-rockstar-memcaching):
Uses a generation number per user for content (their public pages).

	class ApplicationController < ActionController::Base
		before_filter :load_shop
		after_filter :increment_generation
		
		def load_shop
			@current_shop = Shop.find_by_host(request.host)
		end
		
		def increment_generation
			return if request.get?
			@current_shop.increment_generation
		end
	end	
	
	class MyController < ApplicationController::Base
		around_filter :cache
		
		def index; end
		
		def cache
			if content = Rails.cache.get(cache_key)
				render :text => content, :content_type => :html
			else
				yield
				Rails.cache.set(cache_key, @response.body) if @response.code == 200
			end
		end
		
		def cache_key
			"#{request.url}/#{current_shop.generation}/#{cart.size}"
		end
	end
