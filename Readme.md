How to send email with Symfony
==============================

# Yahoo

* Install new `symfony` project and cd into that directory. This will isntall the latest version.
```
$ symfony new project
cd project
```
* # app/config/config.yml
```
swiftmailer:
    transport: "%mailer_transport%"
    encryption: ssl
    host:      "%mailer_host%"
    username:  "%mailer_user%"
    password:  "%mailer_password%"
    spool:     { type: memory }
```
* # app/config/parameters.yml
```
parameters:
    mailer_transport: smtp
    mailer_host: smtp.mail.yahoo.com
    mailer_user: your_account_name@yahoo.com
    mailer_password: your_account_password
```
* # AppBundle/Controller/DefaultController.php
```
/**
 * @Route("/send")
 */
public function sendAction()
{
    $this_is = 'this is';
    $the_message = ' the message of the email';
    $mailer = $this->get('mailer');

    $message = \Swift_Message::newInstance()
        ->setSubject('The Subject for this Message')
        ->setFrom($this->container->getParameter('mailer_user'))
        ->setTo('any_account_name@any_domain.whatever')
        ->setBody($this->renderView('default/email.html.twig', ['this'=>$this_is, 'message'=>$the_message]))
    ;
    $mailer->send($message);
    return new Response('<html><body>The email has been sent successfully!</body></html>');
}
```
* # app/Resources/views/default/email.html.twig
```
{{ this }}{{ message }}
```
* First start the symfony's build-in server:
```
project$ php bi/console server:run
```
* To run and send the email, access in the browser the following address `http://127.0.0.1/send`.