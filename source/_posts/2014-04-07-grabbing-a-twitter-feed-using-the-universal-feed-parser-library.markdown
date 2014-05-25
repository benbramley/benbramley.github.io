---
layout: post
title: "Grabbing a Twitter feed using the Universal Feed Parser library"
date: 2011-05-14 22:09:26 +1000
comments: true
categories: python
---
When this blog was hosted on Wordpress I used a Twitter plugin which would connect to my Twitter feed and display my recent tweets. Since I recently moved over to Google App Engine I needed to recreate this plugin in Python. There is a great Python library called Universal Feed Parser ([http://www.feedparser.org/](http://www.feedparser.org/)) which can be used to do this. It can parse every type of feed you can think of including RSS which is a format the Twitter API (http://dev.twitter.com/pages/api_overview) provides. Here is url to get my latest tweets in RSS format. Simply replace my screen name with yours:[ http://api.twitter.com/1/statuses/user_timeline.rss?screen_name=benjaminbramley](%20http:/api.twitter.com/1/statuses/user_timeline.rss?screen_name=benjaminbramley) 

There are a whole load of parameters that can be passed to the url to set various options around the feed. Check out Twitter API docs for more info. I am using a code snippet from here - [http://teebes.com/blog/17/simple-python-twitter-rss-feed-parser](http://teebes.com/blog/17/simple-python-twitter-rss-feed-parser) - which I've modified slightly to allow me to display only tweets which contain a certain hashtag:

{% codeblock lang:python %}
import datetime 
import feedparser 
import re

#Takes a twitter rss feed url, optional limit and hashtag filter, and returns a list of 
#tweet objects. Each tweet object has a text attribute (html string of the tweet) and 
#posted attribute (date which the tweet was tweeted)

def get_twitter(url, limit=3, hashtag=''): 

	twitter_entries = [] 
	twitterfeed = feedparser.parse(url) 
	counter = 0 
	for entry in twitterfeed['entries']: 
		# convert the given time format to datetime 
		posted_datetime = datetime.datetime( entry['updated_parsed'][0], entry['updated_parsed'][1], entry['updated_parsed'][2], entry['updated_parsed'][3], entry['updated_parsed'][4], entry['updated_parsed'][5], entry['updated_parsed'][6], ) 
		# format the date to include the year if not this year 
		if posted_datetime.year == datetime.datetime.now().year: 
			posted = posted_datetime.strftime("%b %d")
	else: 
		posted = posted_datetime.strftime("%b %d %y") 
		
		# remove the ": " that preceeds all twitter feed entries 
		text = re.sub(r'^\w+:\s', '', entry['title']) 
		
		# parse links 
		text = re.sub( r"(http(s)?://[\w./?=%&\-]+)", lambda x: "[%s](%s)" % (x.group(), x.group()), text) 
		
		# parse @tweeter 
		text = re.sub( r'@(\w+)', lambda x: "[%s](http://twitter.com/%s)"\ % (x.group()[1:], x.group()), text) 
		
		# parse #hashtag 
		text = re.sub( r'#(\w+)', lambda x: "[%s](http://twitter.com/search?q=%%23%s)"\ % (x.group()[1:], x.group()), text) 
		
		#Filter for hashtag in twitter text if set 
		if hashtag: 
			if re.search(hashtag, text): 
				twitter_entries.append({ 'text': text, 'posted': posted, }) 
				counter += 1 
		else: 
			twitter_entries.append({ 'text': text, 'posted': posted, }) 
			counter += 1 
			if counter == limit: 
				break 
	return twitter_entries 
{% endcodeblock %}

To display the Tweets I pass the result of the method above into a Django template which iterates over the list and outputs the result:

{% raw %}

	{% if twitter_list %} 
		<ul>
		<li id="recentposts">
			<h2>Recent Tweets</h2>

	    	<ul>{% for tweet in twitter_list %}   
	    	<li>{{ tweet.text }}</li> 
	    	    {% endfor %}</ul>
    	</li>
    	</ul>
	{% endif %} 

{% endraw %}
