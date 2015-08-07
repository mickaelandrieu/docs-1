# Components

## The Bundle Component

## What is a bundle?
In BackBee, a bundle is like an BackBee's core application (``BackBee\BBApplication``). It can bring its ``class contents``, ``configurations``, ``listeners``, ``events``, ``templates``, ``controllers``, etc. It's surely even more flexible cause you can use bundle loader recipes to change the way things should be loaded. We will talk about this point later.
While the core application contains many features to build up your website/application, a bundle __must__ contain only one main functionnality. For example, a ArticleDisqusBundle that brings article content and Disqus commenting feature __must not__ exist. A good solution is to split it into two distincts bundles:

- ArticleBundle: could be a bundle that brings article classcontent and its views, maybe also some listeners to alter some behaviors.
- DisqusBundle: could simply be an implementation of Disqus API into BackBee.

Also, if one day you need the Disqus feature in another project that doesn't need the article class content, this will result on embedding unused code. So keep it in mind: __one bundle = one feature__.

## Bundle folder structure and entry point
A valid bundle should at least have a folder structure that contains an entry point and the ``Config`` folder. Considering for this example that our bundle is named NewsletterBundle.
```
NEWSLETTER_BUNDLE_ROOT_FOLDER
    |_ Config
        |_ config.yml
    |_ Newsletter.php
```
The entry point is essential for BackBee to know how load your bundle. Note that the classname does not matter at all (no any standard to follow) but it must implement ``BackBee\Bundle\BundleInterface``. We highly encourage you to extends ``BackBee\Bundle\AbstractBundle`` so you have only ``::start()`` and ``::stop()`` methods to implement to be ready to go. You will find below a simple implementation of these two methods and we will explain to you how BackBee works and loads your bundles:

```php
namespace BackBee\Bundle\NewsletterBundle;

use BackBee\Bundle\AbstractBundle;

class Newsletter extends AbstractBundle
{
    /**
     * {@inheritdoc}
     */
    public function start()
    {
        return $this;
    }

    /**
     * {@inheritdoc}
     */
    public function stop()
    {
        return $this;
    }
}
```

Explanation: at the first time the bundle is accessed from application service container, its  ``::start()`` method will be invoked (so only once). And ``::stop()`` will be invoked on every started bundle when the event ``bbapplication.stop``is dispatched.

The ``Config/config.yml`` is also required. It will be parsed to get an instance of ``BackBee\Config\Config``. Here bellow a list of bundle basic configuration parameters:

```yaml
# NEWSLETTER_BUNDLE_ROOT_FOLDER/Config/config.yml
bundle:
    name: NewsLetterBundle
    description: This is a plugin that provide newsletters integration in BackBee
    enable: true # define if the bundle should be load; default: true
    config_per_site: false # allows you to have different configuration per site; default: false
    category: [] # your bundle can belong to none, one or many categories; default: none
    thumbnail: ~ # the thumbnail of your bundle; default: null
```

So we saw what's a bundle and what is needed to create one. Let's learn how register it to BackBee. You need to create ``bundles.yml`` into your project config folder (generally located at ``PROJECT_ROOT_DIR/repository/Config/``):

