# CookBooks

## Ease debugging with Profiler

In BackBee, in addition to the Symfony Debug which display a **better** exception page with more informations, we have
access to a Profiler which collect a lot of interesting data to help developper understand what is going on.

This article cookbook covers the documentation of actual panels from BackBee Profiler.

The BackBee Profiler is a toolbar available at the bottom of the page when you are in "development" mode.

![The BackBee Developper debug toolbar](http://i.imgur.com/2ukjUwd.png)

From left to right, you access to useful informations:
* the ``BackBee CMS`` version and the ``BackBee CMS`` application name;
* the code status of the page and the action used to render the page;
* the number of logs generated when render this page;
* the memory used to render the page (in Mo);
* the number of database requests and the time spent to execute them.


## The **Configuration** panel

When you click on ``BackBee CMS`` version or application name, you access the **Configuration panel**.

In this panel, informations from BackBee configuration are displayed:

### Project configuration

In this section, the version of BackBee CMS used, the application name and the environment are displayed.

### PHP configuration

In this section, the version of PHP used is displayed and informations about cache extensions.
Also, click on "phpinfo" will give you full informations about your PHP configuration.

### Actives Bundle

In this section are listed all activated bundles (i.e exactly the same results as bundles registered in ``bundles.yml`` file).

## The **Request** panel

When you click on status code or in the action used to render page, you open the **Request panel**.

In this panel, all informations from server and from Symfony Request object are displayed to help you understand the
informations used to render your page.

All the informations from PHP super globals related to the Request are displayed.

* *GET* and *POST* parameters;
* Request attributes from BackBee:
    * ``_action``: the action used to render the page;
    * ``_controller``: the controller called to render the page;
    * ``_route``: the route matched by the uri;
    * ``uri``: if not controller was found, return "_root_" which means the BackBee ``FrontController`` is used.
* Request cookies;
* Request headers (also availables in debug toolbar from any web browser)
* Request content
* Request Server parameters:
* Response headers;

But also, the ``Session`` informations and the [``flashes``](http://symfony.com/doc/current/components/http_foundation/sessions.html#flash-messages) messages


## The **Routing** panel

When you click on "Routing", you open the **Request panel**.

In addition to the ``debug:routing`` command available in ``DebugBundle``, the Routing panel provides a global overview of actual routes
registered in the application.

Routes are ordered by name, schema and patterns. For each route, the panel list the Controller/action couple called when the route is accessed.

> This is a good way to discover the REST API.

## The **Database** panel

When you click on the database icon or in the database informations, you open the **Database panel**.

### Queries section

This panel list all Doctrine requests used to render the page with the spending time and the runnable query.
This is useful when you want to optimize SQL queries or want to understand why you have a Doctrine error with a ``QueryBuilder``.

### Doctrine ORM mapping section

If you create your own entities and want to use Doctrine 2 to save in database, you need set up theses PHP classes.
This section display all actual classes managed by Doctrine 2 and if the mapping is correctly set up,
else an explicit message is displayed to help you fix the mapping.
