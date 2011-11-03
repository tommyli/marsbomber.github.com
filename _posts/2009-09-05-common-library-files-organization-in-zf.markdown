--- 
layout: post
title: Common library files organization in ZF
wordpress_id: 71
wordpress_url: http://blog.elinkmedia.net.au/?p=71
date: 2009-09-05 18:07:29 +10:00
---
Once you have used ZF for a couple of projects, you will find that there are library files actually live independent to whichever application you are implementing. These common library files should be reusable by all you ZF projects. Now the question is where you should place these files so that a typical ZF project can make use of them without a glitch?

I have described my ZF project structure in an earlier post <a href="http://blog.elinkmedia.net.au/2009/08/27/my-zend-framework-project-setup/" target="_blank">HERE</a>, and now I want to elaborate a bit more on folder "library/MyApp", because that's where I keep my common library files.
<p style="text-align: left;"><img class="size-full wp-image-72 aligncenter" title="ZF_Lib_MyApp" src="http://blog.elinkmedia.net.au/wp-content/uploads/2009/09/ZF_Lib_MyApp.jpg" alt="ZF_Lib_MyApp" width="187" height="126" />I have a rather simple version shown here. Basically only 3 things to look at.</p>
<strong>Let's start with "MyApp/View/Helper". </strong>

This is the place where I keep all my project independent View Helper files. Things like
<ul>
	<li>baseUrl, as the name says, return the base URL for your application</li>
	<li>loginUserInfo, assemble things like login username, last login time, and even your application logout text/link</li>
	<li>flashMessenger, I use a customized view helper in conjunction with ZF's flash messenger action helper to make different markups for different type of error messages. i.e. good message in green, bad message in red.</li>
	<li>etc</li>
</ul>
I assume you already know that you need to follow ZF's naming conversion for these helpers files to make  autoloading painless. For example, the baseUrl view helper, you will end up with something like this

[php]

class MyApp_View_Helper_BaseUrl {
 function baseUrl() {
 $fc = Zend_Controller_Front::getInstance();
 return $this-&gt;_baseUrl =  $fc-&gt;getBaseUrl();
 }
}[/php]

<strong>Next, let's look at ViewSetup.php file under "MyApp/Controller/Plugin"</strong>

I basically have this file built based on Rob Allen's book "<a href="http://www.zendframeworkinaction.com/" target="_blank">Zend Framework in Action</a>". This is the file that sets up your view using various view related hooks. Particularly I have 3 hooks looked after.
<ul>
	<li>dispatchLoopStartup
Use this hook to setup things like doctype, application common JS and CSS file. And also register our MyAPP view helpers</li>
	<li>preDispatch
I personally use this hook to inject more CSS and/or JS into the script header. Because at this point, we know the view is dispatched by which controller. We could then load in any controller specific resources.</li>
	<li>postDispatch
I use this hook to set my application HTML head title. ZF allows you to set a separator for your header title tokens. And I always add 1 more token, which is the application name to the title. So ... your paper header title can look something like this "ActionName - ControllerName - ApplicationName". It somewhat makes sense to me, but if you do not like the idea, just do not implement this hook.</li>
</ul>
To make a use of this ViewSetup file, I have this too init function in my bookstrap

[php]protected function _initCustomLibraryNamespaces() {
 $loader = Zend_Loader_Autoloader::getInstance();
 $loader-&gt;registerNamespace('MyApp_');
 }

 protected function _initViewHelpers() {
 $frontController = Zend_Controller_Front::getInstance();

 $frontController-&gt;registerPlugin(new MyApp_Controller_Plugin_ViewSetup(), 98);
 $this-&gt;bootstrap('layout');
 }[/php]

<strong>OK, last bit, let's talk about "MyApp/Controller/Action.php"</strong>

A bit background first, I recently just started looking into Ruby on Rails as a self learning course. One RoR programming principle is DRY (Don't Repeat Yourself). The same motivation made me implement this "MyApp/ControllerAction.php" file. There are a lot of common things you do with pretty much all your application controller classes. Things like authentication, maybe ACL (depends on your application specific requirement), flash messenger, logger, etc. I hate repeat myself, that's why I have these common tasks or methods implemented here. So ... the Action.php class declaration goes like this

[php]class MyApp_Controller_Action extends Zend_Controller_Action[/php]

It actually extends the Zend_Controller_Action, so that it should not work any unexpected at all.
and a typical application controller will have something like this

[php]class IndexController extends MyApp_Controller_Action[/php]

and of course if a certain controller does NOT require any extras implemented in the customized Action.php file, you can make it easily fall back to something like this

[php]class ErrorController extends Zend_Controller_Action[/php]

That's it! Hope it helps and let me know if you have better ways.
