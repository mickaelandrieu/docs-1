# Components

## The ClassContent Component

### What is a ClassContent?

ClassContents are at the heart of BackBee, every piece of content you will find in BackBee is a ClassContent. It defines your site and how you contribute to it.
ClassContents can be extended, listened, versioned and they all have in common that they implement `BackBee\Renderer\RenderableInterface`.
In general a ClassContent is defined in YAML, it can be implemented in PHP too but the YAML notation because it's easier, quicker and scalable compared to PHP.

### How it works

The definition of a ClassContent follows [PHPCR recommendations](http://phpcr.github.io) and every flexible content is a combination of primary content types.

PHPCR definition:
> The PHP Content Repository is an adaption of the Java Content Repository (JCR) standard, an open API specification defined in JSR-283.
> The API defines how to handle hierarchical semi-structured data in a consistent way.

> The typical use case is content management systems. PHPCR combines the best out of document-oriented databases (weak structured data) and of XML databases (hierarchical trees). On top of that, it adds useful features like searching, versioning, access control and locking on top of it.

ClassContent is composed of it's definition class and it's templates. The storage of ClassContent is automated and is done int the content table, BackBee uses the Doctrine discriminator system to store contributed entities.

A ClassContent definition is generally declared in a YAML file and is composed of three major parts `properties`, `elements` and `parameters`

### Naming Convention

ClassContent is declared in a `ClassContent` folder and its final namespace depends on the folder where you declare a new entity. All ClassContent have common root namespaces which is `BackBee\ClassContent`. If you create a definition file in a folder named `Article` and this file is named `Paragraph.yml` the complete namespace for this content is`BackBee\ClassContent\Article\Paragraph`.

Respect the PSR naming convention. PSR specification is:
> Namespaces and classes MUST follow an "autoloading" PSR: [[PSR-0](http://www.php-fig.org/psr/psr-0/), [PSR-4](http://www.php-fig.org/psr/psr-4/)].
> This means each class is in a file by itself, and is in a namespace of at least one level: a top-level vendor name.
> Class names MUST be declared in StudlyCaps.

Even if ClassContent are in most cases YML files, they are designed to become PHP Class, then you must declare names in StudlyCaps.

### Primary ClassContent types

The primary ClassContents types are the most basic types in BackBee. They are the base of every other Content.

BackBee provides 5 primary ClassContent types:

- ContentSet: a container of content, they are iterable and countable.
- Element\Text: a simple text input
- Element\File : an attached file
- Element\Link : a URL link
- Element\Keyword : a keyword association

### Native ClassContent types

In addition to the primary types BackBee provides four Natives ClassContents and theses contents are extensions of primary contents:

- Element\Attachment , an attachment content, extending Element\File
- Element\Date, a date input, extending Element\Text
- Element\Image , an image content, extending Element\File
- Element\Select , a select input, extending Element\Text


### Create a ClassContent definition

In most cases extending native and primary ClassContent is unnecessary. You simply create a new ClassContent type that uses some primary and native types.

Example for an article :


Our article will have one title, one picture and one body. You translate this textual definition in ClassContent as followed:

> Article.yml
```yml
Article:
  properties:
    name: Article
    description: "An is compose of one title, one picture and one body"
    category: [article]
  elements:
    title:
      type: BackBee\ClassContent\Element\Text
      label: Title
      default:
        value: Your title here...
      maxentry: 1
    body:
      type: BackBee\ClassContent\Element\Text
      label: Body
      default:
        value: Your abstract here...
      maxentry: 1
    picture:
      type: BackBee\ClassContent\Element\Image
```

#### Focus on properties section:

```yml
name: Article
```
The property `name` is for humans and can be composed of multiple words like "Basic Article"


```yml
description: "An is compose of one title, one picture and one body"
```
The property `description` goes against human reading and provides a description of what to do with your ClassContent and/or what it  is compose of.

```yml
category: [article]
```
The property `category` helps you to categorize your ClassContent and if content isn't categorized it can't be edited online, you musn't forget to assign a category to each content. In addition a ClassContent can be categorized and visually hidden in the toolbar if you use this notation:

```yml
category: [!article]
```

#### Focus on elements section:

```yml
title:
  type: BackBee\ClassContent\Element\Text
  label: Title
  default:
    value: Your title here...
  maxentry: 1
```

```yml
title:
```
This will be the identifier property and the way you access code like this  `{{ this.render(title)|raw }}` in template file

```yml
type: BackBee\ClassContent\Element\Text
```
Type is the definition of your content. Here the title is defined by the primary ClassContent type `Element\Text`

```yml
label: Title
```
The property label is for humans and can be composed of multiple words like "Basic Article" it appears under a contribution form.

```yml
default:
  value: "Your title here..."
```
The default section allows you to define how just initialized ClassContent appears. Here the default value of the title is "Your title here..."

```yml
maxentry: 1
```
This property determine how many entries of this property the Class Content can accept.

### ClassContent templates

When BackBee tries to render your class content, it searches a template corresponding to it's name. In the previous case the searched template was `Article.twig` or `Article.phtml`

If BackBee can't find the template it will retrieve the template of each element and render the elements separately. If you provide only a definition, BackBee is able to render it.

> Article.twig
```twig
<article {{ this.bbcontent(null, null, {class: 'article box-wrapper'})|raw }} itemscope itemtype="http://schema.org/Article">
  <h1 {{ this.bbcontent(title)|raw }} itemprop="headline">
    {{ title.value|raw }}
  </h1>
  <div itemtype="http://schema.org/ImageObject" itemprop="primaryImageOfPage">
    {{ this.render(image, null, {'class': 'figure block-fullwidth'})|raw }}
  </div>
  {{ this.render(body)|raw }}
</article>
```

#### template and render mode

The render mode has the same function as the template but it can be considered as an alternative template. In general, render mode is automatically called by a block extending `ContentSet`, by default when you render it use the name of this block to require an alternative rendering. But you can also define the rendering using `parameters` or in the template directly when you use the function render `this.render(Article, 'renderMode')`.

In the next example we will put our article into an ArticleList and then we alternate the rendering of the article creating a file named `Article.ArticleList.twig` and this template will be automatically used by BackBee.

> Article.ArticleList.twig
```twig
<article {{ this.bbcontent(null, null, {class: 'article box-wrapper'})|raw }} itemscope itemtype="http://schema.org/Article">
  <h2 {{ this.bbcontent(title)|raw }} itemprop="headline">
    {{ title.value|raw }}
  </h2>
  <div itemtype="http://schema.org/ImageObject" itemprop="primaryImageOfPage">
    {{ this.render(image, null, {'class': 'figure block-fullwidth'})|raw }}
  </div>
</article>
```

BackBee can't find the render mode `Article.ArticleList.twig` it will try to render `Article.twig` and it if doesn't find this template it will render each element with its default rendering mode.


### ContentSet

ContentSets are particular ClassContents instead of a classic ClassContent, a ContentSet doesn't have elements section and represents only a collection of other ClassContent. But you can customize a ClassContents trough its `parameters` section.

```yml
RelatedContainer:
  extends: \BackBee\ClassContent\ContentSet
  properties:
    name: Linked article container
    description: "A block that display linked article"
    category: [Article]
  parameters:
    rendermode: related
    accept: [BackBee\ClassContent\Article\Article]
```

> As you can see a ContentSet has extended `\BackBee\ClassContent\ContentSet`


#### Focus on parameters section:

```yml
rendermode: related
```
Render mode allows you to override the default behavior of BackBee by redefining the render mode named on sub content. We have seen that by default BackBee tries to find a render mode by using the name of the ContentSet then by default `Renderer` search a file named `Article.RelatedContainer.twig`. By using the `rendermode` setting you will find `Article.related.twig` file.

```yml
accept: [BackBee\ClassContent\Article\Article]
```
The accept parameter prefilters the type of ClassContent you can add into your ContentSet.

#### ContentSet templates

ContentSet template is rendered exactly at same way as other ClassContent. The only difference is that you have to use a loop to render the collection.

> RelatedContainer.twig
>
```twig
<div {{ this.bbcontent(null, {'class': 'col-md-4'})|raw }}>
    {% for article in this.getObject().getData() %}
        {{ this.render(article)|raw }}
    {% endfor %}
</div>
```

To access the ContentSet collection you have to use this way `this.getObject().getData()`

### The "Autoblock"

An Autoblock isn't a primitive or native ClassContent and is a mix between these two types.
An Autoblock is a fully automated Content, its collection isn't contributable and automatically creates a list of ClassContent. This list is generated by the configuration of the Autoblock.

By default BackBee doesn't provide "Autoblock" but there is one provided in the standard edition.

An Autoblock has to be highly configurable and all the configuration is created in the `parameters` section. An Autoblock has to implement a listener to automatically generate its collection. You can find more information in the Parameters section.

### Override, Extends, listen and add Trait to your ClassContent

#### Overriding a ClassContent

As with the Configuration system, you can override a ClassContent definition.

The priority system tries to find the definition and template in this order :
- `repository/{context}/ClassContent` into your current context
- `repository/ClassContent` into your default context
- `{bundle}/ClassContent` into your bundles ClassContent folder
- `ClassContent` into ClassContent BackBee component

If you create a file with the same name as an existing content but a different folder has the priority, this content will be modified and take the value in the created file. This work also for the template, but have to be replaced `ClassContent` folder with `Templates/scripts`

#### Extends a ClassContent

To extend an other ClassContent, you just have to use `extends` keyword like in the example of the ContentSet.

There are many advantages to extending an other class content. First you do not have to rewrite properties and elements.

The second advantage concerns rendering and listeners, BackBee will run through the parent classes to find templates and listener.

#### Listen a ClassContent

Add an event listener to a class content is pretty easy and you will find more information in the [Event Listener component](/developper-documentation/components/event_listener/) section.

To summarize each ClassContent has 5 attached events:
- prerender
- onrender
- postrender
- preflush
- onflush

#### Add a Trait  to a ClassContent

Trait lets go add behaviors to a ClassContent. By default BackBee doesn't provide trait, but like Autoblocks, some traits are available in the standard edition. If you don't know what is a trait, check the official [PHP documentation](http://php.net/manual/en/language.oop5.traits.php).


### ClassContent Parameters

Parameters allow you to configure contents. ClassContent parameters are usually used in the listener but can be used everywhere. ClassContent parameters are contributed in back office by the user with the gearing button of Block.

Parameters are built from the block's YAML like

```yaml
BlockDemo:
    properties:
        name: Block demo
        description: "Block for demonstration purposes"
        category: [Demo]
    elements:
        text:
            type: BackBee\ClassContent\Element\Text
    parameters:
        mytext:
            label: 'My text'
            type: 'text'
            value: ''
        myselect:
            label: 'My select'
            type: 'select'
            options:
                'foo': 'Foo'
                'bar': 'Bar'
            value: ['foo']

```

#### Use parameters

If ClassContent Parameters are not validated by user the content parameters are overridden by revision parameters.
**Only the value of the parameter is saved.**

To get default parameters:
```php
<?php
$params = $content->getDefaultParams();

/** That returns all parameters with definitions and default values:
 *  array (size=2)
 *    'mytext' =>
 *      array (size=3)
 *        'label' => string 'My text' (length=7)
 *        'type' => string 'text' (length=4)
 *        'value' => string '' (length=0)
 *    'myselect' =>
 *      array (size=4)
 *        'label' => string 'My select' (length=9)
 *        'type' => string 'select' (length=6)
 *        'options' =>
 *          array (size=2)
 *            'foo' => string 'Foo' (length=3)
 *            'bar' => string 'Bar' (length=3)string 'yolo' (length=4)
 *        'value' =>
 *          array (size=1)
 *            0 => string 'foo' (length=3)
 */
```

To get all parameters:
```php
<?php

$params = $content->getAllParams();

/** That returns all parameters with definitions and new values
 *  array (size=2)
 *    'mytext' =>
 *      array (size=3)
 *        'label' => string 'My text' (length=7)
 *        'type' => string 'text' (length=4)
 *        'value' => string '' (length=0)
 *    'myselect' =>
 *      array (size=4)
 *        'label' => string 'My select' (length=9)
 *        'type' => string 'select' (length=6)
 *        'options' =>
 *          array (size=2)
 *            'foo' => string 'Foo' (length=3)
 *            'bar' => string 'Bar' (length=3)
 *        'value' =>
 *          array (size=1)
 *            0 => string 'foo' (length=3)
 */
```

To get one parameter:

```php
<?php
$param = $content->getParam('mytext');

/** That returns the definition and new value
 *  array (size=3)
 *    'label' => string 'My text' (length=7)
 *    'type' => string 'text' (length=4)
 *    'value' => string '' (length=0)
 */
```

To get the value of the parameter:
```php
<?php
$paramValue = $content->getParamValue('mytext');

// That returns the new value string '' (length=0)
```

To set a value, you need to set up the same type you declare in the YAML file as the value:
```php
<?php
$content->setParam('mytext', 'foo');
$param = $content->getParamValue('mytext')

// That returns a new value set  string 'foo' (length=3)
```

### Parameters reference

* checkbox
* datetimepicker
* hidden
* linkSelector
* mediaSelector
* nodeSelector
* password
* radio
* select
* text
* textarea

All parameters have default options:

#### Default options

 |   | Type | Description | Default | Mandatory
--- | --- | --- | --- | ---
**label** |  String | Label is displayed above the field | Empty | No
**value** | Mixed | This field must be set and allows to describe the  parameter's type | None | Yes |
**type** | String | Key of one of available parameters | None | Yes |

***

#### Checkbox

Like HTML checkbox attribute

 |   | Type | Description | Default | Mandatory
--- | --- | --- | --- | ---
**options** |  Array | List of options (key/value) | Empty array | Yes
**inline** | Boolean | Display checkbox inline | false | No |
**value** | Array | List of selected checkboxes | Empty array | Yes |

**Example**:

```yaml
parameters:
    mycheckbox:
        type: 'checkbox'
        options:
            'foo': 'Foo'
            'bar': 'Bar'
        value: ['foo']
        inline: true
```

```php
<?php
$param = $content->getParamValue('mycheckbox'); // sarray('foo')
```
***

#### Datetime picker

Text element with a datetimepicker

 |   | Type | Description | Default | Mandatory
--- | --- | --- | --- | ---
**value** | Number | It is an timestamp value | Empty | Yes |

**Example**:

```yaml
parameters:
    mydatetimepicker:
        type: 'datetimepicker'
        value: 1435573740
```

```php
<?php
$param = $content->getParamValue('mydatetimepicker'); // 1435573740
```

***

#### Hidden

Like HTML hidden attribute

 |   | Type | Description | Default | Mandatory
--- | --- | --- | --- | ---
**value** | String | The default text  | Empty | Yes |

**Example**:

```yaml
parameters:
    myhidden:
        type: 'hidden'
        value: 'foo'
```

```php
<?php
$param = $content->getParamValue('myhidden'); // foo
```

***

#### Link selector

Link selector allows you to choose a link from one of your website pages or an external link.

 |   | Type | Description | Default | Mandatory
--- | --- | --- | --- | ---
**value** | Json | Object which contains url / title / pageUid / target  | Empty array | Yes |

**Example**:

```yaml
parameters:
    mylinkselector:
        type: 'linkSelector'
        value: []
```

```php
<?php
# Example with select one link
$param = $content->getParamValue('mylinkselector');
// array('url' => '/foo', 'title' => 'Foo', 'pageUid' => 'anPageUid', 'target' => '_self')
```
** Page tree **:
We recommend to retrieve the url from the page entity instead of the url attribute because the url can change.

** External link ** :
In this case, note the pageUid attribute is (obviously) null.

***

#### Media selector

Media selector allows you to choose a list of media in the media library.

 |   | Type | Description | Default | Mandatory
--- | --- | --- | --- | ---
**value** | Json | Object which contains folder_uid / image / media_id / title /type / uid  | Empty array | Yes |

**Example**:

```yaml
parameters:
    mymediaselector:
        type: 'mediaSelector'
        value: []
```

```php
<?php
# Example with select one media
$param = $content->getParamValue('mymediaselector');
// array(array('folder_uid' => 'anFolderUid', 'image' => 'imageUrl', 'media_id' => '1', 'title' => '_Foo', 'type' => 'Media/Image', 'uid' => 'anUid'))
```

***

#### Node selector

Node selector allows you to choose a page from tree

 |   | Type | Description | Default | Mandatory
--- | --- | --- | --- | ---
**value** | Json | Object which contains url  pageUid / title  | Empty array | Yes |

**Example**:

```yaml
parameters:
    mynodeselector:
        type: 'nodeSelector'
        value: []
```

```php
# Example of node selection
$param = $content->getParamValue('mynodeselector');
# array('pageUid' => 'anPageUid', 'title' => 'Foo')
```
We recommend retrieving the title from the page entity instead of the attributed title because the title can change.

***

#### Password

Like HTML password attribute

 |   | Type | Description | Default | Mandatory
--- | --- | --- | --- | ---
**value** | String | The default text | Empty | Yes |

**Example**:

```yaml
parameters:
    mypassword:
        type: 'password'
        value: 'foo'
```

```php
<?php
$param = $content->getParamValue('mypassword'); // foo
```
***

#### Radio

Like HTML radio attribute

 |   | Type | Description | Default | Mandatory
--- | --- | --- | --- | ---
**options** |  Array | List of options (key/value) | Empty array | Yes
**inline** | Boolean | Display radio inline | false | No |
**value** | Array | List of selected radio | Empty array | Yes |

**Example**:

```yaml
parameters:
    myradio:
        type: 'radio'
        options:
            'foo': 'Foo'
            'bar': 'Bar'
        value: ['foo']
        inline: true
```

```php
<?php
$param = $content->getParamValue('myradio'); // array('foo')
```
***

#### Select

Like HTML select attribute

 |   | Type | Description | Default | Mandatory
--- | --- | --- | --- | ---
**options** |  Array | List of options (key/value) | Empty array | Yes
**value** | Array | List of selected radio | Empty array | Yes |
**multiple** | Boolean | Allows multiple selection | false | No

**Example**:

```yaml
parameters:
    mypassword:
        type: 'select'
        options:
            'foo': 'Foo'
            'bar': 'Bar'
        value: ['foo']
        multiple: true
```

```php
<?php
$param = $content->getParamValue('myradio'); //array('foo')
```

***

#### Text

Like HTML text attribute

 |   | Type | Description | Default | Mandatory
--- | --- | --- | --- | ---
**value** | String | The default text | Empty | Yes |

**Example**:

```yaml
parameters:
    mytext:
        type: 'text'
        value: 'foo'
```

```php
<?php
$param = $content->getParamValue('mypassword'); // foo
```
***

#### Textarea

Like HTML textearea attribute

 |   | Type | Description | Default | Mandatory
--- | --- | --- | --- | ---
**value** | String | The default text | Empty | Yes |
**rows** | String | the rows html attribute | 5 | No

**Example**:

```yaml
parameters:
    mytextearea:
        type: 'textarea'
        value: 'BackBee'
        rows: 10
```

```php
<?php
$param = $content->getParamValue('mytextearea'); // BackBee
```
***

#### Special parameters

##### Rendermode

Rendermode parameters automatically list rendermodes of content and use them directly.

 |   | Type | Description | Default | Mandatory
--- | --- | --- | --- | ---
**value** | Array | Selected rendermode | Empty | Yes |

```yaml
parameters:
    rendermode:
        type: 'select'
        value: []
```

**The key must be 'rendermode'**

