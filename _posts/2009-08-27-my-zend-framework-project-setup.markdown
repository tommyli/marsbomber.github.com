---
layout: post
title: My Zend Framework project setup
wordpress_id: 32
wordpress_url: http://blog.elinkmedia.net.au/?p=32
date: 2009-08-27 19:57:40 +10:00
---
When starting implementing a new project, I want to start working on a basic code base, so that I can jump in the project specific coding. I believe I'm not alone, and that's why people fall in love with the rapid development scaffolding idea from Symfony, RoR, etc. I personally prefer a different path. I try to make a clean, generic project skeleton to cover the common ground. Building this skeleton project itself is a very valuable learning process. It makes you learn every single step you do, as opposed to the scaffolding approach, which does things for you without needing you to understand it.

Here is what I have done with learning and working on Zend Framework.

When I first started using Zend Framework, I was simply following the quick start guide and making it into my own project skeleton. I then start building my project from the skeleton project.

I then bought and read the book, Zend Framework in Action, by Rob Allen (It's a great book on Zend Framework even the sample code throughout the book have errors and mistakes and sometimes the topics from chapter to chapter do not follow very smoothly. It is still a very good book for developers just got across the ZF newbie mark). From the book, I adapted Rob's project structure and a lot of good ideas. I, of course, updated my home made ZF skeleton project.

From there on, I constantly learn new ideas from the ZF blogs and posts, hence keep enhancing my skeleton project. I want to share my skeleton project structure here, and hope it might help ZF adopters have a shortened learning curve.

<img class="size-full wp-image-58 " title="zf_skeleton" src="/images/posts/zf_skeleton.jpg" alt="Elink Media ZF Skeleton" width="271" height="533" />

It's very basic and very similar to the file structure generated by Zend_Tool.

I have 4 top level folders

1. admin. The "admin" folder is my project web root. Instead of "admin", it can be rename to anything.
2. application. The "application" folder holds the project core.
3. library. The library folder contains a local copy of Zend Framework, and also my own library files under sub folder "MyApp".
4. tests. Folder "tests" holds my unit testing files and the PHPUnit generated code coverage report

I deliberately expanded the "application" - "models" folder. Under "models", I have a sub folder "Db"  and other PHP model files. Reason is I am using a hybrid approach here to handle database interactions.

Sub folder "Db" contains domain objects implementing "Table Data Gateway" and "Row Data Gateway" design patterns using the Zend Framework shipped Zend_Db support.

Files on the same level as folder "Db" are domain classes that typically require resources from many table. These "shortcut" classes can be used under the situation where using the domain classes under the "Db" will impose too much of a performance hit. Using these "shortcut" classes means we are sacrificing abstraction to gain performance. These classes hence should be implemented and used carefully.

I am also looking at implementing Doctrine together with Zend Framework in my skeleton project. I'll post an updated diagram in a future post.
