---
layout: post
title: "Splunk Lookups using Redis"
date: 2014-05-30 22:16:06 +1000
comments: true
categories: splunk, redis 
published: false
---
I'm a big fan of both [Splunk][1] and [Redis][2] so I was pretty happy to get the chance to implement a solution recently which uses both technologies. The requirement was to be able to do Splunk lookups which could scale to millions of keys, but also to be able to frequently modify individual key values of the lookup on the fly i.e. a dynamic lookup.

There is a nice article on the [Splunk Blog][3] on how to achieve dynamic lookups using out of the box lookup functionality. The technique uses a combination of [inputlookup][6] and [outputlookup][7] commands to load in the existing lookup items, append new items and discard unwanted ones. This works really well for small to medium sized lookup files but when you start getting into megabyte sized lookup files performance takes a hit. For example at 5MB the read and subsequent write to delete a lookup item takes 3 seconds. This adds up when the lookup items are changing frequently (in this case a number of times per minute).

Enter Redis.

Redis is a key value store which serves data from RAM (so it's super fast) but also provides persistence and replication capabilities ([amongst other cool features][4]). It has a [Python][5] client which is ideal for us to create a custom lookup command in Splunk.

In terms of data structures Redis offers a number of different [data types][8] from simple strings to more complex hashes. The obvious choice for Splunk Lookups would be to use a Hash with the Hash Key mapped to the Splunk field value we wish to lookup. This would then return the entire Hash with all the associated fields and values. To make this behave exactly like the Splunk lookup command though, we would want to be able to lookup values of the fields within the Hash. Searching by value is not something you can do in Redis (this is by design and for good reason). However we can of course store pointers from field values back to hash keys in Redis. I chose to do this using Redis Sets:

{% codeblock %}

Hash Key1 -> Field1, Value1
          -> Field2, Value2
          -> Field3, Value3

Hash Key2 -> Field1, Value4
          -> Field2, Value5
          -> Field3, Value6

Set Key1(Value1) -> Hash Key1
Set Key2(Value2) -> Hash Key1
Set Key3(Value3) -> Hash Key1

Set Key4(Value4) -> Hash Key2
Set Key5(Value5) -> Hash Key2
Set Key6(Value6) -> Hash Key2

{% endcodeblock %}


{% codeblock lang:python %}
# Small LUA function to return a hash using a set key
lua = """
local matches = redis.call('SMEMBERS', KEYS[1])
local val = {}
for count = 1, #matches do
    val[count] = redis.call('HGETALL', matches[count])
end
return { val }
"""
# Register LUA script
gethashfromkey = redp.register_script(lua)
{% endcodeblock %}



   [1]: http://www.splunk.com/
   [2]: http://www.redis.io/
   [3]: http://blogs.splunk.com/2011/01/11/maintaining-state-of-the-union/
   [4]: http://redis.io/topics/introduction
   [5]: https://github.com/andymccurdy/redis-py
   [6]: http://docs.splunk.com/Documentation/Splunk/6.0.2/SearchReference/inputlookup
   [7]: http://docs.splunk.com/Documentation/Splunk/6.0.2/SearchReference/outputlookup
   [8]: http://redis.io/topics/data-types
