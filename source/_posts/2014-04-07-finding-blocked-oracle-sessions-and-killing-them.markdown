---
layout: post
title: "Finding blocked Oracle sessions (and killing them)"
date: 2011-05-19 22:08:59 +1000
comments: true
categories: sql
---
Here's a handy bit of SQL which will find blocked sessions in your Oracle database:
{% codeblock %}
select blocking_session, sid, serial#, sql_id, wait_class,seconds_in_wait 
from v$session where blocking_session is not NULL 
order by blocking_session; 
{% endcodeblock %}

This returns some useful info about the blocked sessions, including how long its been blocked for and the wait class event. Wait class events will give you an indication about the root cause of the hung session i.e. what it is waiting for. Burleson has a nice intro article on Oracle wait class events here - [http://www.dba-oracle.com/concepts/query_wait_events.htm](http://www.dba-oracle.com/concepts/query_wait_events.htm)

Once you have the sql_id from the above SQL you can view the problematic SQL query like this:
{% codeblock %}
select sql_text from v$sql where sql_id = 'insert sql_id here'; 
{% endcodeblock %}

If you decide you need to kill the problematic session use the sid and serial# like this :
{% codeblock %}
alter system kill session 'sid,serial#'; 
{% endcodeblock %}