# CookBooks

## Create new commands

The Console page of the Components section covers how to create a console application.
This cookbook article covers the creation of a command using BackBee Standard Edition.

### Automatically Registering Commands

To make the console commands available automatically with BackBee, create a
``Command`` directory inside your bundle and create a PHP file suffixed with
``Command.php`` for each command that you want to provide. For example, if you
want to extend the DemoBundle to welcome you from the command line, create
``WelcomeCommand.php`` and add the following to it:

```php
// bundle/WelcomeBundle/Command/WelcomeCommand.php
namespace WelcomeBundle\Command;

use Symfony\Component\Console\Input\InputInterface;
use Symfony\Component\Console\Input\InputArgument;
use Symfony\Component\Console\Input\InputOption;
use Symfony\Component\Console\Output\OutputInterface;


use BackBee\Console\AbstractCommand;

class WelcomeCommand extends AbstractCommand
{
    protected function configure()
    {
        $this
            ->setName('demo:welcome')
            ->setDescription('Welcome someone')
            ->addArgument(
                'name',
                InputArgument::OPTIONAL,
                'Who do you want to welcome?'
            )
            ->addOption(
                'yell',
                null,
                InputOption::VALUE_NONE,
                'If set, the message will yell in uppercase letters'
            )
            ->setHelp(<<<EOF
The <info>%command.name%</info> command can welcome someone.
With the option ``yell``, the message will be displayed in uppercase letters.
EOF;
            )
        ;
    }

    protected function execute(InputInterface $input, OutputInterface $output)
    { 
        $text = 'Welcome '.$input->getArgument('name');

        if ($input->getOption('yell')) {
            $text = strtoupper($text);
        }

        $output->writeln($text);
    }
}
```

This command will now automatically be available to run:

```bash
    $ ./backbee demo:welcome Charles
```