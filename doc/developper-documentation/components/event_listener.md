# Components

## The Event Listener component

*Events* are a common and effective way to tie together loosely coupled components in an application.
An event is generally used to broadcast a change that has occured during a process. If a `component` is
interested in a particular event, it can *listen* to it. When this particular event is triggered, a `method`,
often called *callback* or *handler* is executed. Events are convenient ways to share
data between components.
In `BackBee`, events are triggered by an *EventDispatcher* and an *EventListener* is used to listen to them.

## Event

`Namespace: BackBee\Event\Event`

The Backbee event class extend `sfEvent` and allows us to create a generic `Event` object.
A generic event object has two properties: `target` and `args`;
The target property represents the target of the event. The `args` is an optional associated map.
When the `args` property is provided, it's sent to the listeners.
Custom Event can be created by extending the class `BackBee\Event\Event`.

```php
<?php
$myHelloEvent = new Event($target, array("message"=>"Hello BackBee");

```

## Event Listener
`Namespace: BackBee\Event\EventListener`

To listen to an event you must use the `addListener` method of the `EventDispatcher`.
`addListener` takes three parameters. The first one is the name of the event that will be triggered, the second is an array whose first element
is a class path and the second is a method. The last parameter is the priority of the event. Listener with higher priority will be triggered first.

    $this->application->getEventDispatcher()->addListener("loading.event", array("BackBee/Event/Listener/HelloListener", "sayHello"));

Here, we register the listener *HelloListener* to be executed when the event `loading.event` is occured.

```php
    <?php

        namespace BackBee\Event\Listener;
        use BackBee\Event\Event;

        class HelloListener {

            public static sayHello (Event $event) {

                $dispatcher = $event->getDispatcher();

                if ($event->hasArguments("message")) {
                    $message = $event->get("message");
                    if ( null !== $dispatcher->getApplication()) {
                        $dispatcher->getApplication()->getLogging()->notice($message);
                    }
                }
            }
        }

    ?>
```

Notice that the event object has access to the `application` *via* the `EventDispacher` and that the `sayHello` is a *static* method.

## Event Dispatcher

`Namespace: BackBee\Event\EventListener`

Events are *dispatched* or *triggered* by the `EventDispatcher` by using the method `triggerEvent`
The sample bellow shows how to listen to an event.

To trigger an event, we must use the `EventDispatcher`. It provides, among others, a `dispatch` method that takes two arguments. A name and an event object.

```php
<?php
    $this->application->getEventDispatcher()->dispatch("loading.event", $myHelloEvent);
```

When the *loading.event* is dispatched by the `EventDispatcher`, all the registered listeners will be executed. In our example,
the static `sayHello` method of the `Backbee/Event/Listener/HelloListener` will be called.

## Events from Class Content Component

One of the main purpose of `Backbee` is to render contents. As we have seen, a
content is defined in a `.yml` file. Content creation and content rendering go
through many phases that `Backbee` exposes with events. By instance, an event is triggered
when a content is created, before and after a content has been saved, when the rendering process
is started, while the content is been rendered, after the renderer process and so on. These events
allow us the act in different ways on the contents lifecycle and rendering process itself.

ClassContent events are similar to custom events. But, as they take place when the main application
is dealing with a request, a common way to listen to them is to use an `event.yml` config file.
Will try to local event file in the `repository/Config` folder. Bellow is an example of how to
use the file.

```yaml
    article.article.render:
        listeners:
            - [BackBee\Event\Listener\ArticleListener, onRender]

    social.facebook.prerender:
        listeners:
            - [BackBee\Event\Listener\Socialistener, PrerenderFacebook]
```

`events.yml` is a declarative way to add listeners to `ClassContentEvents` event. Under the hook,
this yaml file is parsed and the `EventDispatcher` is used to register the listeners.
In our example,  `article.article.render` and `social.facebook.prerender` are events names.
By convention `BackBee` transforms ClassContent class path to event name. Let's take a look at our events files.

According to our `event.yml`, the `article.article.render` event will be triggered when
the content `ClassContent/Article/Article.yml` is about the be rendered whereas
the 'social.facebook.prerender' will be triggered *before* the content `ClassContent/Social/Facebook.yml`
was rendered.
`*.render`  and `*.prerender` are events triggered by the `Renderer` object. Bellow is the list of
all the Renderer events.

- `*.prerender` : is triggered *before* the content is being rendered;
- `*.render` : when the content is about to be rendered;
- `*.postrender` : after the render process.

However there is no strong difference between *render* and *postrender*.

In BackBee all the contents are `Doctrine` entities. These doctrine events are also available for all the contents.

- `preremove`
- `postremove`
- `postupdate`
- `preupdate`
- `update`
- `prepersist`
- `postpersist`
- `postload`
- `onflush`


## ClassContent Listener

To listen to a ClassContentEvent we have to create a `Listener` class. By default BackBee
will look for Listener in the repository/Config/Listener folder.

```php
    <?php
        namespace BackBee\Event\Listener,
        use BackBee\Event\Event;

        class ArticleListener extends Event {

            public static function onRender(Event $event){
                /* The renderer object */
                $renderer = $event->getEventArgs();

                /* The eventDispatcher */
                $eventDistacher = $event->getDispatcher();

                /* the BackBee application */
                $application = $eventDistacher->getApplication();

                /* The article classcontent */
                $content = $renderer->getObject();

                /* Add a new parameter that will be available in the content template */
                $renderer->assign('myParams', "my Param value");
            }
        }
    ?>
```

## Event inheritance
In `BackBee` content can be inherited from one another. The `EventDispatcher` respects this inheritance.
If an event is triggered for a subContent it will also be triggered for its parents. So *class content* Events bubbles up in BackBee.
