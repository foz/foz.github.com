# Javascript Tricks and Patterns

## Use Good Tools

Firebug makes js development much easier. FireQuery makes jQuery development rock.

## Use Smart Debugging

Calling `console.log` works great with Firebug or the Safari Developer Tools, but it will kill IE and other browsers with a javascript error. Use a debug function instead:

	function debug($obj) {
	     if (window.console && window.console.log){
	          window.console.log($obj);    
	     }
	};
	
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

