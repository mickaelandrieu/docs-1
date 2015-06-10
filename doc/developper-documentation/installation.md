# Developper documentation

## Installation

Installation of BackBee in developper mode assume assume you have basic knowledge of PHP5, Apache or Nginx and MySQL.

### Third party Software

First of all, you need to check that your computer has a friendly working environment for web development. At a minimum, you need a web server (Apache, for instance), a database engine (MySQL, PostgreSQL, SQLite, or any PDO-compatible database engine), and **PHP 5.4** or later.

At least, you need to install [git](http://git-scm.com/) and [Composer](https://getcomposer.org/), the dependency manager for PHP.

Follow this link to install it if your are on [Linux/Unix based OS](https://getcomposer.org/doc/00-intro.md#globally) (like ubuntu or Mac OS X), Composer is also available on [Windows system](https://getcomposer.org/doc/00-intro.md#installation-windows).

In a command line interface, check if ``composer`` is correctly installed:

![composer on cmder](http://i.imgur.com/xDZi6Sc.png "composer")

Also, check your PHP version which should be 5.4 or greater.

![PHP on cmder](http://i.imgur.com/DkgQJz2.png "PHP")


Eventually, you need to set up a domain name in your operating system.
On Unix/Linux based OS, you can add it into ``/etc/hosts`` file, we will choose **blogbee.dev**:

    127.0.0.1   blogbee.dev


Ok ! We can now launch the installation process.

### Web Installer

Open a command line interface and get BackBee:

    $ composer create-project "backbee/backbee-standard" /path/to/your/folder "1.0" -n

Then on the ``public`` directory, launch the builtin server of PHP:

    $ cd /path/to/your/folder/public && php -S blogbee.dev:8000

And you should see the first installation step of BackBee (access [http://localhost:8000/install.php](http://localhost:8000/install.php)) in a web browser:

![BackBee Installer - first step](http://i.imgur.com/saok4nc.png "BackBee Installer - first step")

You need to create ``cache`` and ``log`` folders with the correct rights, then you can refresh the page and access to the **second step** of the installer.

![BackBee Installer - step 2](http://i.imgur.com/pvaDJIH.png "BackBee Installer - step 2")

If required, check the ``repository/Config`` folder rights and then proceed with the **third** step.

You need to set your database settings. If you use a different database engine than MySQL or MariaDB, set the correct driver else you have nothing to change, only fill the fields **database name**, **username** and **password**.

![BackBee Installer - step 3](http://i.imgur.com/f6ejuwI.png "BackBee Installer - step 3")

You also need to define your super admin user, fill the credentials then save and continue to the **fourth** step.

![BackBee Installer - step 3 bis](http://i.imgur.com/ZENfnSS.png "BackBee Installer - step 3 bis")

BackBee Installer creates and populates the database, fill the last information to complete the installation process: the **site_name** and the **domain**.

![BackBee Installer - step 4](http://i.imgur.com/kk4xNPw.png "BackBee Installer - step 4")

You are done: BackBee gives you the ``Apache`` or ``Nginx`` configuration you have to set to be able to use BackBee on the chosen domain.

![BackBee Installer - step 5](http://i.imgur.com/T13tVjT.png "BackBee Installer - step 5")

![BackBee Installer - step 5 bis](http://i.imgur.com/ePKuLX5.png "BackBee Installer - step 5 bis")

When you have set your web server, access the site by going to **[http://blogbee.dev](http://blogbee.dev)**.

If everything is correctly installed, you should see the BackBee welcome message:

![BackBee welcome message](http://i.imgur.com/rWY8IQk.png "BackBee welcome message")


> *If you can't see BackBee welcome message, check both ``cache`` and ``log`` folders have the correct rights.*


You can even connect to the "Edition mode", press  ``CTRL + ALT + B`` to show the login dialog. Note that we created a super admin user for you with login (=admin) and password (=admin).

![BackBee Installed - Edition mode](http://i.imgur.com/6NUBWCG.png "BackBee Installed - Edition mode")