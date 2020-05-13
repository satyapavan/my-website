---
title: "Fork bomb"
date: 2010-05-22T10:07:47+06:00
draft: false

# post thumb
image: "images/post/explosion.jpg"

# meta description
description: "This is a full size nuclear reactor"

# taxonomies
categories: 
  - "blog"
tags:
  - "unix"
  - "fork"

# post type
type: "featured"
---

A fork bomb works by creating a large number of processes very quickly in order to saturate the available space in the list of processes kept by the computer’s operating system. If the process table becomes saturated, no new programs may start until another process terminates. Even if that happens, it is not likely that a useful program may be started since the instances of the bomb program will each attempt to take any newly-available slot themselves.

In addition to using space in the process table, each child process of a fork bomb uses further processor-time and memory. As a result of this, the system and existing programs slow down and become much more unresponsive and difficult or even impossible to use.

The following code provides arguably one of the most elegant examples of a fork bomb. The user executes the fork bomb by pasting the following 11 characters into a UNIX shell such as bash or zsh.

```
:(){ :|:& };:
```

Understanding the above:

```
:()      # define ‘:’ – whenever we say ‘:‘, do this:
{        # beginning of what to do when we say ‘:’
    :    # load another copy of the ‘:’ function into memory…
    |    # …and pipe its output to…
    :    # …another copy of ‘:’ function, which has to be loaded into memory
         # (therefore, ‘:|:’ simply gets two copies of ‘:’ loaded whenever ‘:’ is called)
    &    # disown the functions – if the first ‘:’ is killed,
         #     all of the functions that it has started should NOT be auto-killed
}        # end of what to do when we say ‘:’
;        # Having defined ‘:‘, we should now…
:        # …call ‘:‘, initiating a chain-reaction: each ‘:’ will start two more.
```

To keep it simple,

```
forkbomb(){ forkbomb|forkbomb & } ; forkbomb
```
