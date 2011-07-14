---
title: Javascript Notes
layout: default

---

# Javascript Tricks and Patterns

## Good Tools

[Firebug](http://getfirebug.com/) makes js development much easier. [FireQuery](http://firequery.binaryage.com/) makes jQuery development rock.

## Plugins

Some of my favorite jQuery plugins:

* [facebox](http://chriswanstrath.com/facebox/) - a lightbox plugin that looks and works like the Facebook site does. Has a really nice API and callback/event hooks. From the genius/madness of Chris Wanstrath.
* [FullCalendar](http://arshaw.com/fullcalendar/) - the mother of all calendar plugins, day/week/month, editing, remote json, timezones, the works.
* [prettyCheckboxes](http://www.no-margin-for-errors.com/projects/prettyCheckboxes/) - nice styling of HTML checkbox elements, customizable.
* [jQuery hotkeys](https://github.com/jeresig/jquery.hotkeys) - fork by John Resig, lets you easily assign keyboard shortcuts to your web app.
* [jQPlot](http://www.jqplot.com/) - charts, graphs, etc. with loads of options, plugins, and variations. Flexible API.
* [jCrop](http://deepliquid.com/content/Jcrop.html) - for drawing rectangular regions and storing the coordinates.
* [timeEntry](http://keith-wood.name/timeEntry.html) - makes a smart time input box with minutes/hours/am-pm, set steps and validation filters.
* [jQuery Star-Rating](http://www.fyneworks.com/jquery/star-rating/) - Transforms radio buttons into a nice-looking interactive widget for ranking/rating. 
* [blockUI](http://malsup.com/jquery/block/) - present a modal dialog and disable/dim the underlying page. Useful for error messages, dialogs, or progress indicators.
* [jHtmlArea](http://jhtmlarea.codeplex.com) - as far as WYSIWYG editors go, this one is pretty basic. But that can be a good thing. Unlike more popular editors, this one is more hackable. The project is not well maintained any more, and the API is goofy, but it's good for simple things and easy to get going.

## Avoid console.log

Calling `console.log` works great for debugging with Firebug or the Safari Developer Tools, but in production, it can kill IE and other browsers with a javascript error. It's all too easy to forget to remove those log messages before deploying an update. So I've started using a global debug function instead:

{% highlight js %}
function debug(){
	if (typeof console != "undefined"){
		if (console.log){
			if (arguments.length == 1){
				console.log(arguments[0]);
			} else {
				console.log(arguments);
			}
		}
	}
}
{% endhighlight %}

You can pass any number of arguments to `debug()` and they will print nicely. In production the code won't blow up.

## A Generalized Class (a.k.a. "Module Reveal Pattern")

{% highlight js %}
var myClass = function(opts){
	var options = opts;

	var inspect = function(){
		console.log('A person named '+options.name+' is '+options.age+' years old.');
	}

	return {
		inspect: inspect,
		test: function(){ alert('test'); },
		name: function(){ return options.name; }
	};
};
{% endhighlight %}

Using this class would work like this:
{% highlight js %}
var c = new myClass({name: "Joe", age: 26});
var str = c.inspect();   // "A person named Joe is 26 years old."
{% endhighlight %}

## Javascript weirdness

Null is weird:

	>> null >= 0
	true
	>> null > 0
	false
	>> null <= 0
	true
	>> null < 0
	false
	>> null >= -1
	true
	>> null >= 1
	false
	>> null == 0
	false
	>> null == false
	false
	
Equality (==) can be misleading. It doesn't work the same way as in languages, it does type conversions before the compare. You must use === to be certain:

	>> "123" == 123  
	true
	>> 1 == true
	true
	>> 2 == true
	false
	>> 0 == true
	true
	>> "123" === 123
	false
	>> 1 === true
	false

## Using regular expressions

Sometimes its simpler to extract values with a regexp as the first part of an assignment:

{% highlight js %}
	var url = /(\d+)\/?$/.exec($('#flickr_url').val());
{% endhighlight %}
	