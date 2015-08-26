# CookBooks

## Create bundle administration

### Configuration :

To create an bundle administration we need to declare some parameters into the bundle configuration. Then open your config file into `Config/config.yml`

Define your controller

```yml
bundle:
  ...
  admin_controller:
    doc: BackBee\Bundle\DocumentationBundle\Controller\DocController
```

> You can declare controller as much as you want.

```yml
bundle:
  ...
  admin_controller:
    first: BackBee\Bundle\DocumentationBundle\Controller\FirstController
    second: BackBee\Bundle\DocumentationBundle\Controller\SecondController
    ...
```



And to finish we need to define the entry point of your admin interface.

```yml
bundle:
  ...
  admin_entry_point: doc.index
```

> The entry point it's compose by controller name that refer the name you declare in `admin_controller` section, dot separator and action name without the Action key word index refer to `indexAction`.

### Controller and Actions

Your admin controller class have to extend `AbstractAdminBundleController` to work correctly in automated mode.

```php
<?php
namespace BackBee\Bundle\DocumentationBundle\Controller;
use BackBee\Bundle\AbstractAdminBundleController;

class DocController extends AbstractAdminBundleController
{
}
```
> This represent the minimum necessary to implement your admin controller.


By extending `AbstractAdminBundleController` your controller can access to lot of methods:

- `getApplication()` Returns current application
- `getContainer()`Returns the dependency injection container
- `getRequest()` Returns current request
- `getEntityManager()` Returns doctrine entity manager
- `getRepository($entity)` Returns the repository corresponding of the entity name passed in paramater
- `isGranted($permission, $object)` Returns true if the current user have the permission to do the action on the object
- `getTranslator()` Returns translator object
- `render($template, $parameters)` Returns template parsed and rendered first argument is the template file starting from your rendering folder and $parameters are the variables necessary to render the template.
- `createResponse($content, $statusCode, $contentType)` Generate your own response, $content parameter is the body of the response, $statusCode correspond to the
- `redirect($url, $statusCode)` Create a redirection response
- `notifyUser($type, $message)` Generate an notification on the backoffice to the user there is 3 types availble `self::NOTIFY_SUCCESS` (green), `self::NOTIFY_WARNING` (orange) and `self::NOTIFY_ERROR` (red).

Now you have to implement some actions in your controller by default an action have to return an `String`, before we have defined that the entry point of the bundle administration it was do in the `indexAction`.
The index action return an array of values and need no parameters to work, `indexAction` it's compose of an `findAll` and one `render`

```php
public function indexAction()
{
    $docs = $this->getRepository('BackBee\Bundle\DocumentationBundle\Entity\Item')->findAll();

    return $this->render('Doc/Index.twig', ['docs' => $docs]);
}
```

To consult one object doc in particular we need an showAction. This action need an identifier to retrieve the good object and render it.

```php
public function showAction($id)
{
    $doc = $this->getRepository('BackBee\Bundle\DocumentationBundle\Entity\Item')->find($id);

    return $this->render('Doc/Doc.twig', ['doc' => $doc]);
}
```

###Routing

By default on all application, you to expose some route and do the matching with an controller and an action. In the bundle administration the routing is dynamic and automatic. To generate the URL you have 3 helpers to render the URL into your template.
Implicit route are defined like this: `Route is composed by the bundle name, controller name and action name separated by a dot`.
>In the case of the method `showAction` from `DocController` into `DocumentationBundle` the route is equal to `documentation.doc.show`. `documentation` matches to the name declared into `bundle.yml` file in your global configuration and `doc` is the name you gave to your controller into admin_controller section of your `config.yml` into your bundle configuration file.

 - `bundleAdminUrl($route, $parameters)` this helper generate your URL it need a "route" and optionally parameters.
 - `bundleAdminLink($route, $parameters, $httpMethod)` this helper generate all value necessary to an automated system, it work like the previous helper, but accept an third parameter to specify the HTTP method you want use when the link was cliqued. By default the HTTP method is GET.
 - `bundleAdminForm($route, $parameters, $httpMethod)` work like `bundleAdminLink` but by default the HTTP method is POST.

> `bundleAdminLink`  `bundleAdminForm` generate more than just an URL and it need to be goodly used.

> `bundleAdminUrl` is required only if you want create an custom system and bypass the automated system.

Usage of the `bundleAdminLink` into an template to reach the `showAction`
```twig
<a {{ this.bundleAdminLink("documentation.doc.show", {'id': 1})|raw }}>
    Show the first document
</a>
```

And the result before rendering is:

```html
<a data-bundle="link" href="/bundle/documentation/doc/show/1" data-http-method="read">
   Show the first document
</a>
```

>As you can see the id 1 do part of the URL and is not present as query. This is because your `showAction` method require an $id to work and the helper do automatically the matching behind the action and your parameters.

>In the case of `bundleAdminForm`, this helper work correctly only in `form` HTML tag.