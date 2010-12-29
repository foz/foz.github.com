---
title: Javascript Notes
layout: default

---

# Javascript Tricks and Patterns

## Use Good Tools

[Firebug](http://getfirebug.com/) makes js development much easier. [FireQuery](http://firequery.binaryage.com/) makes jQuery development rock.

## Avoid console.log

Calling `console.log` works great with Firebug or the Safari Developer Tools, but it will kill IE and other browsers with a javascript error. And it's easy to forget to remove them before updating a web site. So I've started using a global debug function instead:

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

This lets you pass any number of arguments to `debug()` and it will print nicely. In production the code won't blow up.

## Generalized Class (a.k.a. "Module Reveal Pattern")

{% highlight js %}
var myClass = function(opts){
	var options = opts;

	var inspect = function(){
		debug('A person named '+options.name+' is '+options.age+' years old.');
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

# Javascript weirdness

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

