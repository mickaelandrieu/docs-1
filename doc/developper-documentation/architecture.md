# Developer documentation

## Architecture

BackBee CMS relies on many Symfony components and Doctrine 2.

Here is the list of components currently used by BackBee CMS:

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

BackBee CMS is based on a PHP application (the BackBee "Core") which provides a REST API managing all the contents,
the users, the media and the security. A REST Javascript client provides user interface.

To understand how BackBee CMS is built, take a look on this schema:

 ![The BackBee architecture](http://i.imgur.com/XfRFyLE.png)
