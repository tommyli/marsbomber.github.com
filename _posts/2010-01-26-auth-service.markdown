---
layout: post
title: Authentication service
wordpress_id: 137
wordpress_url: http://blog.elinkmedia.net.au/?p=137
date: 2010-01-26 20:22:47 +11:00
---
This is a followup to my previous post "<a href="http://blog.elinkmedia.net.au/2010/01/24/zf-authentication-using-doctrine/">ZF Authentication using Doctrine</a>". After posting my implementation of using a ZendX doctrine auth adapter. I got some feedback from Jon Lebensold from <a href="http://www.zendcasts.com/" target="_blank">ZendCasts</a>. The issue raised is that my implementation makes the controller fat, which is considered as a bad practice. I cannot agree more on that. So ... I tried to refactor the code and come up with the introduction of a service layer class named "AuthenticationService".

Now the LoginController class is made thinner with the following code.
{% highlight php %}
public function loginAction()
{
    $authService = new Model_AuthenticationService();
    if($authService->isAuthenticated() == true) {
        $this->_redirect('/index');
    }

    if($this->getRequest()->isPost()) {
        $this->view->headTitle('Login');
        // collect the data from the user
        $loginUsername = $this->getRequest()->getParam('username', '');
        $loginPassword = $this->getRequest()->getParam('password', '');

        $authResult = $authService->authenticate(
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
        $this->_redirect('/login');
    }
}
{% endhighlight %}

Code for AuthenticationService.php
{% highlight php %}
class Model_AuthenticationService
{
    private $_authenticationMessage = '';

    public function getAuthenticationMessage()
    {
        return $this->_authenticationMessage;
    }

    public function isAuthenticated()
    {
        return Zend_Auth::getInstance()->hasIdentity();
    }

    public function authenticate($username, $password)
    {
        $doctrineAuthAdapter = new ZendX_Doctrine_Auth_Adapter(
            Doctrine_core::getConnectionByTableName('Model_User')
        );
        $doctrineAuthAdapter->setTableName('Model_User u')
            ->setIdentityColumn('u.username')
            ->setCredentialColumn('u.password')
            ->setIdentity($username)
            ->setCredential(Model_Utility::encryptPassword($password));

        $myAuth = Zend_Auth::getInstance();
        $authResult = $myAuth->authenticate($doctrineAuthAdapter);
        if(!$authResult->isValid()) {
            $this->_authenticationMessage = 'You have entered invalid username and password.';
            return false;
        } else {
            $identity = $doctrineAuthAdapter->getResultRowObject(null, 'password');
            $myAuth->getStorage()->write($identity);
            return true;
        }
    }
}
{% endhighlight %}

As we can see, the AuthenticationService class is now doing most of the heavy lifting stuff. The controller is left with the duty of checking user login state and dispatching appropriate actions when necessary.

Hope this makes sense, and again more than happy to discuss.

Reference
<ul>
	<li><a href="http://blog.elinkmedia.net.au/2010/01/24/zf-authentication-using-doctrine/" target="_blank">http://blog.elinkmedia.net.au/2010/01/24/zf-authentication-using-doctrine/</a></li>
	<li><a href="http://zendcasts.com" target="_blank">http://zendcasts.com</a></li>
</ul>
