Three Ways To Access Symfony's Container Outside a Controller Class
=============================================================

* # AppBundle/Outer/Outer.php
```
<?php

namespace AppBundle\Outer;

class Outer
{
    /**
     * @var\Doctrine\ORM\EntityManager
     */
     private $em;

     public function __construct($em)
     {
        $this->em = $em;
     }

     public function show()
     {
        return $this->em->getRepository('AppBundle:Person')->findAll();
     }
}
```

* # AppBundle/Controller/DefaultController.php
```
<?php

namespace AppBundle\Controller;

# use ...
use AppBundle\Outer\Outer;

class DefaultController extends Controller
{
    /**
     * @Route("/show")
     */
     public function showAction()
     {
        $em = $this->getDoctrine()->getManager();
        $outer = new Outer($em);
        $persons = $outer->show();
        return $this->render(..., ['persons'=>$persons]);
     }
}
```