---
title: Ruby Getter and Setter Methods
date: 2019-10-13
published: true
tags: []
series: false
cover_image: ./images/ruby_methods.jpeg
canonical_url: false
description: "Ruby methods are very similar to functions in any other programming language. Ruby methods are used to bundle one or more repeatable statements into a single unit."
---
###  What is getter method?
A getter method is a method that gets a value of an instance variable.
Without a getter method, you can not retrieve a value of an instance
variable outside the class the instance variable is instantiated from.
### Example
```ruby
class Movie
  def initialize(name)
    @name = name
  end

  def name
    @name
  end
end

obj1 = Movie.new('Dare Devil')
p obj1.name #=> "Dare Devil"
```
In the above example a getter method `name` is defined which help in accessing `@name` instance variable,
though the name of a getter method can be anything, it is common practice to name a getter method the instance variable’s name.

### What is setter method?
A setter is a method that sets a value of an instance variable.Without a setter method, you can not assign a value to an instance variable outside its class.
if you try to set a value of an instance variable outside its class, Ruby raises No Method Error just like it does when you
try to retrieve a value of an instance variable outside its class without a getter method

### Example
```ruby
class Movie
  def initialize(name)
    @name = name
  end

  def name #getter method
    @name
  end

  def name=(name) #setter method
    @name = name
  end
end

obj1 = Movie.new('Dare Devil')
p obj1.name #=> "Dare Devil"
obj1.name = 'Commandos'
p obj1.name #=> "Commandos"
```

### What are accessors?
Accessors are a way to create getter and setter methods without explicitly defining them in a class.
There are three types fo accessors in Ruby.
- `attr_reader` automatically generates a getter method for each given attribute.
- `attr_writer` automatically generates a setter method for each given attribute.
- `attr_accessor` automatically generates a getter and setter method for each given attribute.

In below example, name and year are retrieved outside Movie class even though there is no getter method for either of them. This is because attr_reader generates a getter method for each given attribute.
```ruby
class Movie
  attr_reader :name, :year

  def initialize(name, year)
    @name = name
    @year = year
  end
end
obj1 = Movie.new('Dare Devil', 2019)
p obj1.name #=> Dare Devil
p obj1.year #=> 2019
```
As I mentioned above, attr_witer generates a setter method for each given attribute. Therefore you can assign new values to ob1 without explicitly writing setter methods for name and year

```ruby
class Movie
  attr_reader :name, :year
  attr_writer :name, :year

  def initialize(name, year)
    @name = name
    @year = year
  end
end
obj1 = Movie.new('Dare Devil, 2019)
obj1.name = 'Fight Club'
obj1.year = 2020
p obj1.name #=> "Fight Club"
p obj1.year #=> 2020
```

Lastly, attr_accessor does what attr_reader and attr_writer do with just one line of code! It will automatically generate a getter and setter mehod for each given attribute.
```ruby
class Movie
  attr_accessor :name, :year

  def initialize(name, year)
    @name = name
    @year = year
  end
end
obj1 = Movie.new('Dare Devil', 2019)
obj1.name = 'Fight Club'
obj1.year = 2020
p obj1.name #=> "Fight Club"
p obj1.year #=> 2020
```
