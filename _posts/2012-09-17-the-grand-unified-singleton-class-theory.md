---
layout: post
title: The grand unified singleton class theory
date: '2012-09-17T00:44:00.000+02:00'
tags:
- Programming
- Ruby
modified_time: '2012-09-17T00:44:53.856+02:00'
blogger_id: tag:blogger.com,1999:blog-695972570119037880.post-4853716816472374882
blogger_orig_url: http://hirnerweichung2null.blogspot.com/2012/09/the-grand-unified-singleton-class-theory.html
---

One of the hardest things to understand in Ruby (beside groking blocks and syntax like in this [LIST_ITEMS_FOR example](http://viget.com/extend/practical-uses-of-ruby-blocks), which deserves a blog post of its own) is all the fuzz about meta/eigen/singleton classes. I've read dozens of blog posts, the "Metaprogramming Ruby" book, dozens of explanations on stackoverflow and all the information I can get from now 5 (soon to be 6) books on Ruby programming in general. And guess what: My confusion has grown ever since, till today. I think one of the culprits is the changing terminology. If it's unclear what the authors are talking about exactly, it's getting much more confusing for the one who tries to learn about it (especially if you aren't a native speaker). In books before 2011 the concept is called eigenclass (whilst methods of this class are called singleton methods all over), later it switched to singleton class, which allegedly can get confused with the singleton-pattern. But not enough:  
Some (like _why and Yehuda Katz) call it metaclass, [others](http://www.dyrathror.com/2009/07/ruby-objects-class-objects-and-metaclass-objects/) introduce their own terminology and call singleton classes "virtual classes" and the superclass of immediate values (like fixnum and symbol) eigenclasses. In two of my books (one uses consistently the term eigenclass, the other book singleton class) the material is quite accordingly, but at the end one book suddenly calls superclasses of singleton classes 'metaclasses'. You get the picture. What I missed was a clear train of thougt for the whole concept and an overview of syntactic pitfalls. This is my attempt to fix this. (For clarity reasons, I'll stick to the term singleton class in this post.)

### The overall mindset
The first important step is to leave the mindset of say, a Java programmer. In Java all you can do is define classes wich describe the behaviour and state of objects. In Ruby however, each object can have seperate behaviour. And since classes are objects too, one should not think of classes as 'recipies' for objects, but of classes which are basically objects of modules with three additional methods and as customizable as any other objects. You interact with objects by sending them messages. This applies to modules and classes also, so leave the static world of Java (mentally).

### Singleton methods
The easiest way of define specific behaviour for a object is to define a singleton method on that object.

{% highlight ruby %}
o = Object.new
o2 = Object.new
def o.talk
  puts 'hello!'
end
o.talk # works
o2.talk # does not work
{% endhighlight %}

If I wanted all of my objects to be able to talk, I would have defined the talk method in the class `Object`:

{% highlight ruby %}
class Object
  def talk
    puts 'hello'
  end
end
{% endhighlight %}

This is called an instance Method, because it is shared by all instances of the class `Object`.

### Classes as objects
Since classes are objects too they can be instantiated. Every class is an instance of a class called `Class`. The keyword for that is the "`class`" keyword. This is just a nice looking way. You could also write `my_class = Class.new` (and some other fancy things with the optional block for new, see below). What's confusing here is that this is basically the same as `my_object = Object.new`. It's confusing because we are used to class objects be referenced by constants, not variables. The main difference between the 'class object' and the 'object object' is that the class object is able to spawn other objects via the `new` method.

{% highlight ruby %}
my_class.methods.size #=> 98 Including new, and others.
my_object.methods.size #=> 57
{% endhighlight %}

95 of the 98 methods are inherited from Module. Class adds just three more: `[:allocate, :new, :superclass]`.

### Singleton methods on class objects
Just like above, singleton methods can be defined on classes instead of class instances (aka objects). But first, instance methods:

{% highlight ruby %}
class Car
  def set_color(color)
    @color = color
  end
end
c = Car.new
c.set_color('red')
{% endhighlight %}

The "Car" class has now a instance method wich sets the color of a car object.

{% highlight ruby %}
Car.instance_methods #=> :set_color is in here
Car.methods #=> no :set_color in sight
c.methods #=> :set_color is here again
{% endhighlight %}

We can now do (assuming there are only cars with 4 wheels):

{% highlight ruby %}
def Car.wheels
  return 4
end
{% endhighlight %}

Compare that with the notation for a singleton method on an object above: It's exactly the same, we're just using a constant instead of a variable. c won't have the method "wheels", despite it's an instance of that class.

{% highlight ruby %}
Car.instance_methods #=> no :wheels in sight
Car.methods #=> :wheels, the last method
c.methods #=> no :wheels in sight
{% endhighlight %}

It is to remember that the class object does not share it's methods or state with instances of itself. What is this good for? The example which is used most is Java's math methods like Math.sin() etc. It's used for modeling behaviour which is a feature belonging to the class as concept itself rather to its instances.

### Method lookup, part 1
A first primitive attempt: When a method is called on an object, it will search for the definition of the object in the following order:

- Its class (self to be more precise)
- Mixed-in modules, in reverse order of inclusion
- The superclass of the class
- Modules mixed into the superclass like above
- And so on to Object, Kernel and BasicObject

This raises questions like: Where is the method actually defined when we write

{% highlight ruby %}
o = Object.new
def o.talk
  puts 'hi'
end
{% endhighlight %}

Because the object itself doesn't appear in the method lookup path, niether does the method appear in the instance methods of `Object` or `Class`. The method is exclusive to that object and none other. The same goes for singleton methods on class objects:

{% highlight ruby %}
class Car
  # other syntax, same result
  def self.wheels
    return 4
  end
end
{% endhighlight %}

The following syntax gives you an error because we used "Car" before, but it shows one more time that classes are just objects and after tinkering a while the only remaining confusing thing is the constant:

{% highlight ruby %}
Car = Class.new do
  def self.steering_wheel
    return 1
  end
end
{% endhighlight %}

Every time you use `Class.new` the variable (the constant `Car` in this example) gets overwritten. Every time you use `class Car` etc. you reopen the class, using the `Car` constant. The methods you define are only valid for one single object. We're getting closer...

### Singleton classes
Instance methods live inside a class or module. But singleton methods live inside the singleton class of each object. So every object has two classes: The class of which it's an instance, and its singleton class. To add stuff to that singleton class, the special syntax "`class <<`" is used. Another confusing thing at this point is that you can use this syntax with self, variable and constant names and so forth. But when you keep in mind that "`class <<`" targets always the singleton class of the object on the right hand side of "`class <<`" it's quite easy to remember. This leads us to...

### Singleton class syntax
The main purpose of the singleton class so far is to add singleton methods to objects (we haven't talked about modules yet). Knowing that the value of self changes in class definitions, we can use the "`class <<`"-syntax to do things like this:

{% highlight ruby %}
class Car
  # self evaluates to Car
  # self.class evaluates to Class
  class << self
    def wheels
      return 4
    end
  end
# works because class opens a block in which self is Car like above
class << Car
  def wheels
    return 4
  end
end
{% endhighlight %}

These examples work on classes (referenced through constants in these examples). What confused me at first is that it works on objects in general, but since classes are objects the difference just vanished:

{% highlight ruby %}
str = 'Hi!'
class << str
# we are now accessing the singleton class of 'str'
# which is the anonymous class behind this particular string.
  def twice
    puts self * 2
  end
end
{% endhighlight %}

The irb interpreter even has a special output for representing that we are not operating on a regular object or class:

{% highlight ruby %}
class Car
  class << self
    puts self
  end
end
#<Class:Car>

class << str
  puts self
end
#<Class:#<String:0x0000000136ead8>>
{% endhighlight %}

You can see that before the constant respectively the object there is this `<Class:>` thingy, indicating that self is pointing to something 'above' the `Car`, so to speak. Instead of using the "`class << object`"-syntax I could have used the syntax like in the first examples:

{% highlight ruby %}
def str.twice
  etc..
{% endhighlight %}

There is one difference, though. It has to do with the way constants are resolved, which I won't explain here. It's far more better explained in David A. Black's superb book "The well-grounded Rubyist", the one Ruby book I should have read before all other books about Ruby programming. One thing I really don't like about Ruby is that I'm showing here 2 ways to define singleton methods on a class, but there are at least 10, maybe more ways to do that (by using techniques like send). And when you aren't 100% familiar with all the stuff above and stumble upon code like this:

{% highlight ruby %}
private
  class << self
    def define_singleton_method(name, content = Proc.new)
      (class << self; self; end).class_eval do
        undef_method(name) if method_defined? name
        String === content ? class_eval("def #{name}() #{content}; end") : define_method(name, &content)
      end
    end
    etc...
{% endhighlight %}

well.. good luck. (This snippet is taken from the sinatra.rb source code, I'll say something about class_eval and instance_eval later.) Just for the record: you could do

{% highlight ruby %}
class << Object
  class << self
    self
  end
end
#<Class:#<Class:Object>>
{% endhighlight %}

which is the singleton class of the singleton class of object. But who would wanna do that?  

Two more things about syntax: Since Ruby has all those cool introspection features like `Object#methods` and `Class#instance_methods`, it doesn't have `Object#singleton_class` like `Object#class`. But you can create that method by yourself:

{% highlight ruby %}
class Object
  def singleton_class
    class << self
      self
    end
  end
end
{% endhighlight %}

This method just returns self in the context of the singleton class. Now you can assign the singleton class to a variable! According to the [German Ruby wiki](http://wiki.ruby-portal.de/Singleton) this was introduced by _why to run eval on a singleton class for an object. (Noticing something similar in the sinatra-snippet?) Another interesting thing you can do with the "`class << object`"-syntax is from the [Ruby programming wikibook](http://en.wikibooks.org/wiki/Ruby_Programming/Syntax/Classes): By defining an `attr_accessor` in the singleton class context you can have class instance variables, which are unique for every descending object. This points at the fact that there is something different in the lookup chain and inheritance by using singleton classes. Let's have a look.

### Lookup, part 2
The revised lookup path goes as follows:

- Look first in the own singleton class of the current object
- Then in all the modules the singleton class may have included
- Now the search goes like above: The own class of the object
- And up like above

A singleton class is just a class (You can try by examining the object you get with `singleton_class` above, it has all the typical "class-methods".) Therefore, it can include a module to get more methods. That is what we have to digest next.

### Modules and singleton classes
To easily add behaviour to certain objects, you can add behaviour to a module and then include that module into the singleton class. Since the order changed in which methods are looked up (the own singleton class first), all other methods with the same name are overwritten. This is cool for adding a set of behaivours to single objects.

### What about class_eval and instance_eval?
[This blogpost](http://ilikestuffblog.com/2009/01/09/fun-with-rubys-instance_eval-and-class_eval/) covers right about everything. The connection one could make that it is another way of defining class methods, but it is capable of much more, especially with the eval functionalitiy. More details are covered in the excellent book "Metaprogramming Ruby" where the Author reveals more details how `instance_eval` and `class_eval` work.

### Inheritance and singleton classes
As we have seen above, you can define a method in a singleton class and it is found also in classes which are ascendants from that particular class. For example:

{% highlight ruby %}
class C
  class << self
    def a_class_method
      puts 'greetings from C!'
    end
  end
end
class D < C
end
D.a_class_method #=> 'greetings from C!'
{% endhighlight %}

Why does this work? It seems our model about the method lookup is still flawed. Let's add that.

### Lookup, part 3
It turns out that additionally to part 2 the search includes not only the singleton class, but also the superclass of a class. From there, the search is continued in the singleton class of the current superclass. In the above example this would be:

1. `D.a_class_method` => nope
2. Singleton class of `D` => nope
3. Superclass of the singleton class of `D`, aka the singleton class of `C` => jay!
4. If not found till here, the normal mechanism would kick in and the superclass of `D` (`C`) would be searched
5. See above

It is to note here that the search goes through the singleton classes first, then through the rest. This is why the trick above works.

### Conclusion
I hope I'm not missing any important details. But "trains of thoughts" like this help me to remember stuff like that more easily, like my blog posts in German below.
