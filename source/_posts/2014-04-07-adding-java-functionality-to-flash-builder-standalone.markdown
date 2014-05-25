---
layout: post
title: "Adding Java functionality to Flash Builder standalone"
date: 2011-01-24 22:10:25 +1000
comments: true
categories: flex
---
I was surprised whilst using Flash Builder that when I tried to open a **.java** file it tried to fire up XCode instead of opening the file in Flash Builder.

This is because even though Flash Builder is essentially Eclipse underneath the Java support is not installed by default.

To add support for Java:

1.  Open Flash Builder
2.  Click Help - Install New Software…
3.  At the top of the dialog, click “Add…” to add an update site URL
4.  Specify a name (I chose Java) and add this URL http://download.eclipse.org/releases/galileo/
5.  Press OK.
6.  In the table of features, expand “Programming Languages” and select “Eclipse Java Development Tools”
7.  Finish the wizard Restart Flash Builder and you should be good to go with Java support.