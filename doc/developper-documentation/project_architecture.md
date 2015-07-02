# Developper documentation

## Project Architecture

### Global Architecture

Global Architecture is build by default by BackBee, it's compose the folders:
- `bundle` optional folder, create it only if you want develop your own bundle. By default the bundle is installed into the vendor folder
- `cache` optional  and can be placed out of the project and is location have to be specified into `services.yml` into the `Config` folder.
- `log` same as cache folder
- `public` folder it is the document root of your website
- `repository` The folder where you customize your project it's more develop after
- `vendor` folder build by composer where you can find every dependencies


### Repository folder Architecture

Repository folder (writhed `repository`) is certainly the most important folder of your project, is in this folder where you will customize entirely your BackBee project. If you start from standard edition you have some folder available, but you can add lot of more and we are going to enumerate every folder it's possible to add (Take note of you have to respect the Case).


- `ClassContent` This folder contains your contents definitions

- `Config` Here you can find and override each config file of BackBee

- `Controller` In this folder your controller will be automatically auto-loaded

- `Data` Folder where every uploaded file are stored. Against cache and log folder, data can moved out of the project by customizing `services.yml`

- `Layouts` Layouts template are stored in this folder.

- `Listener` In this folder your Event Listeners will be automatically auto-loaded.

- `Resources` In this folder you put your statics files like images, JS scripts and CSS.

- `Templates` Folder where you put your helpers into `helpers` subfolder, partials into `partials` subfolder and ClassContent templates into `scripts` subfolder.

- `Traits` In this folder your ClassContent's Trait will be automatically auto-loaded.

- `Translation` Your translation catalog will automatically auto-loaded in this folder.

### Context and repository folder overriding

In BackBee repository folder can be contextualized. You use an contextualized repository when you start your application, it's the first parameter in construct method.

```php
<?php
$app = new \BackBee\Standard\Application('newContext');
// ...
```

If you initialize your application like this, you have the possibility to extend the entire repository folder. Before will see the repository folder architecture then you can reproduce it into your context.

The context repository, have to be created into the `repository` folder and have the same name as the key you pass in first parameter of the Application.

After this you override the file you want by reproducing the folder path and the file. And for the other element, you don't need to reproduce it, it will automatically inherited.

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

In this case, doctrine configuration will be override to use an other database for example.

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

In this case the "newContext" instance have access to two type of ClassContent and the default configuration only one.

And of course you can combine the two example.