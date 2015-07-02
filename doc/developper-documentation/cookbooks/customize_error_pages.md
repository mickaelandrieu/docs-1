# CookBooks

## Customize error pages

In BackBee, thanks to Symfony Debug component all errors are treated as exceptions: pages not found or access unauthorized.
Each error is triggered by throwing an exception in your code.

In "Developper Mode" BackBee will catch and display a **better** exception page with a lot of informations
to help you understand the issue:

![The BackBee Developper exception displayer](http://i.imgur.com/GwvS1qE.png)

In production, your visitors will see the nice error page provided by BackBee:

![The BackBee Production exception displayer](http://i.imgur.com/tml7ozz.png)

Error pages can be customized in two different ways depending on your needs:

* If you just want to design the contents and styles of the error pages, you can set your own error templates
* If you need total control of exception handling to execute your own logic, create your own ExceptionListener on ``kernel.exception`` event.

### Use your own default templates

When the error page loads, an internal ExceptionListener is used to render a Twig template to show template to the user.

This ExceptionListener uses the HTTP status code, the ``debug`` parameter and the following logic to determine the template filename:

BackBee provides core templates which are located into ``vendor/backbee/BackBee/Resources/layouts/error``.
If the template for the code status doesn't exist, the default template which be used instead.

In BackBee Standard edition, theses templates are already overriden in the **ToolbarBundle**. Theses templates are located
into ``vendor/backbee/toolbar-bundle/Resources/layouts/error`` folder.

If you want to override theses templates, your application might look like this:

```
repository/
    Resources/
        layouts/
            error/
                404.phtml
                500.twig
                default.phtml
```

In case you need them, the ExceptionController passes informations to the error template via the error variable.
It acts as the original PHP exception so you can access the HTTP status code and exception message.

