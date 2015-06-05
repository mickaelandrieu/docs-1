# Developper documentation

## Requirements for running BackBee CMS

To run BackBee CMS, your system needs to adhere to a list of requirements.

Below is the list of required and optional requirements.

### Required

Because BackBee CMS is built on top of Symfony components, theses requirements are required.

* ``JSON`` needs to be enabled
* ``ctype`` needs to be enabled
* ``mbstring`` needs to be enabled
* Your php configuration needs to have the date.timezone setting completed

More, PHP needs to be a minimum version of **PHP 5.4.0**.

### Optional

* You (may) need to have the PHP-XML module installed
* You (may) need to have at least version 2.6.21 of libxml
* You (may) need to have PHP tokenizer module enabled
* You (may) need to have iconv module enabled
* You (may) need to have POSIX enabled (only on *nix)
* You (may) need to have Intl installed with ICU 4+
* You (may) need to have APC 3.0.17+ installed (or another opcode cache needs to be installed)
* php configuration recommended settings:

```
short_open_tag = Off

magic_quotes_gpc = Off

register_globals = Off

session.auto_start = Off
```


### Doctrine2 (Database management)

Because we depend on Doctrine 2, you will need to have PDO installed. Additionally, you need to have the PDO driver installed for the database server you want to use.
Also, you need ``php5-sqlite`` extension to be installed and enabled during the installation process.
