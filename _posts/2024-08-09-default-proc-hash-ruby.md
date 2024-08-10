---
layout: post
title:  "A Clever Way to Use default_proc in a Hash"
date:   2024-08-09 15:40:00
categories: [coding]
tags: [ruby]
---

![header](https://images.unsplash.com/photo-1679118213913-3f94639b0cca?q=80&w=2532&auto=format&fit=crop&ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D){:width="50%"}

# A Clever Way to Use default_proc in a Hash

Let's explore an interesting way to use the `default_proc` feature in a Ruby Hash. Suppose we have a `RANKS` hash to determine the badge a player receives in a game based on their points. If the points don't match any of the defined ranges, the result should be `:none`. We'll create a rank method to receive the points and iterate through the hash to find the appropriate badge.

```ruby
RANKS = {
  1..10   => :rabbit,
  11..50  => :falcon,
  51..100 => :dog,
  101..   => :tiger
}
RANKS.default = :none

def rank(points)
  _, badge = RANKS.find { |range, _| range.include?(points) }
  badge || RANKS.default
end
```

Here's what we get when we use the `rank` method:

```ruby
puts rank(0)    # none
puts rank(20)   # falcon
puts rank(100)  # dog
puts rank(5000) # tiger
```

It would be convenient to retrieve the badge directly from the hash, for example, using `RANKS[20]` to get `:falcon`. However, since the keys are ranges, we can't directly use a number as a key. This is where `default_proc` comes in handy.

The `default_proc` is called whenever we try to access an element in the hash using a non-existent key. Since the keys of `RANKS` are ranges, `default_proc` will be triggered when we attempt to use the number of points as the key, like `RANKS[20]`.

```ruby
RANKS = {
  1..10   => :rabbit,
  11..50  => :falcon,
  51..100 => :dog,
  101..   => :tiger
}

RANKS.default_proc = proc do |hash, key|
  _, badge = hash.find { |range, _| range.include?(key) }
  badge || :none
end
```

Now, we can retrieve the badge directly from the hash, using the points as a key, and let the hash handle the heavy lifting:

```ruby
puts RANKS[0]    # none
puts RANKS[20]   # falcon
puts RANKS[100]  # dog
puts RANKS[5000] # tiger
```

Cheers!

---
Banner photo by <a href="https://unsplash.com/@muriel_1?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash">Muriel Liu</a> on <a href="https://unsplash.com/photos/a-bunch-of-bubbles-floating-in-the-air-wgjqKylzksQ?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash">Unsplash</a>
