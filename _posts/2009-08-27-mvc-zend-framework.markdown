--- 
layout: post
title: MVC & Zend Framework
wordpress_id: 21
wordpress_url: http://blog.elinkmedia.net.au/?p=21
date: 2009-08-27 11:26:24 +10:00
---
As high speed internet becomes available to more and more people, the computer world gets its focus back to the centralised computing style. This opens up  opportunities to web development companies. They are no longer just building "another website". More complicated web applications get in the radar. The increased complexity in developing web applications requires web development methodology change, as well as web developers' programming mindset change.

Programmers can no longer hack their way through, for a decent sized project anyway. A typical web application project will take longer time to develop and require a lot more resources to complete the work. And thanks to our high speed internet again, these people resources can be everywhere around the globe. A clean and structured way of developing projects becomes required. For the sake of not reinventing the wheels, having a jump start and working on a robust code base, a web development framework(s) is normally used.

The MVC pattern becomes a de facto to web applications these days. I'm not saying MVC is the only pick. We can still implement our project in other styles just as efficient and effective. MVC is however commonly adapted and used in the community (the PHP community at least). If you are not sure about what MVC is, you can have a look on Wikipedia or go <a href="http://www.phppatterns.com/docs/design/archive/model_view_controller_pattern" target="_blank">HERE</a> for a PHP specific explanation.

The guidance of MVC should lead us to have a clean and purposely build project structure, which should be self-explanatory in terms of what class does what.

To take the MVC approach further, we will then naturally start hunting for a well developed, enhanced and maintained framework. There are quite a number of PHP framework libraries out there. They all have pros and cons and I'll leave that to your own research.

I have used the Zend Framework for over a year and a half now. The reason I chose Zend over the others is because of its "use at will" characteristics. This means for those adhoc and small through-away jobs, I can still benefit from using Zend Framework's robust base classes as my external libraries, without the constraints of having to follow the MVC pattern. This means you are never forced to over engineer projects. Let's face it, there are always jobs that you never need a full blown MVC design. Determine a project implementation strategy is a separate issue to this post. Hopefully we can do another post on that later.

Apart from the "use at will" flexibility, we have to mention the robust code base that Zend and the PHP community provides. Zend Framework gives us a jump start on any MVC styled projects. We can use Zend_Tool to generate a basic Zend Framework recommended project file structure. However we still have the flexibility to alter and change the structure to make it fit in our company's  convention, as long as it makes sense to the developers. From there on, any developers who are familiar with Zend Framework should be able to jump in and start working.
