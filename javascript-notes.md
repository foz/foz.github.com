# Javascript Tricks and Patterns

## Use Good Tools

Firebug makes js development much easier. FireQuery makes jQuery development rock.

## Use Smart Debugging

Calling `console.log` works great with Firebug or the Safari Developer Tools, but it will kill IE and other browsers with a javascript error. I use a debug function instead:

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
	
## Generalized Class (a.k.a. "Module Reveal Pattern")

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

Using this class would work like this:

	var c = new myClass({name: "Joe", age: 26});
	var str = c.inspect();   // "A person named Joe is 26 years old."

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
	
Equality is misleading. It doesn't work the same way as in languages like Ruby or PHP. Use === to be certain:

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
	