# Developer documentation

## Installation

Make sure you have basic knowledge of PHP5, Apache or Nginx and MySQL to install BackBee in developer mode.

### Third party Software

First of all, you need to check that your computer has an appropriate working environment for web development. You need at least a web server (Apache, for instance), a database engine (MySQL, PostgreSQL, SQLite, or any PDO-compatible database engine), and **PHP 5.4**.

You need to install [git](http://git-scm.com/) and [Composer](https://getcomposer.org/), the dependency manager for PHP.

If your are on Linux/Unix based (like ubuntu or Mac OS X), follow this [link](https://getcomposer.org/doc/00-intro.md#globally) for installation. Composer is also available on [Windows system](https://getcomposer.org/doc/00-intro.md#installation-windows).

In a command line interface, check if ``composer`` is correctly installed:

![composer on cmder](http://i.imgur.com/xDZi6Sc.png "composer")

Also, check your PHP version which should be 5.4 or more.

![PHP on cmder](http://i.imgur.com/DkgQJz2.png "PHP")


Set up a domain name in your operating system.
On Unix/Linux based OS, you can add it into ``/etc/hosts`` file. In our example: **blogbee.dev**:

    127.0.0.1   blogbee.dev


You can now launch the installation process.

### Web Installer

Open a command line interface and get BackBee:

    $ composer create-project "backbee/backbee-standard" /path/to/your/folder "1.0" -n

Then on the ``public`` directory, launch the builtin server of PHP:

    $ cd /path/to/your/folder/public && php -S blogbee.dev:8000

And you should see the first installation step of BackBee (access [http://blogbee.dev:8000/install.php](http://blogbee.dev:8000/install.php)) in a web browser:

![BackBee Installer - first step](http://i.imgur.com/saok4nc.png "BackBee Installer - first step")

You need to create ``cache`` and ``log`` folders with the correct rights, then you can refresh the page and access to the **second step** of the installer.

![BackBee Installer - step 2](http://i.imgur.com/pvaDJIH.png "BackBee Installer - step 2")

If required, check the ``repository/Config`` folder rights and then go to the **third** step.

You need to set your database settings. If you use a different database engine than MySQL or MariaDB, set the correct driver else you have nothing to change, only fill the fields **database name**, **username** and **password**.

![BackBee Installer - step 3](http://i.imgur.com/f6ejuwI.png "BackBee Installer - step 3")

You also need to define your super admin user, fill the credentials then save and continue to the **fourth** step.

![BackBee Installer - step 3 bis](http://i.imgur.com/ZENfnSS.png "BackBee Installer - step 3 bis")

BackBee Installer creates and populates the database, fill the last fields to complete the installation process: the **site_name** and the **domain**.

You can also check the option "Create demonstration website" if you want to install a complete demonstration based on a press website, see [The MAG](http://early.backbee.com/).

![BackBee Installer - step 4](http://i.imgur.com/BcZ3RNA.png "BackBee Installer - step 4")

Installation is now completed: BackBee gives you the ``Apache`` or ``Nginx`` configuration you have to set up to be able to use BackBee on the chosen domain.

![BackBee Installer - step 5](http://i.imgur.com/T13tVjT.png "BackBee Installer - step 5")

![BackBee Installer - step 5 bis](http://i.imgur.com/ePKuLX5.png "BackBee Installer - step 5 bis")

When your web server is set up, access the site by going to **[http://blogbee.dev](http://blogbee.dev)**.

If everything is correctly installed, you should see the BackBee welcome message:

![BackBee welcome message](http://i.imgur.com/rWY8IQk.png "BackBee welcome message")


> *If you can't see BackBee welcome message, make sure that ``cache`` and ``log`` folders have the correct rights. (755 or 777)*


You can now enter your new backoffice buy pressing  ``CTRL + ALT + B``. Fill the login dialog with the credentials you have set up in the fourth step.

![BackBee Installed - Edition mode](http://i.imgur.com/6NUBWCG.png "BackBee Installed - Edition mode")
