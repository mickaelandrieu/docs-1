# Components

## The Routing component

This component is used in BackBee to map an HTTP request to a set of configuration variables.
Note this component is an extension of [Symfony Routing Component](http://symfony.com/doc/current/components/routing/introduction.html), so the official documentation is
still valid.

## How the Routing component work ?

To set a basing routing system, you need at least to set and configure three objects:

* A ``BackBee\Routing\RouteCollection`` instance, which contains routes definitions (instance of ``BackBee\Routing\Route``);
* A ``BackBee\Routing\RequestContext`` instance, which embed the current request;
* Finaly, a ``RequestMatcher`` instance, in charge of performs the mapping from request to a Route.

In BackBee CMS, all routes are declared in ``BackBee/Config/route.yml`` file, but obviously you can create your own routes in Standard
Edition if you have frameworks needs outside the "CMS" context (backend bundles for instance).

The ``route.yml`` is parsed by the Yaml component and BackBee create a ``RouteCollection`` instance, which is stored in Container as the
``routing`` service (Symfony developpers should notice this behavior is a little bit different to the Symfony ``router`` service which is not available in BackBee).

In this file, you can find all the routes (and so on, paths) from the actual REST API used to communicate with the Javascript client when you are logged to BackBee Standard Edition.

Also, some routes are defined to handle some resources and finaly the more important route is the default route.

Let's take an example:

```yaml
# Access all media
bb.media.list:
  pattern: /rest/{version}/medialist
  defaults:
    _action: medialistAction
    _controller: BackBee\Rest\Controller\MediaController # can be a service definition
  requirements:
    _method: GET
```

A ``BackBee\Route`` is defined by a pattern (mostly an url path) where we can use variables, have some mandatory defaults
(binded to a Controller and an action) and may have some requirements.

## Routing component requirements

The ``requirements`` property allow use to add requirements used by the ``RequestMatcher`` to map an url to a request.

This is an example of some available requirements:

```yaml
# this route is very difficult to match
route.name:
  pattern: /route/{a}/{b}/{c}/difficult
  defaults:
    _action: fooAction
    _controller: Baz\BarController
  requirements:
    a: .+ # accept regex conditions
    b: \d+
    _method:POST
    condition: "context.getMethod() in ['GET', 'HEAD'] and request.headers.get('User-Agent') matches '/firefox/i'" # use the Symfony Expression Language component
```

The ``condition`` requirement can use the Symfony Expression Language component and have access to:
* ``context``: the instance of RequestContext;
* ``request``: the instance of the actual Request;

If you are interested by this component, you can take a look to the official [Expression Language](http://symfony.com/doc/current/components/expression_language/index.html) documentation.
