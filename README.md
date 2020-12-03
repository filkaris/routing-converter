# Routing Converter

Converts your routing.yml files to annotations.

## Example 

**Before**

````yaml
#app/config/routing.yml

app_homepage:
    path:   /home/{id}
    defaults:  { _controller: AppBundle:Home:index}
    requirements:
        id:  \d+
    methods:  [GET, HEAD]
````
**After**

````php
<?php
# AppBundle/Controller/HomeController.php

use Sensio\Bundle\FrameworkExtraBundle\Configuration\Route;

class HomeController{
    
    /**
     * @Route(name="app_homepage", path="/home/{id}", methods={"GET","HEAD"}, requirements={"id": "\d+"})
     */
    public function indexAction($id){
    
    }
    
}

````
## Installation

```
composer require stoakes/routing-converter
```

Then add to your `AppKernel.php`

````php
<?php

        if (in_array($this->getEnvironment(), array('dev', 'test'))) {
            $bundles[] = 
  new Stoakes\RoutingConverterBundle\StoakesRoutingConverterBundle();
  
  }
````

And to your `services.yml`:

````yaml
    Stoakes\RoutingConverterBundle\Command\MigrateRoutesCommand:
        tags:
            - { name: 'console.command' }
        arguments:
          $resolver: '@debug.controller_resolver'
````

Use it : `php bin/console stoakes:convert_yml PATH_TO_A_ROUTING.YML_FILE [PREFIX]`

`php bin/console stoakes:convert_yml ./src/AppBundle/Resources/config/routing.yml /fr
`

## How does it works

That bundle provides a convertion command that uses symfony core components (routing, http kernel) and redaktilo to modify your Controllers.

## Tests

You : " - Hey that bundle is untested !!"

Me : " - I am working it. However I have created it in 2 evenings to convert routing in one of my app, and it works quite well"

## Known bugs

Be careful about this: `null` defaults will be converted to empty strings, example:

    user_edit:
      path: /user/edit/{id}
      defaults: { _controller: 'UserController::editAction', 'id': null }

becomes:
  
    /**
     * @Route(name="user_edit", path="/user/edit/{id}", defaults={"id": ""})
     */

This may or may not impact you. Patches are welcome.
