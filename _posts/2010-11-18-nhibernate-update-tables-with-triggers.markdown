--- 
layout: post
title: NHibernate - update tables with triggers
wordpress_id: 344
wordpress_url: http://blog.elinkmedia.net.au/?p=344
date: 2010-11-18 17:06:05 +11:00
---
I was finishing off the windows service project I was working on. After installing the windows service on the production server, I got exceptions thrown by Castle ActiveRecord. After looking into the stack trace, I found that NHibernate was throwing exceptions like "Unexpected row count: 2; expected: 1".

After some investigation, I found this exception was caused by the trigger implemented on a database table that NHibernates tries to update. 

Not sure if this is a database specific error. My application interacts with a MS SQL Server 2005 instance. The workaround is kinda simple. 

In the trigger, find the statement that does the updates. Wrap it with "SET NOCOUNT ON" and "SET NOCOUNT OFF". Issue fixed.
