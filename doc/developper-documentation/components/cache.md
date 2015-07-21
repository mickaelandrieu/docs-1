# Components

## The Cache Component

The Cache component provides many adapters to the most used and cache systems for validating/invalidating the cache.

## Cache Adapters

* APC Adapter: Uses ``APC`` cache if available
* DAO Adapter: Uses database cache system
* FileAdapter: Uses file system
* Memcache Adapter: Uses ``memcache`` if available and set up
* Memcached Adapter: Uses ``memcached`` if available and set up

There is also a special one, used when cache is not activated: the ``NoCache`` adapter.

You can create your own adapters implementing the ``CacheInterface`` interface:

```php
<?php
/*
 * Copyright (c) 2011-2015 Lp digital system
 *
 * This file is part of BackBee.
 *
 * BackBee is free software: you can redistribute it and/or modify
 * it under the terms of the GNU General Public License as published by
 * the Free Software Foundation, either version 3 of the License, or
 * (at your option) any later version.
 *
 * BackBee is distributed in the hope that it will be useful,
 * but WITHOUT ANY WARRANTY; without even the implied warranty of
 * MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
 * GNU General Public License for more details.
 *
 * You should have received a copy of the GNU General Public License
 * along with BackBee. If not, see <http://www.gnu.org/licenses/>.
 *
 * @author Charles Rouillon <charles.rouillon@lp-digital.fr>
 */
namespace BackBee\Cache;
use Psr\Log\LoggerInterface;
use BackBee\Cache\Exception\CacheException;
/**
 * Abstract class for cache adapters.
 *
 * @category    BackBee
 *
 * @copyright   Lp digital system
 * @author      Mickaël Andrieu <mickael.andrieu@lp-digital.fr>
 */
interface CacheInterface
{
    /**
     * Returns the available cache for the given id if found returns false else.
     *
     * @param string    $id          Cache id
     * @param boolean   $bypassCheck Allow to find cache without test it before
     * @param \DateTime $expire      Optionnal, the expiration time (now by default)
     *
     * @return string|false
     */
    public function load($id, $bypassCheck, \DateTime $expire);
    /**
     * Tests if a cache is available or not (for the given id).
     *
     * @param string $id Cache id
     *
     * @return int|false the last modified timestamp of the available cache record (0 infinite expiration date)
     */
    public function test($id);
    /**
     * Saves some string datas into a cache record.
     *
     * @param string $id       Cache id
     * @param string $data     Datas to cache
     * @param int    $lifetime Optional, the specific lifetime for this record
     *                         (by default null, infinite lifetime)
     * @param string $tag      Optional, an associated tag to the data stored
     *
     * @return boolean true if cache is stored false otherwise
     */
    public function save($id, $data, $lifetime, $tag);
    /**
     * Removes a cache record.
     *
     * @param string $id Cache id
     *
     * @return boolean true if cache is removed false otherwise
     */
    public function remove($id);
    /**
     * Clears all cache records.
     *
     * @return boolean true if cache is cleared false otherwise
     */
    public function clear();
    /**
     * Sets the cache logger.
     *
     * @param \Psr\Log\LoggerInterface $logger
     *
     * @return \BackBee\Cache\CacheAdapterInterface
     */
    public function setLogger(LoggerInterface $logger);
    /**
     * Gets the cache logger.
     *
     * @return \Psr\Log\LoggerInterface $logger
     */
    public function getLogger();
    /**
     * Returns the cache context.
     *
     * @return string|null
     */
    public function getContext();
    /**
     * Sets the cache context.
     *
     * @param string $context
     *
     * @return \BackBee\Cache\CacheAdapterInterface
     */
    public function setContext($context);
    /**
     * Logs a message on provided level if a logger is defined.
     *
     * @param string $level   The log level
     * @param string $message The message to log
     * @param array  $context The logging context
     */
    public function log($level, $message, array $context);
    /**
     * Returns the expiration timestamp.
     *
     * @param int $lifetime
     *
     * @return int
     */
    public function getExpireTime($lifetime);
    /**
     * Control the lifetime against min and max lifetime if provided.
     *
     * @param int $lifetime
     *
     * @return int
     */
    public function getControledLifetime($lifetime);
}
```

You can also extend the ``AbstractCache`` class provided by the Cache component.

## Cache validating/invalidating

Thanks to the ``BackBee\Cache\CacheValidator`` class, you can use the Symfony Validator component to validate or invalidate your cache. Let's see an use case.

The CacheListener occurs at prerender at postrender and flush with contents. Before going deeper into each of these methods, we need to test various parameters of the application and the application. So we can define a validation group called ' cache_status '. This group contains two validators :

* ApplicationCoreValidator check:
    * that the user is not a BBUser;
    * that the application is not in debug mode
    * the application has started and that is not a customer SAPI
* RequestValidator check:
    * that the request method is GET because we do not want to hide the POST or PUT requests for example.

The execution of prerender methods postrender and flush a content CacheListener is conditional that respond positively CacheValidator :

```php
<?php
class CacheListener
{
    public function onPreRenderContent(BackBee\Event\Event $event)
    {
        $cache_validator = $event->getApplication()
            ->getContainer()
            ->get('cache.validator');

        if (!$cache_validator->isValid('cache_status', $event->getTarget())) { // the second parameter of isValid() is optional
            return;
        }

        // execute your pre-render tasks
    }
}
```

You can set as many validation group as you want and create as validator as you like provided they implement all the ``BackBee\Cache\Validator\ValidatorInterface`` interface and are declared in a services.yml (that of the repository or any of your bundles) with ``cache.validator`` tag. 

This last point allows the CacheValidator load them automatically so you do add them manually with CacheValidator ``addValidator::method()``.

## Cache key generator

The BackBee Cache component provide a centralized cache key generator: the class ``BackBee\Cache\CacheIdentifierGenerator``.

This service (accessible from the container application with id ``cache.identifier_generator``) generates a new cache id as the generator group that you provide as a parameter.

These groups consist of one or more appenders.

You can define as generator groups as you like and arbitrarily select the number of appender in each of its groups. The only requirement is that a generator must necessarily implement the ``BackBee\Cache\IdentifierAppender\IdentifierAppenderInterface interface``.

They can be automatically loaded into the CacheIdentifierGenerator if you declare them as a repository of service or one of your bundles .

Note that each generator must have the tag ``cache.identifier.appender``.
