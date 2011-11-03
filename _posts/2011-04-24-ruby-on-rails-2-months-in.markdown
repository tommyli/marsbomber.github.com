--- 
layout: post
title: Ruby on Rails, 2 months in
wordpress_id: 377
wordpress_url: http://blog.elinkmedia.net.au/?p=377
date: 2011-04-24 22:41:35 +10:00
---
I've started my "senior" developer role in doing Ruby on Rails projects for a bit over 2 months. Reason I quoted the "senior" bit is that I'm really a newbie to it. I guess the seniority is more about general web based application development rather than RoR. Having said that, RoR isn't that foreign to me at all. I started studying it a year ago. However my self-study doesn't compensate my lack of real world working experience. In the past 2 months, lots of ideas and knowledges are exchanged between me and my peers. I learnt a lot of Rails of course. And I believe I have also contributed some non-rails thinking into the Rails project.

2 months in, I learnt that Rails is moden and rapid in doing web development (I absolutely love it). However it's convention over configuration nature sometimes dictates the way developers think too much. Somehow, developers' creativities become canvased by Rails. You find yourself battling with routes, ActiveRecord models, DRY controllers a lot. The application architectural deliberating stage is somewhat overlooked, if not bypassed. Most of the time, this will lead to future potential problems, which could be security issues, scaling issues, performance issues or just tedious labour work in doing repetitive tasks.

As I'm still learning, what I believe now is that don't let Rails control how you author software,
<ul>
	<li>write non AR models to describe your domain if required</li>
	<li>use service layer classes to encapsulate business logic if it makes sense</li>
	<li>off load long running, non critical tasks to a job queue (loving the resque gem now) when it's appropriate</li>
	<li>the list goes on</li>
</ul>
Let Rails help you instead,
<ul>
	<li>use its powerful command line tool</li>
	<li>utilise its ecosystem, all those wonderful gems</li>
	<li>make a use of its RESTful controllers, exposing API is never as easy as before</li>
	<li>look into it's respond_to feature, making your RESTful API respond to XML, json, or whatever is made super easy</li>
	<li>the list goes on</li>
</ul>
To summarise, the basic software engineering practices apply to Rails or non Rails applications the same way.

Bend the rails to let your train reach it's destination. Expect more Rails related posts from now on.
