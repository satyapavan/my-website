---
title: "Bob, What's this CLOB and BLOB?"
date: 2013-11-10T23:05:42+05:30
draft: false

# post thumb
image: "images/post/purple-object.jpg"

# meta description
description: ""

# taxonomies
categories: 
  - "blog"

tags:
  - "database"

# post type
type: "post"

---

The main differences between BLOB and CLOB are:

- BLOB stores values as LOB (Large OBject) in bitstreams.
- CLOB stores values as LOB (Large OBject) in character steams.

CLOBs are for text like XML, JSON, etc. You should not assume what encoding the database will store it as (at least in an application) as it will be converted to whatever encoding the database was configured to use.

BLOBs are for binary data. You can be reasonably assured that they will be stored how you send them and that you will get them back with exactly the same data as they were sent as.

CLOB is encoding and collation sensitive, BLOB is not.

When you write into a CLOB using, say, `CL8WIN1251`, you write a `0xC0` (which is Cyrillic letter А).
When you read data back using `AL16UTF16`, you get back `0x0410`, which is a UTF16 representation of this letter.

If you were reading from a BLOB, you would get same `0xC0` back.