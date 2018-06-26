---
layout: post
title:      "Let's Get Meta..."
date:       2018-06-26 05:23:41 +0000
permalink:  lets_get_meta
---

```
class Fixnum
    def hours
      self * 3600 # number of seconds in an hour
    end
    alias hour hours
  end
```



And that’s all metaprogramming is. Away of adding arbitrary code to your application without having to write (or manually copying) that code.   


Like in defining new methods and classes:  

```
class MyClass
  def red
    'red'
  end
  def blue
    'blue'
  end
  def green
    'green'
  end
  def yellow
    'yellow'
  end
end
```


Much more succinctly put: 

```
class MyClass
  COLORS = ['red', 'blue', 'green', 'yellow']
  COLORS.each do |color|
    define_method color do
      color
    end
  end
end
```



