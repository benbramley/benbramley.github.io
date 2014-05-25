---
layout: post
title: "Flex and XML over HTTP"
date: 2011-03-21 22:09:49 +1000
comments: true
categories: flex
---
I’ve recently been building a flex based GUI and wanted to populate certain UI components using some XML returned via a Java Servlet. I thought this would be very simple using the **mx:xml** tag. However it turns out that the **mx:xml** tag does not support dynamic content (it populates at compile time and does not update at runtime).

So instead I needed to use the **mx:HTTPService** tag and use this to return the XML as a variable which can then be assigned to my UI components. I also need to create methods to handle success and fail of calling the HTTP Service:

{% codeblock %}
![CDATA[ 
private var xmldata:XML; 
private function initData():void { 
	uielement = new UIElement(xmldata);
} 

private function xmlResultSuccess( event:ResultEvent ):void { 
	xmldata = event.result as XML; initData(); 
} 

private function xmlResultFailure( event:FaultEvent ):void { 
	Alert.show( event.fault.message, "Error loading XML" );
} 
]]> 
{% endcodeblock %}

Here we have defined a variable xmldata to hold the xml content. We populate this data using the HTTP Service tag. We then create the UI element, passing in the XML we obtained from the Servlet. Simples.