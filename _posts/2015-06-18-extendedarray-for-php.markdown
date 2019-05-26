---
title: 'ExtendedArray for PHP'
date: 2015-06-18 16:21:32 +0000
tags: php composer
layout: post
subclass: 'post tag-test tag-content'
---
Today I released an `ExtendedArray` for **PHP**, you can [check It out in Github][repo].

An example:

<pre><code class="language-php">use TA\ExtendedArray\Type\ExtendedArray;

$fruits = new ExtendedArray(
    array(
        'tropical' => 'pineapple',
        'farm'     => 'strawberry'
    )
);

$fruits->has('tropical');

// true

$fruits->hasOne(array('tropical', 'asian'));

// true

$fruits->hasAll(array('tropical', 'asian'));

// false

$fruits->get('canary', 'banana');

// 'banana'

$fruits->get('tropical', 'banana');

// 'pineapple'

$fruits->getSubArray(array('tropical'));

// array('tropical' => 'pineapple');

$fruits->toArray();

// array('tropical' => 'pineapple', 'farm' => 'strawberry');

</code></pre>

You can access the keys as an object or as an array.

<pre><code class="language-php">$fruits->tropical;

// 'pineapple'

$fruits['tropical'];

// 'pineapple'
</code></pre>

There is more than one way of accessing the properties, I want to give flexibility, but [I know there should be only one (obvious) way to use It][zen], feel free to select your way of access.

It uses [ArrayObject][array_object] underneath and [You can install ExtendedArray through composer][composer]. :-)

[repo]: https://github.com/javaguirre/php-extended-array
[array_object]: https://php.net/manual/en/class.arrayobject.php
[composer]: https://packagist.org/packages/javaguirre/php-extended-array
[zen]: https://www.python.org/dev/peps/pep-0020/
