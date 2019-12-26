---
layout: single
title:  "\"Overloading constructors\" in Python"
date:   2018-05-26 15:31:57 +0300
header:
    image: "/assets/images/overload-init-header.jpg"
    image_description: "classmethod"
category: Python
tags: 
- python 
---

*TL;DR Provide more than one way to instantiate your class by implementing factory methods with the classmethod decorator.*

I have encountered the need to have multiple constructors for a Python class more than once. Since the few articles about this subject present what I believe to be a non-optimal solution, I decided to share the one I use (it appears in a few stackoverflow answers, but surprisingly there seem to be no articles about it).

The problem
-----------
Let's say we have a Book class:

{% highlight python %}
class Book:
    def __init__(self, title: str, author: str, pages: int):
        self.title = title
        self.author = author
        self.pages = pages
{% endhighlight %}

We decided to consume a web API that returns books data, and it would be nice to be able to instantiate a Book object directly from the API's JSON response. However, Python does not have method overloading, and we already have some code using this class from before, preventing us from changing the existing \__init__ method.
As some would suggest, we could add an optional parameter (and give default arguments to the other parameters) like so:

{% highlight python %}
class Book:
    def __init__(self, title: str = None, author: str = None , pages: int = None, book_as_json: str = None):
    if book_as_json:
    	book = json.loads(book_as_json)
    	self.title = book['title']
    	self.author = book['author']
    	self.pages = book['pages']
    else:
        self.title = title
        self.author = author
        self.pages = pages
{% endhighlight %}

This is not a good solution for two reasons. The first is code duplication and extra branching, but perhaps more important is the introduction of default values which are not really needed as sort of a hack. book_as_json and the rest of the parameters are mutually exclusive, and we are at risk of instantiating an object with all None arguments. And what will happen if we will need another constructor? It will be even more complex! Yikes.

The clean solution
------------------
"Overloading" (or rather mimicking overloading) is not a good solution, in my opinion - this is a classic case for a factory method, using the [classmethod decorator][classmethod-decorator]:
{% highlight python %}
class Book:
    def __init__(self, title: str, author: str, pages: int):
        self.title = title
        self.author = author
        self.pages = pages

    @classmethod
    def from_json(cls, book_as_json: str) -> 'Book':
    	book = json.loads(book_as_json)
    	return cls(title=book['title'], author=book['author'], pages=book['pages'])
{% endhighlight %}

Ah, much cleaner...
{% highlight python %}
catch22 = Book.from_json('...')
{% endhighlight %}

Actually, I encourage the use of this pattern even in languages that do allow constructor overloading, especially in ones without keyword arguments (I am looking at you, Java). This is a nice example of how the constraints of a language forces the use of a cleaner solution. 

[classmethod-decorator]: https://www.programiz.com/python-programming/methods/built-in/classmethod