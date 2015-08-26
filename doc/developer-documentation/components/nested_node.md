# Components

## The NestedNode Component

### What is it

NestedNode component regroups every BackBee object using the Nested set model. In this component, you can find ``KeyWord``, ``Media``, ``MediaFolder``, ``Page`` and ``Section``.

### Nested set model

> The nested set model is to set to count the nodes according to a tree traversal, which visits each node twice, assigning numbers in the visiting order, and at both visits. This leaves two numbers for each node, which are stored as two attributes. Querying becomes inexpensive: hierarchy membership can be tested by comparing these numbers. Updating requires renumbering and is therefore expensive. Refinements that use rational numbers instead of integers can avoid renumbering, and so are faster to update, although much more complicated.

> [wiki](https://en.wikipedia.org/wiki/Nested_set_model)

### KeyWord

KeyWord entity is hierarchical tree of keywords.

KeyWord in the CMS are used as keyword, tag and in rare case category. These words can be attach to a `ClassContent` by using the native content Keyword.


### Media and MediaFolder

MediaFolder entity is hierarchical tree of folders that contain Media.

These two objects are used in the Media library and like the KeyWord Object, Media is attached to a ClassContent and more precisely all medias with this namespace `BackBee\ClassContent\Media`


### Page and Section

Page and Section work like respectively Media and MediaFolder. Only Sections are hierarchical entities.

To lighten the page Nested set model, the BackBee team has decided to make a distinction between pages with children (section) and pages without children (page).

When you create new pages it has a reference to a section. It will automatically become a section when you attach a child to it.

#### Create a page by the code

To ease page creation, we recommand you to use PageBuilder. See an example below:

```php
<?php
// retrieve an application instanced or create new application
$app = new \BackBee\Standard\Application();
// Load the layout
$layout = $app->getEntityManager()->getRepository('BackBuilder\Site\Layout')->findBy(['label' => 'Home']);

$page = $app->getContainer()->get('pagebuilder')
// Add an title to your page
  ->setTitle('My awesome Page')
// Add an Layout
  ->setLayout($layout)
// Add Site where your page is available
  ->setSite($app->getSite())
// Define your page as onlinne
  ->putOnlineAndHidden()
// Add a content inner your page
  ->pushElement(new \BackBee\ClassContent\Article\Article())
  ->getPage();

$app->getEntityManager->persist($page);
$app->getEntityManager->flush($page);
```
