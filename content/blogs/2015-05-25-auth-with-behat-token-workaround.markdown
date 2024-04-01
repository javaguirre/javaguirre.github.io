---
title: 'Auth with Behat token workaround'
date: 2015-05-25 15:35:26 +0000
tags: [symfony,php,behat]
---
[Behat][behat] is a very nice behaviour-driven framework well suited for *Symfony*.

A workaround is always some *hack* you need to solve a problem not in an optimal way, but in my case I found it good enough to solve my problem in a timely fashion.

This is about user authentication for Behat and how you could do It.

<pre><code class="language-php">/**
 * @Given /^I am logged in as "([^"]*)"$/
 */
public function loggedIn($email)
{
    // Find a user
    $user = $this->myservice->find(
        'User',
        array('email' => $email)
    );

    if ($user) {
        $token = new UsernamePasswordToken(
            $user, null, 'main', $user->getRoles()
        );

        $this->security->setToken($token);

        $granted = $this->security->isGranted('ROLE_USER');

        if (!$granted) {
            throw new Exception('User is not logged in');
        }
    }
}
</code></pre>

I tried to solve it the way it *should*, creating a token inside a *Context* and It seemed to work (`isGranted` was returning an authenticated valid value), but the security token wasn't used in the Browser client launching the headless browser.

I tried to do the same inside the client and [following other][tuto] [tutorials][tuto2], checking if something was missing, but didn't work and at some point I didn't want to spend more time.

I could solve the problem in a *behavioral* way, without jumping out the flow of the site. [People recommend][stack] to use the web navigation flow on Behat (filling out forms and such) to authenticate, but when you are trying to login with social networks, the strategy has to be a bit different.

In my case I could have just created some logic inside our login process and trigger the *testing mock authentication* when the `test` Symfony environment is active, but I didn't want to set that logic inside the login process of our service.

I decided to create a `TestController` to log in the user of our choosing while in the test environment, It works more or less like the [impersonating symfony feature][impersonating].

We create a new `/login_test` route linking to `Test:login` action.

<pre><code class="language-bash"># routing.yml
test:
    resource: "@MainBundle/Resources/config/routing_test.yml"
</code></pre>

<pre><code class="language-bash"># routing_test.yml
login_test:
    pattern: /login_test
    defaults: { _controller: MainBundle:Test:login }
</code></pre>

Inside the controller we just create the authentication token depending on the user retrieved by email.

<pre><code class="language-php"># TestController
&lt;?php

namespace Acme\MainBundle\Controller;

use Symfony\Bundle\FrameworkBundle\Controller\Controller;
use Symfony\Component\HttpKernel\Exception\AccessDeniedHttpException;

use Acme\CoreBundle\Model\Environment;

class TestController extends Controller
{
    public function loginAction()
    {
        $this->checkTestEnvironment();
        $email = $this->getRequest()->query->get('email');

        // Gets a user by email or whatever you want
        $user = $this->getObjectOr404('User', $email);

        $this->authenticateUser($user);

        return $this->setRedirect('homepage');
    }

    private function checkTestEnvironment()
    {
        $env = $this->get('kernel')->getEnvironment();

        // Environment::TEST is 'test'
        if ($env != Environment::TEST) {
            throw new AccessDeniedHttpException('No access');
        }
    }

    /**
     * Authenticates a user
     *
     * @param User $user User to be authenticated
     */
    private function authenticateUser($user)
    {
        $token = new UsernamePasswordToken(
            $user,
            null,
            // The firewall name in your security.yml
            Environment::DEFAULT_FIREWALL,
            $user->getRoles()
        );
        $this->container->get('security.context')
            ->setToken($token);
    }
}
</code></pre>

Easy enough if you know how [symfony security layer][symfony_security] works.

Then in you Behat context, you could just do:

<pre><code class="language-php">// Inside the Behat Context

const LOGIN_VIEW = '/login_test';

...

/**
 * @Given /^I am logged in as "([^"]*)"$/
 */
public function loggedIn($email)
{
    $user = $this->myservice->find(
        'User',
        array('email' => $email)
    );

    if ($user) {
        $this->getSession()->visit(
            $this->baseUrl . self::LOGIN_VIEW . '?email=' . $email
        );
    }
}
</code></pre>

That's It! In our scenarios:

<pre><code class="language-bash"># user.feature

Scenario: Can view header menus with all the options
    Given I am logged in as "fake&#64;email.com"
    And I am on "/pepe"
</code></pre>

Questions? shoot!

[behat]: https://behat.readthedocs.org/en/v2.5/
[tuto]: http://robinvdvleuten.nl/blog/handle-authenticated-users-in-behat-mink/
[tuto2]: http://symfony.com/doc/current/cookbook/testing/simulating_authentication.html
[stack]: https://stackoverflow.com/questions/8108001/behat-authenticate-symfony2-user
[impersonating]: http://symfony.com/doc/current/cookbook/security/impersonating_user.html
[symfony_security]: http://symfony.com/doc/current/book/security.html

