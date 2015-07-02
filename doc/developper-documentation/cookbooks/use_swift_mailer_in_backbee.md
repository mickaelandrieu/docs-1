# CookBooks

## Use Swiftmailer in BackBee

### Introduction

In BackBee there is no home made component for mailing. We actually use Swift Mailer library.
BackBee only provides a way to configure it and get your instance initialized easily.

## Configuration

To configure Swift Mailer in BackBee you need to create mailer.yml into your global configuration folder `repository/Config`.

The minimal configuration file is composed of an SMTP and  a port

```yml
smtp: localhost
port: 25
```

You can also set up more parameters for more security on SMTP.

```yml
smtp: localhost
port: 587
username: mailer_user_identifier
password: superSecretPassword
```

### Basic usage example

To retrieve the configured mailer you need an access to your Application.
You can start with a new app, but for performances matters prefer use the current Application.
The example will be placed in the context of an EventListener. *This is not a good practice to place your business logic into an EventListener* but it is easier to understand.

```php
<?php
namespace BackBee\Bundle\DocumentationBundle\Listener;

use BackBee\Event\Event;
use BackBee\Site\Site;

class MailerListener
{
    public static function onSomethingNeedToBeMailed(Event $event)
    {
        // retrieve your current Application
        $application = $event->getDispatcher()->getApplication();

        // retrieve other informations
        $site = $application->getSite();

        if ($site instanceof Site) {
            // render e-mail body by using partial method from the renderer
            $body = $application->getRenderer()->partial(
                'Email/Email.twig',
                [
                    'site_name' => $site->getLabel(),
                    'home_link' => $site->getServerName(),
                ]
            );

            // Build the message
            $message = static::buildMessage(
                $body,
                $application->getSite(),
            );

            // now get the configured mailer by passing by application and send your message
            $application->getMailer()->send($message);
        }
    }

    /**
     * E-mail message creation
     */
    public static function buildMessage($body, $site)
    {
        $from = 'administrator@' . $site->getServerName();

        // Create Swift_Message
        $message = \Swift_Message::newInstance();
        // Set the From address
        $message->addFrom($from, $site->getLabel());
        // Set the To addresses
        $message->addTo($from, $site->getLabel());
        // Give the message a subject
        $message->setSubject('Something append on ' . $site->getLabel());
        // Give it the body
        $message->setBody($body, 'text/html', 'UTF-8');

        return $message;
    }
}
```

If you want do more with Swift Mailer, you can find additional documentation on [Swift Mailer web site](http://swiftmailer.org/)
