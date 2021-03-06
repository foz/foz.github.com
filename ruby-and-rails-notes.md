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

    * query_trace
    * acts_as_slugable 
    * betternestedset 
    * geokit
    * paginating_find
	* superdeploy

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

To install a specific branch of a github plugin
-----------------------------------------------

	$ script/plugin install http://github.com/activescaffold/active_scaffold.git -r rails-2.3

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
	$ find . -name "*helper.rb" | xargs mate

Regex Shortcut
--------------

Given a string `text`:

    text = "file no 1234"

You can use a regex in several ways in Ruby. The "perl-ish" way is with `~=` :

		text =~ /file no (\d+)/
		found = $1  # => "1234"

Another way is using `match()`, which returns `MatchData` objects:

		text.match(/file no (\d+)/).matches[0]  # =>  "1234"
		
However, if text could be nil

Still another way to extract matches is using the `[]` method and a regex:

		text.to_s[/file no (\d+)/, 1]  # =>  "1234"
		
Using `to_s` ensures that if `text` is `nil` you won't get an exception.

But, perhaps the cleanest way is with `scan`:

    test.scan(/file no (\d+)/)  # =>  "1234"
    
Also, `scan` works with multiple matches where others do not:

    text = "test here"
    
    text.match(/(test|here)/)
    => #<MatchData "test" 1:"test">  # ONE match
    
    text[/(test|here)]  # => ONE match
    
    text.scan(/(test|here)/)   # => TWO matches: ["test", "here"]
    
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
	$ sudo gem install rmagick -v 2.12.2
	
Test it (using a file called 'mars.jpg', can be any image):

	$ irb
	>> require 'rubygems'
	=> false
	>> require 'RMagick'
	=> true
	>> i=Magick::ImageList.new('mars.jpg')
	=> [mars.jpg JPEG 2400x2400 2400x2400+0+0 DirectClass 8-bit 333kb]
	>> i.scale!(0.1)
	=> mars.jpg=>test.png JPEG 2400x2400=>240x240 240x240+0+0 DirectClass 8-bit 2982kb
	>> i.write('test.png')
	=> [mars.jpg=>test.png JPEG 2400x2400=>240x240 240x240+0+0 DirectClass 16-bit 155kb]
	>> exit
	
	$ identify test.png
	test.png PNG 240x240 240x240+0+0 DirectClass 16-bit 155.221kb
	
Ruby Pitfall
------------

A common Ruby pitfall...

	>> i = b > a and b < c
	false
	>> i
	true

fix: 

	>> i = (b > a and b < c)
	false
	>> i
	false
	
Migrations
----------

To make a BIGINT column:

	t.integer :author_id, :limit => 8	
	
		
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

Mocha
-----

In some cases under Rails 2.3 with Bundler, Mocha will not load right. This results is complaints about "Undefined method `mock`" etc. The solution is to have this in Gemfile:

	gem 'mocha', '0.9.8', :require => false
	
The `require => false` bit prevents Mocha from loading until the test framework loads (which means `require 'mocha'` should be in `test_helper.rb`).


Internals
---------

## What launched rails?

ENV has:

	>> ENV['_']
	'script/server'
	or
	'/usr/bin/irb'
	
You can also check `caller[-1]` which will be the first program on the call stack. This might give you `script/server:3` or it may be something less obvious like `"/System/Library/Frameworks/Ruby.framework/Versions/1.8/usr/lib/ruby/1.8/irb/workspace.rb:52"`.

If you want to know if the server is running, you can check for:

	ENV['RACK_ENV'].present?
	
# Bundler

	$ bundle config build.mysql --with-mysql-config=/usr/local/mysql/bin/mysql_config

# ActionView::MissingTemplate: Missing template

Add this to `application.rb` to handle errors with "smart" format detection.

  	config.action_dispatch.ignore_accept_header = true  

## Array Fun

Reduce:

	>> (1..33).reduce([]){|r,v| r.tap{ r << v if v % 2 == 0} }
	=> [2, 4, 6, 8, 10, 12, 14, 16, 18, 20, 22, 24, 26, 28, 30, 32]