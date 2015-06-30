# Components

## The Translation component

This component is used in BackBee to translate text into differents languages. Note this component is an extension of [Symfony Translation Component](http://symfony.com/doc/current/components/translation/index.html)


### How the translation work

BackBee translation component override `Symfony\Component\Translation\Translator` to lazy load every catalogs from:
- BackBee\Resources\translations
- PATH_TO_REPOSITORY\Resources\translations
- PATH_TO_CONTEXT_REPOSITORY\Resources\translations.

 BackBee use `XLIFF` catalogs to translate statics contents into your templates.

### Configuration

BackBee become with own is catalog and it can be extended without configuration if you put your catalog into the folder `repository/translations`, the file need some naming convention `messages.{locale}.xlf` you need to adapt `{locale}` with one real locale like en, fr, etc.

You configure the default locale of the site in `encoding.yml` into `repository/Config` folder. By default the value is `~` and is corresponding to `en_US` locale.

### The Catalog

First step will go to create our first catalog and need to follow the Oasis XLIFF specifications.

```xliff
<?xml version="1.0"?>
<xliff version="1.2" xmlns="urn:oasis:names:tc:xliff:document:1.2">
    <file source-language="en" datatype="plaintext" original="file.ext">
        <body>
            <trans-unit id="1">
                <source>hello.backbee</source>
                <target>Hello BackBee !</target>
            </trans-unit>
        </body>
    </file>
</xliff>
```

> In this  catalog case we define that the string `hello.backbee` will be translate into `Hello BackBee !`

### Use translation

In template you can access to the translator by the global `bb` and you can use is function `trans` to translate your string.

```twig
{{ bb.translator.trans('hello.backbee') }}
```

This will return "Hello BackBee !".

For using it out of the template you need to access to the Dependencies Invection Container and then you can use the `trans` method.

```php
$translator = $this->getApplication()->getContainer()->get('translator');

$translator->trans('hello.backbee');
```

> the getApplication method is depending in the context where you run your translation.

> If you are in an event listener you need to  do `$this->getEventDispatcher()->getApplication()->...`

Like before the result will be "Hello BackBee !".

If you want do some more advanced usage of the component translation you will found more information on [Symfony Translation Component Usage Documentation](http://symfony.com/doc/current/components/translation/usage.html)