---
layout: post
title: "StaleConnectionException"
date: 2011-02-25 22:09:59 +1000
comments: true
categories: java
---
The **StaleConnectionException** comes about when an application tries to use a connection in the connection pool which is invalid.

An invalid connection can occur for a number of reasons however in my client's case the reason was due to a firewall rule change that had been made between the application server and the database. This firewall rule meant that any connections made on port 1521 (default Oracle port) would be dropped every 5 minutes. Once dropped the connection would become 'stale' and needed to be refreshed. Unfortunately the application, running on WebSphere 6.0, did not handle this scenario very well and would not get a new connection from the pool when the **StaleConnectionException** was thrown.

Instead if would fail horribly until the connection pool was refreshed. Then the cycle would start again. Here is an example of how to deal with this scenario:
{% codeblock lang:java %}
try { 
  	boolean retry = true; 
  	while( retry) { 
	    try { 
	      Connection conn1 = ds1.getConnection(); 
	      Statement stmt1 = conn1.createStatement();
	      ResultSet res = stmt1.executeQuery(stmtString); // If all the database work was successful. 
	      retry = false; 
	    } 
	    catch (StaleConnectionException ex) { // counter to retry a limited number of times? 
	      	retry = true; // wait? 
	  	} 
	  	catch (SQLException sqle) { 
	  	} // end try-catch 
	  	finally { 
	  		// close result sets 
	  		// close statements 
	  		// close connection 
	  	} 
	} // end while 
} 
catch( Exception ex) { 
} // end try-catch 
{% endcodeblock %}

Lesson learned here is never assume there will always be valid connections in your connection pool and cater for that in your code.
