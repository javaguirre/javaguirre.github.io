---
title: 'Clean Code, functions'
date: 2015-06-19 14:53:28 +0000
tags: clean architecture functions
---
I am reading [Clean Code: A Handbook of Agile Software Craftmanship][clean_code]. Here my highlights from the *Functions* chapter.

The way every developer with experience should read this book is like you know nothing, a blank canvas, draw your picture from there.

Having less than two arguments is always preferable.

> There are two very common reasons to pass a single argument into a function. You may be asking a question about that argument, as in boolean fileExists(“MyFile”). Or you may be operating on that argument, transforming it into something else and returning it

Return values.

> Using an output argument instead of a return value for a transformation is confusing. If a function is going to transform its input argument, the transformation should appear as the return value.

Related to boolean values in functions.

If you want to change the behaviour with a boolean indicates the function is doing two things, violating the [single responsibility principle][srp].

>  Flag arguments are ugly. Passing a boolean into a function is a truly terrible practice.

Use classes if you need to abstract functionality instead of a big list of arguments(more than three).

>  Reducing the number of arguments by creating objects out of them may seem like cheating, but it’s not. When groups of variables are passed together, the way x and y are in the example above, they are likely part of a concept that deserves a name of its own

In an object, change the state of the owning object instead of returning a value in a method.

>  In general output arguments should be avoided. If your function must change the state of something, have it change the state of its owning object.

Self-explanatory. :-)

>  Functions should either do something or answer something, but not both.

Error handling is one thing, It should have its own function.

>  Try/catch blocks are ugly in their own right. They confuse the structure of the code and mix error processing with normal processing. So it is better to extract the bodies of the try and catch blocks out into functions of their own.

More on the topic.

>  Functions should do one thing. Error handing is one thing. Thus, a function that handles errors should do nothing else.

Regarding function and method organization.

>  your real goal is to tell the story of the system, and that the functions you write need to fit cleanly together into a clear and precise language to help you with that telling.

On the top of our file/class should be the highest level of abstraction and scrolling down in the file we should have the simplest details used in that file *story*.

[clean_code]: http://www.amazon.com/Clean-Code-Handbook-Software-Craftsmanship/dp/0132350882
[srp]: https://en.wikipedia.org/wiki/Single_responsibility_principle
