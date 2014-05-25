---
layout: post
title: "Locking and Unlocking Oracle User Accounts"
date: 2011-06-27 21:54:59 +1000
comments: true
categories: sql
---
To lock an Oracle user account open sqlplus and execute:
{% codeblock %}
ALTER USER username ACCOUNT LOCK; 
{% endcodeblock %}

To unlock an Oracle user account open sqlplus and execute:
{% codeblock %}
ALTER USER username ACCOUNT UNLOCK; 
{% endcodeblock %}
