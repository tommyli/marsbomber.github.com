--- 
layout: post
title: Authentication service
wordpress_id: 137
wordpress_url: http://blog.elinkmedia.net.au/?p=137
date: 2010-01-26 20:22:47 +11:00
---
This is a followup to my previous post "<a href="http://blog.elinkmedia.net.au/2010/01/24/zf-authentication-using-doctrine/">ZF Authentication using Doctrine</a>". After posting my implementation of using a ZendX doctrine auth adapter. I got some feedback from Jon Lebensold from <a href="http://www.zendcasts.com/" target="_blank">ZendCasts</a>. The issue raised is that my implementation makes the controller fat, which is considered as a bad practice. I cannot agree more on that. So ... I tried to refactor the code and come up with the introduction of a service layer class named "AuthenticationService".

Now the LoginController class is made thinner with the following code.

[php]
public function loginAction()
{
    $authService = new Model_AuthenticationService();
    if($authService-&gt;isAuthenticated() == true) {
        $this-&gt;_redirect('/index');
    }

    if($this-&gt;getRequest()-&gt;isPost()) {
        $this-&gt;view-&gt;headTitle('Login');
        // collect the data from the user
        $loginUsername = $this-&gt;getRequest()-&gt;getParam('username', '');
        $loginPassword = $this-&gt;getRequest()-&gt;getParam('password', '');

        $authResult = $authService-&gt;authenticate(
            $loginUsername,
            $loginPassword
        );

        if($authResult == true) {
            // auth good, do your logic
        }
        else {
            // auth bad, do your logic
        }
    } else {
        $this-&gt;_redirect('/login');
    }
}
[/php]

Code for AuthenticationService.php

[php]
class Model_AuthenticationService
{
    private $_authenticationMessage = '';

    public function getAuthenticationMessage()
    {
        return $this-&gt;_authenticationMessage;
    }

    public function isAuthenticated()
    {
        return Zend_Auth::getInstance()-&gt;hasIdentity();
    }

    public function authenticate($username, $password)
    {
        $doctrineAuthAdapter = new ZendX_Doctrine_Auth_Adapter(
            Doctrine_core::getConnectionByTableName('Model_User')
        );
        $doctrineAuthAdapter-&gt;setTableName('Model_User u')
            -&gt;setIdentityColumn('u.username')
            -&gt;setCredentialColumn('u.password')
            -&gt;setIdentity($username)
            -&gt;setCredential(Model_Utility::encryptPassword($password));

        $myAuth = Zend_Auth::getInstance();
        $authResult = $myAuth-&gt;authenticate($doctrineAuthAdapter);
        if(!$authResult-&gt;isValid()) {
            $this-&gt;_authenticationMessage = 'You have entered invalid username and password.';
            return false;
        } else {
            $identity = $doctrineAuthAdapter-&gt;getResultRowObject(null, 'password');
            $myAuth-&gt;getStorage()-&gt;write($identity);
            return true;
        }
    }
}
[/php]

As we can see, the AuthenticationService class is now doing most of the heavy lifting stuff. The controller is left with the duty of checking user login state and dispatching appropriate actions when necessary.

Hope this makes sense, and again more than happy to discuss.

Reference
<ul>
	<li><a href="http://blog.elinkmedia.net.au/2010/01/24/zf-authentication-using-doctrine/" target="_blank">http://blog.elinkmedia.net.au/2010/01/24/zf-authentication-using-doctrine/</a></li>
	<li><a href="http://zendcasts.com" target="_blank">http://zendcasts.com</a></li>
</ul>
