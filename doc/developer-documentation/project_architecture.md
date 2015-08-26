# Developer documentation

## Project Architecture

### Global Architecture

Global Architecture is build by default by BackBee, it composes the folders:
- `bundle` optional folder, create it only if you want develop your own bundle. By default the bundle is installed into the vendor folder

- `cache` optional and can be placed out of the project and is location have to be specified into `services.yml` into the `Config` folder.

- `log` same as cache folder

- `public` folder it is the document root of your website

- `repository` The folder where you customize your project (see below)

- `vendor` folder build by composer where you can find every dependencies


### Repository folder Architecture

Repository folder (writhed `repository`) is certainly the most important folder of your project. In this folder you will entirely customize your BackBee project. If you start from standard edition you have some folder available, but you can add lot of more and we are going to enumerate every folder that is possible to add (Take note of you have to respect the Case).


- `ClassContent` This folder contains your content definitions

- `Config` Here you can find and override each config file of BackBee

- `Controller` In this folder your controller will be automatically auto-loaded

- `Data` Folder where every uploaded file is stored. As the cache and log folder, data can be moved out of the project by customizing `services.yml`

- `Layouts` Layout templates are stored in this folder.

- `Listener` In this folder your Event Listeners will be automatically loaded.

- `Resources` In this folder you put your static files like images, JS scripts and CSS.

- `Templates` Folder where you put your helpers into `helpers` subfolder, partials into `partials` subfolder and ClassContent templates into `scripts` subfolder.

- `Traits` In this folder your ClassContent's Trait will be automatically loaded.

- `Translation` Your translation catalog will automatically loaded in this folder.

### Context and repository folder overriding

In BackBee repository folder can be contextualized. You use a contextualized repository when you start your application, it's the first argument in construct method.

```php
<?php
$app = new \BackBee\Standard\Application('newContext');
// ...
```

If you initialize your application like this, you have the possibility to extend the entire repository folder. Before will see the repository folder architecture then you can reproduce it into your context.

The context repository has to be created inside the `repository` folder and has to have the same name as the key you pass as first parameter to the Application.

After this you override the file you want by reproducing the folder path and the file. For the other files, if you have not defined them, they will automatically be inherited.

```path
repository/
  ClassContent/
  Config/
    doctrine.yml
  Data/
  Listener/
  newContext/
    Config/
      doctrine.yml
  Resources/
  ...
```

In this case, doctrine configuration will be overwritten to use an other database for example.

```path
repository/
  ClassContent/
    AnContent.yml
  ...
  newContext/
    ClassContent/
      AnotherContent.yml
  Resources/
  ...
```

In this case the "newContext" instance has access to two types of ClassContent and the default configuration only one.

And of course you can combine the two examples.
