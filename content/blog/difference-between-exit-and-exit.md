---
title: "Difference between exit() and _exit()"
date: 2010-08-22T23:00:19+05:30
draft: false

# post thumb
image: "images/post/samesame-but-different.jpg"

# meta description
description: "There are a few differences between exit() and _exit() that become significant when fork(), and especially vfork(), is used"

# taxonomies
categories: 
  - "blog"

tags:
  - "unix"

# post type
type: "featured"

---

The functions, exit() and _exit() serve the same master, but have different motives.

There are a few differences between exit() and _exit() that become significant when fork(), and especially vfork(), is used.

The basic difference between exit() and _exit() is that the former performs clean-up related to user-mode constructs in the library, and calls user-supplied cleanup functions, whereas the latter performs only the kernel cleanup for the process.

In the child branch of a fork(), it is normally incorrect to use exit(), because that can lead to stdio buffers being flushed twice, and temporary files being unexpectedly removed. In C++ code the situation is worse, because destructors for static objects may be run incorrectly. (There are some unusual cases, like daemons, where the parent should call _exit() rather than the child; the basic rule, applicable in the overwhelming majority of cases, is that exit() should be called only once for each entry into main.)

In the child branch of a vfork(), the use of exit() is even more dangerous, since it will affect the state of the parent process.