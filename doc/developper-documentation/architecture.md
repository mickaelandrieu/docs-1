# Developper documentation

## Architecture

BackBee CMS relies on many Symfony components and on Doctrine 2.

There is the list of components currently used by BackBee CMS:

* Config   
* Console   
* Debug   
* DependencyInjection   
* EventDispatcher
* Expression Language
* Filesystem
* HttpFoundation
* HttpKernel
* Routing
* Security ACL
* Security Core
* Security HTTP
* Serializer
* Translation
* Validator
* Yaml
* (Twig)

BackBee CMS depends on a PHP application (the BackBee "Core") which provide a REST API to manage all the contents,
the users, the media and the security and to a REST Javascript client which provide to the final users a toolbar
to ease the management of the contents.

To understand how BackBee CMS is built, take a look on this schema:

 ![The BackBee architecture](http://i.imgur.com/XfRFyLE.png)