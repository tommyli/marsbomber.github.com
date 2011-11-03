--- 
layout: post
title: ZF authentication using Doctrine
wordpress_id: 131
wordpress_url: http://blog.elinkmedia.net.au/?p=131
date: 2010-01-24 09:27:21 +11:00
---
Edit: There's a follow up article <a href="http://blog.elinkmedia.net.au/2010/01/26/auth-service/">http://blog.elinkmedia.net.au/2010/01/26/auth-service/</a>

As one of my everyday morning tasks, I just checked the rss feeds from my news reader. Interesting enough, one of my favourite blog authors, Jon Lebensold from ZendCasts, just published a new post on "<a href="http://www.zendcasts.com/writing-a-zend_auth_adapter-with-doctrine/2010/01/" target="_blank">Writing a Zend_Auth_Adapter with Doctrine</a>".

The issue that we are trying to solve is to handle user login authentication from an application, which uses Zend Framework and Doctrine ORM. Jon took the approach of writing a custom zend auth adaptor to accomplish the task. I watched the video from ZendCasts, and clearly this is another quality video and it will just work for you.

However here, I want to show you an alternative approach to solve the same problem. I basically used an already written authentication doctrine adapter from the ZendX incubator. The class file can be found <a href="http://framework.zend.com/svn/framework/extras/incubator/library/ZendX/Doctrine/Auth/Adapter.php" target="_blank">here</a>.

To autoload the ZendX_Doctrine_Auth_Adapter class file, obviously you need to include the file in the correct directory inside the "library" directory. So that is "library/ZendX/Doctrine/Auth/Adapter.php".

Now let's look at some code. Please note that I am only showing topic-related code here, all other security checking, error checking code have been taken out.

[php]
class LoginController extends Zend_Controller_Action
{
    public function loginAction()
    {
        // collect the data from the user
        $loginUsername = $this-&gt;getRequest()-&gt;getParam('username', '');
        $loginPassword = $this-&gt;getRequest()-&gt;getParam('password', '');

        $myAuth = Zend_Auth::getInstance();

        // do the authentication
        $authAdapter = $this-&gt;_getAuthAdapter($loginUsername, $loginPassword);
        $result = $myAuth-&gt;authenticate($authAdapter);
        if (!$result-&gt;isValid()) {
            // take care of invalid login
        } else {
            // empty password
            $identity = $authAdapter-&gt;getResultRowObject(null, 'password');
            $myAuth-&gt;getStorage()-&gt;write($identity);

            // all good, do you redirect or whatever
        }
    }

    protected function _getAuthAdapter($username, $password)
    {
        $authAdapter = new ZendX_Doctrine_Auth_Adapter(Doctrine::getConnectionByTableName('Model_User'));

        $encryptedPassword = Model_Utility::encryptPassword($password);

        $authAdapter-&gt;setTableName('Model_User u')
            -&gt;setIdentityColumn('u.username')
            -&gt;setCredentialColumn('u.password')
            -&gt;setIdentity($username)
            -&gt;setCredential($encryptedPassword);

        return $authAdapter;
    }

[/php]

Ok. Most of the code should be pretty self explanatory. The authentication is done with the following assumptions.
<ul>
	<li>Your login users are stored in a table called "user", with at least 2 columns, "username" and "password".</li>
	<li>You have your Doctrine "Model_User" class setup.</li>
	<li>If you store encrypted or hashed passwords in your database (I do), the encryption logic is handled by a static method call from a utility class, "Model_Utility".</li>
</ul>
This is it. In our case, we do not need to create an authentication method in "Model_User" as Jon showed us in his video tutorial. The authentication magic is all done by the "ZendX_Doctrine_Auth_Adapter" class.

Hope you find this useful and let me know if you have issues or suggestions.

Reference
<ul>
	<li><a href="http://www.zendcasts.com/writing-a-zend_auth_adapter-with-doctrine/2010/01/" target="_blank">http://www.zendcasts.com/writing-a-zend_auth_adapter-with-doctrine/2010/01/</a></li>
</ul>
