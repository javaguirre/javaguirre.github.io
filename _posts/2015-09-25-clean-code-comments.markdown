---
title: 'Clean Code, comments'
date: 2015-09-25 14:06:21 +0000
tags: clean architecture
---
Some months ago I finished the [Clean Code book][book], [I already wrote about functions][clean1], today I'll write about my highlights regarding **comments**,

> Comments are not like Schindler’s List. They are not “pure good.” Indeed, comments are, at best, a necessary evil

Indeed, if a comment is needed, 99% of the time is because the code is not descriptive enough, the name of the variables, the classes or the function methods don't have a clear nomenclature within the domain of our application. As It is expressed further in the book:

> The proper use of comments is to compensate for our failure to express ourself in code.

Regarding coding standards (in this case in Java), the book says the following.

> It is just plain silly to have a rule that says that every function must have a javadoc, or every variable must have a comment. Comments like this just clutter up the code, propagate lies, and lend to general confusion and disorganization.

In this case We usually generate documentation from comments within the code, which is useful, because if you are deep in the code you usually want an explanation there. What the book says is this comments shouldn't be an obligation in each file, class, method, which I completely agree.

It's annoying when your coding standard extension obligates you to write a comment when It's not really needed, is it gonna help the people understand the code better? is it really necessary? then write It, but It shouldn't be a requirement. :-)


[clean1]: https://javaguirre.net/2015/06/19/clean-code-functions/
[book]: http://www.amazon.com/Clean-Code-Handbook-Software-Craftsmanship/dp/0132350882