```yaml
# PROJECT_ROOT_DIR/repository/Config/bundles.yml
nl: BackBee\Bundle\NewsletterBundle\Newsletter
demo: BackBee\Bundle\DemoBundle\Demo
```
That's all! Pretty simple, right? One more thing, bundles are automatically loaded into application service container. Your bundle identifier will always follow this pattern: ``bundle.__BUNDLE_NAME__``. Note that the ``__BUNDLE_NAME__`` is the declared key of your bundle, so it's ``bundle.nl`` in this case (``nl`` cause it's the key name of the bundle entry point in ``bundles.yml``). You can also access to NewsletterBundle's config from the service container. Its service identifier follow this pattern: ``bundle.__BUNDLE_NAME__.config``. So the NewsletterBundle config identifier is ``bundle.nl.config``.

Well, now you know the basics about BackBee's bundle. Let's get into the next level and learn more about how BackBee loads your bundles!

## How the bundle loader works?
During the execution of ``BackBee\BBApplication::__construct`` many things are done. Within it we have the call to the bundle loader. First, BackBee loads its dependencies like its listeners, services, parameters (into dependency injection container), classcontents, etc. Then BackBee says _"Well bundle loader, it's time for you to do your job!"_.
The first task the bundle loader do is to get every declared bundle containing by ``PROJECT_ROOT_DIR/repository/Config/bundles.yml`` and load them one by one. Note that the order bundles are loaded is determined by declaration order in ``bundles.yml`` (in previous example, NewsletterBundle will be loaded first). The order is important cause the last loaded one will always win if there is a conflict about which template to use or which parameter value to use in service container. So __be careful about your bundle declaration order__.

From now, consider that the current path (``./``) is ``__BUNDLE_BASE_DIR__``. So ``./Config/`` is equal to ``__BUNDLE_BASE_DIR__/Config/``.

#### Default expectations
We are now coming up to talk about bundle default structure expected by the loader:

- services for dependency injection container can be declared in yaml or xml format and is expected to be find into ``./Config/services.[yml|xml]``
- events listeners are expected to be declared in ``./Config/events.yml``
- routes are expected to be declared in ``./Config/route.yml``
- classcontent folder is expected to be ``./ClassContent/``
- templates are expected to be in ``./Templates/scripts/``
- helpers are expected to be in ``./Templates/helpers/``
- resources are expected to be in ``./Resources/``

As long as you follow these standard you won't have anything to do to load your services, events, routes, classcontent, templates, helpers and resources.

Default bundle folder structure:
```
BUNDLE_ROOT_FOLDER
    |_ ClassContent/
        |_ FakeContent.yml
    |_ Config/
        |_ config.yml
        |_ events.yml
        |_ route.yml
        |_ services.yml
    |_ Resources/
        |_ css/
            |_ fake.css
    |_ Templates/
        |_ helpers/
            |_ fakeHelper.php
        |_ scripts/
            |_ FakeContent.html.twig
    |_ EntryPoint.php

```

#### Using recipes to custom some load
Admitting that we want to put our templates into ``./Resources/views/`` instead of ``./Templates/scripts/`` and our helpers into ``./Renderer/Helper/`` instead of ``./Templates/helpers/``, how can we do that?
It's pretty simple if you use bundle loader recipes. They allow you use your custom recipe instead of the default one to load some part of your bundle. To load templates and helpers by ourself, simply add these line into ``./Config/config.yml``:
```yaml
# ./Config/config.yml
bundle:
    enable: true
    config_per_site: false
    category: []
    thumbnail: ~
    bundle_loader_recipes:
        helper: [BackBee\Bundle\FakeBundle\EntryPoint, loadHelpers]
        template: [BackBee\Bundle\FakeBundle\EntryPoint, loadTemplates]
```
Below an example of how to implements these callbacks into ``BackBee\Bundle\FakeBundle\EntryPoint``:
```php
namespace BackBee\Bundle\FakeBundle;

use BackBee\BBApplication;
use BackBee\Bundle\AbstractBundle;
use BackBee\Config\Config;

class EntryPoint extends AbstractBundle
{
    public static function loadHelpers(BBApplication $app, Config $config)
    {
        $helperDir = __DIR__.DIRECTORY_SEPARATOR.'Renderer'.DIRECTORY_SEPARATOR.'Helper';
        if (!is_dir($helperDir)) {
            $app->error(sprintf('Unable to load helpers directory (:%s).', $helperDir));
        } else {
            $app->getRenderer()->addHelperDir($helperDir);
        }
    }

    public static function loadTemplates(BBApplication $app, Config $config)
    {
        $viewsDir = __DIR__.DIRECTORY_SEPARATOR.'Resources'.DIRECTORY_SEPARATOR.'views';
        if (!is_dir($viewsDir)) {
            $app->error(sprintf('Unable to load views directory (:%s).', $viewsDir));
        } else {
            $app->getRenderer()->addScriptDir($viewsDir);
        }
    }

    /**
     * {@inheritdoc}
     */
    public function start()
    {
        return $this;
    }

    /**
     * {@inheritdoc}
     */
    public function stop()
    {
        return $this;
    }
}
```
We just saw how to custom the load of helpers and views. Below the complete list of recipe name bundle loader can interpret:

- ``classcontent``
- ``custom``: use this one if you want to run custom loader without altering default behavior
- ``event``
- ``helper``
- ``namespace``
- ``resource``
- ``route``
- ``service``
- ``template``
