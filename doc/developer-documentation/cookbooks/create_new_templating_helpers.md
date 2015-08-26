# CookBooks

## Create new templating Helpers

This cookbook article covers the creation of Renderer helpers using BackBee Standard Edition.

Templating helpers are PHP classes that extend the ``final`` class ``BackBee\Renderer\AbstractRenderer\AbstractHelper``,
and need to be located in ``repository/Templates/helpers`` folder in your BackBee application (or in ``Templates/helpers`` folder in bundles).

As an example, we will create an helper to provide a new ``bb_dump()`` function in templates.

> You need to create class with the same name as the function you want to create, because ``__invoke`` magic function is called.

```php
<?php
namespace BackBee\Renderer\Helper;

/**
 * @author Mickaël Andrieu <mickael.andrieu@lp-digital.fr>
 */
class bb_dump extends AbstractHelper
{
    /**
     * Returns dump from template (to ease debug of Class Content rendering)
     * If Symfony VarDumper is autoaded, use it instead of Doctrine Debug::dump()
     *
     * @param  mixin $mixin the variable to dump
     *
     * @return string
     */
    public function __invoke($mixin)
    {
        if (!class_exists('\Symfony\Component\VarDumper\VarDumper')) {
            return '<pre>' . \Doctrine\Common\Util\Debug::dump($mixin, 2, true, false) . '</pre>';
        }else {
            return dump($mixin);
        }
    }
}
```

The helper is done and can used directly in all templates.

```twig
<!-- /repository/Templates/scripts/Home/HomeContainer -->
<div {{ this.bbcontent(null, {'class': 'container main_containt'})|raw }}>

    {% if this.getObject().getParamValue('container_title') != '' %}
        <div class="bloc-title-2 triangle-b">
          <div class="border-bx2 border-color color-txt-1">{{ this.getObject().getParamValue('container_title') }}</div>
        </div>
    {% endif %}

    {{ this.render(container)|raw }}

    {{ this.bb_dump(this.getObject())|raw }}
</div>
```

or, in phtml:

```php
<!-- in a pHTML template -->
<p>
<?php $this->bb_dump($this->getObject()); ?>
</p>
```
Will produce, with the ``[Symfony VarDumper](http://symfony.com/doc/current/components/var_dumper/introduction.html)`` added as dependency of project,
this output:

![The new dump() helper](http://i.imgur.com/GjG4uwp.png)
