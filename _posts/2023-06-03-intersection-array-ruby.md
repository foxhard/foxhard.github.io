---
layout: post
title:  "How to use intersection methods in Ruby arrays"
date:   2023-06-03 15:40:00
categories: [coding]
tags: [ruby]
---

![header](https://images.unsplash.com/photo-1477519242566-6ae87c31d212?ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D&auto=format&fit=crop&w=1470&q=80){:width="50%"}

# How to use intersection methods in Ruby arrays

In this post, we will explore three different methods that can be used to find the common elements between two or more arrays in Ruby: `intersection`, `Array#&` and `intersect?`. These methods are useful for performing set operations on arrays and can help us manipulate data more efficiently.

## The intersection method

The `intersection` method was introduced in Ruby 3.0 and it returns a new array containing the elements that are common to all the arrays given as arguments. The order of the elements is preserved from the original array and any duplicates are removed. For example:

```ruby
a = [1, 2, 3, 4]
b = [2, 4, 6, 8]
c = [4, 8, 12, 16]

a.intersection(b) # => [2, 4]
a.intersection(b, c) # => [4]
```

The `intersection` method can also take a block that determines how the elements are compared. The block should return a boolean value indicating whether two elements are equal or not. For example:

```ruby
a = ["apple", "banana", "cherry"]
b = ["pineapple", "mango", "apple", "strawberry"]

a.intersection(b) { |x, y| x[0] == y[0] } # => ["apple"]
```

The `intersection` method is equivalent to using the `&` operator on multiple arrays, but it is more expressive and readable.

## The Array#& method (aka & operator)

The `Array#&` method is an alias for the `intersection` method and it performs the same operation as described above. It returns a new array containing the elements that are common to both arrays and removes any duplicates. The order of the elements is preserved from the original array. For example:

```ruby
a = [1, 2, 3, 4]
b = [2, 4, 6, 8]

a & b # => [2, 4]
```

The `Array#&` method can also take multiple arrays as arguments and it will return the common elements among all of them. For example:

```ruby
a = [1, 2, 3, 4]
b = [2, 4, 6, 8]
c = [4, 8, 12, 16]

a & b & c # => [4]
```

The `Array#&` method does not take a block as an argument and it uses the `==` operator to compare the elements.

## The intersect? method

The `intersect?` method was introduced in Ruby 3.1 and it returns a boolean value indicating whether two or more arrays have any common elements or not. For example:

```ruby
a = [1, 2, 3, 4]
b = [2, 4, 6, 8]
c = [5, 7, 9]

a.intersect?(b) # => true
a.intersect?(c) # => false
```

The `intersect?` method is useful for checking whether two or more arrays overlap or not without creating a new array.

## Conclusion

In this post, we have learned how to use three different methods to find the common elements between two or more arrays in Ruby: `intersection`, `Array#&` and `intersect?`. These methods are handy for performing set operations on arrays and can help us manipulate data more efficiently.

---
Banner Photo by <a href="https://unsplash.com/@dnevozhai?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Denys Nevozhai</a> on <a href="https://unsplash.com/photos/_QoAuZGAoPY?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Unsplash</a>
