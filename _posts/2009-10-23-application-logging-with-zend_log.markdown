---
layout: post
title: Application logging with Zend_Log
wordpress_id: 91
wordpress_url: http://blog.elinkmedia.net.au/?p=91
date: 2009-10-23 12:20:34 +11:00
---
It's been a while since my last post here... I've been pretty busy with projects and other things. However I did manage to finish the book I recently bought, Zend Enterprise PHP Patterns. It's an interesting book. Even the book title mentions PHP Patterns, it's actually more about good practice with Zend related technologies. It's a shame that not every PHP developer will have the chance to access those Zend products (me included ...), however I did manage to get some interesting ideas out of the book. To name one, application logging. I have to say, I have over looked the aspect of application level logging for a long time. I so rely on the server access and error log, and there are instances that I fail to find necessary information from those big ... massive server log files.

So here's how I did my application logging with Zend_Log.

In my Bootstrap.php, I added the following init method. You can see the logger object is then stored in the registry. You can see I'm not doing anything fancy here, just logging to a file, which is specified in my ini file.
{% highlight php %}
protected  function _initApplicationLogger(){
  // init firebug log writer
  $writerFirebug = new Zend_Log_Writer_Firebug();
  $filterFirebug = new Zend_Log_Filter_Priority(Zend_Log::DEBUG, '==');
  $writerFirebug->addFilter($filterFirebug);

  // log file
  $logFile = Zend_Registry::get('config')->logFiles;

  // application log writer
  $writerFileApplication = new Zend_Log_Writer_Stream($logFile->application);
  $filterFileApplication = new Zend_Log_Filter_Priority(Zend_Log::INFO, '&lt;=');
  $writerFileApplication->addFilter($filterFileApplication);

  // error log writer
  $writerFileApplicationError = new Zend_Log_Writer_Stream($logFile->error);
  $filterFileApplicationError = new Zend_Log_Filter_Priority(Zend_Log::ERR, '==');
  $writerFileApplicationError->addFilter($filterFileApplicationError);

  // security log writer
  $writerFileApplicationSecurity = new Zend_Log_Writer_Stream($logFile->security);
  $filterFileApplicationSecurity = new Zend_Log_Filter_Priority(Zend_Log::ALERT, '==');
  $writerFileApplicationSecurity->addFilter($filterFileApplicationSecurity);

  // add writers to logger
  $logger = new Zend_log($writerFirebug);
  $logger->addWriter($writerFileApplication);
  $logger->addWriter($writerFileApplicationError);
  $logger->addWriter($writerFileApplicationSecurity);

  Zend_Registry::set("logger" , $logger);
}
{% endhighlight %}

Next, I want to log my application errors, so the natural place to do this is the ErrorController.php file.  I just added the following lines to the bottom of the errorAction() method
{% highlight php %}
$this->logger->log($errors->exception->getMessage() . PHP_EOL . $errors->exception->getTraceAsString(), Zend_Log::ERR);
{% endhighlight %}

Since the application I'm writing is accessible by authenticated users only,  it is important to log security breaches. How I did this is a bit interesting. I think there must be better ways to do this ... but I don't know how ... so here it is.

I have implemented my own controller action class, MyApp_Controller_Action. It extends the standard Zend_Controller_Action. So all my protected controllers extend MyApp_Controller_Action instead of Zend_Controller_Action.
{% highlight php %}
class MyApp_Controller_Action extends Zend_Controller_Action {
  ...
  protected $logger;
  public function init() {
    ...
    $this->logger = Zend_Registry::get("logger");
  }

  ...

  public function checkAcl() {
    ...
    if($noGood) {
      $this->logSecurityBreach();
    }
  }

  protected function logSecurityBreach() {
    // log some basic info here. extend it to include extra info that you need
    $errorLog = 'Invalid Access Attempt' . PHP_EOL .
      'Resource: ' . $this->getRequest()->getRequestUri() . PHP_EOL .
      'Offending IP: ' . $this->getRequest()->getClientIp() . PHP_EOL .
      'Offending user: ' . $this->identity->Username;
      $this->logger->log($errorLog, Zend_Log::ALERT);
  }
}
{% endhighlight %}

Since the logSecurityBreach function is made "protected", so that in the controllers, who extend MyApp_Controller_Action, can call the logSecurityBreach method whenever there are any other access control violations. For example, a specific security error can be logged if a logged in user tweaked the URL (like an ID param/token in the URL) to access some other resources that should not be visible to his login.

So here it is. A basic use of Zend_Log. Hope it makes sense.

Reference
<ul>
	<li><a href="http://www.apress.com/book/view/9781430219743" target="_blank">http://www.apress.com/book/view/9781430219743</a></li>
</ul>
