# CookBooks

## Layout creation using LayoutBuilderBundle

### What is a Layout

A layout is a global template used to decorate page and content. A layout is composed of column, this columns are `ContentSet`, And layout determine positionning style of this column inner the page.

### Requirement

To create layout, we highly recommend you to use LayoutBuilderBundle. To do so, add this to the composer.json of your project:

```json
"require": {
    "php": ">=5.4.0",
    ...
    "backbee/layout-builder-bundle": "1.0.*@dev"
}
```

As last step you have to update your project dependencies by using composer:

```bash
composer update
```

### Create a Layout definition.

LayoutBuilderBundle creates layouts based on YAML definitions. You have to put your definitions into the folder `__PROJECT_ROOT_DIR__/repository/Layouts/definitions`:

```bash
cd mkdir repository/Layouts/Definitions
```

And we can create your first layout definition, It will be composed by 2 columns, MainColumn and SidePane:

> repository/Layouts/Definitions/LandingPage.yml

```yml
template: ~
label: Landing Page Layout
columns:
  MainColumn:
    mainZone: true
    accept: ~
    maxentry: ~
    defaultClassContent: ~
    inherited: false
  SidePane:
    mainZone: false
    accept: ~
    maxentry: ~
    defaultClassContent: ~
    inherited: true
```

#### Template setting:

The template setting define what twig or phtml file you use to render the layout. By default, BackBee is able to automatically compute your layout template name by using the definition filename. In this case, `LandingPage.twig` or `LandingPage.phtml`. If you want to use a custom name you can specify it by using the key ``template`` in your definition (default to ``null`` which is equal to ``~``). If you give a template name, you have to specify the extension too, Example:

```yml
template: landing_page.twig
```

#### Label setting:

This option allow you to change the label of your layout. It will be used to display the layout name in front.

#### Column setting:

This option define each column that the layout implement and there few options for each column.

##### Column -> mainZone setting

This is to highlight your main column into your layout. MainColumn option permit to BackBee that create a direct link behind the page (and the URL) and the content contained into the main column. Each layout must have one unique main column.

##### Column -> accept setting

This option permit to filter what type you can put into your column. In the case of the LandingPage layout we accept every type of content. But we can filter that and only accept for example an `BackBee\ClassContent\Landing\Contentainer`:

```yml
accept: ['Landing\Container']
```

##### Column -> maxentry setting

That option permit to add a maximum type of accepted content into the page. The default value and 0 is equal to unlimited.

##### Column -> defaultClassContent setting

The default ClassContent setting permit to initialize the layout with the entity provided. Example like `accept` setting. To initialize the layout with the entity `BackBee\ClassContent\Landing\Contentainer`, you have to write:

```yml
defaultClassContent: Landing\Contentainer
```

##### Column -> inherited setting

That setting determine the default behavior of your column, if you set at `true` this setting the column will inherit from the column in the same position in the parent page.

> repository/Layouts/LandingPage.twig

```twig
<!doctype html>
<html lang="en">
  <head>
    {{ this.partial('partials/head.twig')|raw }}
  </head>
  <body>
    {{ this.bbtoolbar()|raw }}
    <div id="bb5-site-wrapper">
      <div class="main container">
        {{ this.container().first()|raw }}
      </div>
      <div class="side pane">
        {{ this.container().next()|raw }}
      </div>
    </div>
  </body>
</html>
```

In this template there few think require:

- `bbtoolbar` permit to print the administration toolbar.
- `id="bb5-site-wrapper"` is an identifier used by the toolbar to know where the Site content.
- `this.container().first()` print the first column of your layout definition
- `this.container().next()` print the next column of your layout definition, and if three column you continue to iterate the columns with this instruction.

#### Create a Layout template

As we see before with `template` setting, the template must have the same filename as the definition (in the case of template is setted with the default option). The folder by default for your Layout is `repository/Layouts` but folder work like

#### Save the layout

 To save the layout in database you have to use the commands line provided by `LayoutBuilderBundle`.

##### Create command

Creation command don't override older layout and only create new template.

```bash
./backbee layout::create
```

Like this, the command will iterate all your layouts definitions and create this layout for each Site available.
To filter layout want create use the option `--layout=` and specify the layout you want create:

```bash
./backbee layout::create --layout=LandingPage
```

If you have a multi site and you want add the layout to one specific site, you can use the option `--site=` and specify the label or domain name to this site

```bash
./backbee layout::create --site="My awesome Site"
```

And then, you can combine the 2 options.


##### Update command

Update command has the same options than create and do the same job, but this command override automatically Layout already save in database. Then use it carefully.

Remove or add a column to a layout already used don't generate crash off this existent pages but the number off will not automatically upgrade to this older pages.

##### Remove command

If you want delete an Layout, you can use this command. And this command accept to work only under some conditions.

The firsts condition is, the layout you have to remove, have to be unused.
And you have to use all the previous options `--layout` and `--site`.
