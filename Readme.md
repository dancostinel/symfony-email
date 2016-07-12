Codeception
===========

## Simple codeception setup for testing Symfony project

* Install new `symfony` project and cd into that directory. This will install the latest version.
```
$ symfony new project
cd project
```
* Install `codeception` into the project
```
project$ composer require codeception/codeception
```
* Initialize `codeception`. This will create all needed classes.
```
project$ vendor/bin/codecept bootstrap
```
* To create a new acceptance test:
```
project$ vendor/bin/codecept generate:cest acceptance YourTestName
```
* Configure `tests/acceptance/parameters_test.yml`:
```
user: 'your_db_user'
password: 'your_db_pass'
db1: 'your_db_name'
```
* Configure `tests/acceptance.suite.yml`:
```
class_name: WebGuy
modules:
    enabled:
        - PhpBrowser
        - WebHelper
    config:
        PhpBrowser:
            url: 'http://project.lh/app_dev.php' # the URL of your project
```
* Simple test code: `login`:
```
# tests/acceptance/YourTestNameCest.php - this was generated at step 4

<?php
use \WebGuy;

class YourTestNameCest
{
    private $admin_name = 'admin';
    private $admin_pass = 'adminpass';
    private $user_name  = 'user';
    private $user_pass  = 'userpass';
    private $fail_user  = 'failuser';
    private $fail_pass  = 'failpass';

    public function _before()
    {

    }

    public function _after()
    {

    }

    //tests
    private function testAsAdmin(WebGuy $I)
    {
        //fail login as admin
        $I->wantTo("Fail the login as an admin");
        $I->amOnPage('/login');
        $I->submitForm('#login_form', array( # MAKE SURE YOUR FORM HAS THE ID LIKE THIS: id="login_form", OR WHATEVER YOU WANT...
            '_username' => $this->fail_user,
            '_password' => $this->fail_pass,
            'submit' => '_submit'
        ));
        $I->seeCurrentUrlEquals('/app_dev.php/login');
        $I->see('Invalid credentials.');

        //login as an admin and test the home-page, if after the success login, you redirect the admin back to home page
        $I->wantTo("Login as an admin");
        $I->amOnPage('/login');
        $I->submitForm('#login_form', array(
            '_username' => $this->admin_name,
            '_password' => $this->admin_pass,
            'submit' => '_submit'
        ));
        $I->seeCurrentUrlEquals('/app_dev.php/');
        $I->see('Logout');
        // create whatever asserts you need for testing the page
    }

    private function testAsUser(WebGuy $I)
    {
        //fail login as regular user
        $I->wantTo("Fail the login as a regular user");
        $I->amOnPage('/login');
        $I->submitForm('#login_form', array(
            '_username' => $this->fail_user,
            '_password' => $this->fail_pass,
            'submit' => '_submit'
        ));
        $I->seeCurrentUrlEquals('/app_dev.php/login');
        $I->see('Invalid credentials.');

        //login as regular user and test the home-page, if after the success login, you redirect the admin back to home page
        $I->wantTo("Login as regular user");
        $I->amOnPage('/login');
        $I->submitForm('#login_form', array(
            '_username' => $this->user_name,
            '_password' => $this->user_pass,
            'submit' => '_submit'
        ));
        $I->seeCurrentUrlEquals('/app_dev.php/');
        $I->see('Logout');
        // create whatever asserts you need for testing the page
    }

    //main method
    public function completeScenario(WebGuy $I)
    {
        $this->testAsAdmin($I);
        $this->testAsUser($I);
    }
}
```
* Run the test
```
project$ vendor/bin/codecept run --steps tests/acceptance/YourTestNameCest.php
```

### IMPORTANT: Remember to always create a test database. Do `NOT` work with the prod database! You can dump your prod database, and paste the generated sql code inside `tests/_data/dump.sql` file