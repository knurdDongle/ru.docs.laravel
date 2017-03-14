# Laravel Homestead

- [Введение](#introduction)
- [Установка и настройка](#installation-and-setup)
    - [Первые шаги](#first-steps)
    - [Configuring Homestead](#configuring-homestead)
    - [Launching The Vagrant Box](#launching-the-vagrant-box)
    - [Per Project Installation](#per-project-installation)
    - [Installing MariaDB](#installing-mariadb)
- [Daily Usage](#daily-usage)
    - [Accessing Homestead Globally](#accessing-homestead-globally)
    - [Connecting Via SSH](#connecting-via-ssh)
    - [Connecting To Databases](#connecting-to-databases)
    - [Adding Additional Sites](#adding-additional-sites)
    - [Configuring Cron Schedules](#configuring-cron-schedules)
    - [Ports](#ports)
- [Network Interfaces](#network-interfaces)
- [Updating Homestead](#updating-homestead)
- [Old Versions](#old-versions)

<a name="introduction"></a>
## Введение

Laravel стремится сделать восхитительным весь опыт разработки на PHP, включая вашу локальную среду разработки. [Vagrant](https://www.vagrantup.com) предоставляет простой и элегантный способ управления и обеспечения виртуальными машинами.

Laravel Homestead - это официальный, предварительно упакованный образ Vagrant (box), который предоставляет замечательную среду разработки, без необходимости устанавливать PHP, веб-сервер и другое серверное ПО на вашей локальной машине. 

Больше не стоит беспокоится о том что вы можете испортить свою операционную систему! Боксы Vagrant полностью одноразовые. Если что-то пойдет не так, вы сможете уничтожить и пересоздать бокс за считанные минуты!

Homestead запускается на любых системах Windows, Mac и Linux, и включает веб-сервер Nginx, PHP 7.1, MySQL, Postgres, Redis, Memcached, Node и другие вкусности, которые могут потребоваться вам для разработки потрясающих Laravel-приложений.

> {Примечание} Если вы используете Windows, Вам может потребоваться включить аппаратную виртуализацию (VT-x). Это обычно включается через ваш BIOS. Если вы используете Hyper-V на UEFI системе, вам возможно потребуется дополнительно отключить Hyper-V для доступа к VT-x.

<a name="included-software"></a>
### Включенный софт

- Ubuntu 16.04
- Git
- PHP 7.1
- Nginx
- MySQL
- MariaDB
- Sqlite3
- Postgres
- Composer
- Node (With Yarn, PM2, Bower, Grunt, и Gulp)
- Redis
- Memcached
- Beanstalkd

<a name="installation-and-setup"></a>
## Установка и настройка

<a name="first-steps"></a>
### Первые шаги

Перед запуском среды Homestead, вы должны установить [VirtualBox 5.1](https://www.virtualbox.org/wiki/Downloads), [VMWare](https://www.vmware.com), или [Parallels](http://www.parallels.com/products/desktop/) а также [Vagrant](https://www.vagrantup.com/downloads.html). Все эти программные пакеты имеют легкие в использовании визуальные инсталяторы для всех популярных операционных систем.

Для использования провайдера VMware, вам потребуеться преобрести как VMware Fusion / Workstation, так и [VMware Vagrant plug-in](https://www.vagrantup.com/vmware). Хотя это не бесплатно, VMware может предоставить быструю производительность для совместного доступа к папкам с коробки.

Для использования провайдера Parallels, вам потребуеться установить [Parallels Vagrant plug-in](https://github.com/Parallels/vagrant-parallels). Это бесплатно.

#### Установка бокса Homestead Vagrant

После установки VirtualBox / VMware и Vagrant, вам следует добавить бокс `laravel/homestead` в Vagrant, используя следующую команду в терминале. Процесс загрузки бокса займет какое-то время, в зависимости от скорости вашего интернет-соединения:

```bash
vagrant box add laravel/homestead
```

Если эта команда не сработает, убедитесь в том что у вас установленна последняя версия Vagrant.

#### Установка Homestead

Вы можете установить Homestead простым клонированием репозитория. 

Желательно клонировать репозиторий с папкой `Homestead` находясь в вашем "домашнем" каталоге, так как бокс Homestead будет служить хостом для всех ваших проектов Laravel:

```bash
cd ~
git clone https://github.com/laravel/homestead.git Homestead
```

После клонирования репозитория Homestead, запустите команду `bash init.sh` внутри директории Homestead, чтобы создать файл конфигурации `Homestead.yaml`. Файл `Homestead.yaml` будет помещён в скрытую директорию `~/.homestead`:

```bash
// Mac / Linux...
bash init.sh

// Windows...
init.bat
```

<a name="configuring-homestead"></a>
### Configuring Homestead

#### Setting Your Provider

The `provider` key in your `~/.homestead/Homestead.yaml` file indicates which Vagrant provider should be used: `virtualbox`, `vmware_fusion`, `vmware_workstation`, or `parallels`. You may set this to the provider you prefer:

    provider: virtualbox

#### Configuring Shared Folders

The `folders` property of the `Homestead.yaml` file lists all of the folders you wish to share with your Homestead environment. As files within these folders are changed, they will be kept in sync between your local machine and the Homestead environment. You may configure as many shared folders as necessary:

    folders:
        - map: ~/Code
          to: /home/vagrant/Code

To enable [NFS](https://www.vagrantup.com/docs/synced-folders/nfs.html), just add a simple flag to your synced folder configuration:

    folders:
        - map: ~/Code
          to: /home/vagrant/Code
          type: "nfs"

You may also pass any options supported by Vagrant's [Synced Folders](https://www.vagrantup.com/docs/synced-folders/basic_usage.html) by listing them under the `options` key:

    folders:
        - map: ~/Code
          to: /home/vagrant/Code
          type: "rsync"
          options:
              rsync__args: ["--verbose", "--archive", "--delete", "-zz"]
              rsync__exclude: ["node_modules"]


#### Configuring Nginx Sites

Not familiar with Nginx? No problem. The `sites` property allows you to easily map a "domain" to a folder on your Homestead environment. A sample site configuration is included in the `Homestead.yaml` file. Again, you may add as many sites to your Homestead environment as necessary. Homestead can serve as a convenient, virtualized environment for every Laravel project you are working on:

    sites:
        - map: homestead.app
          to: /home/vagrant/Code/Laravel/public

If you change the `sites` property after provisioning the Homestead box, you should re-run `vagrant reload --provision`  to update the Nginx configuration on the virtual machine.

#### The Hosts File

You must add the "domains" for your Nginx sites to the `hosts` file on your machine. The `hosts` file will redirect requests for your Homestead sites into your Homestead machine. On Mac and Linux, this file is located at `/etc/hosts`. On Windows, it is located at `C:\Windows\System32\drivers\etc\hosts`. The lines you add to this file will look like the following:

    192.168.10.10  homestead.app

Make sure the IP address listed is the one set in your `~/.homestead/Homestead.yaml` file. Once you have added the domain to your `hosts` file and launched the Vagrant box you will be able to access the site via your web browser:

    http://homestead.app

<a name="launching-the-vagrant-box"></a>
### Launching The Vagrant Box

Once you have edited the `Homestead.yaml` to your liking, run the `vagrant up` command from your Homestead directory. Vagrant will boot the virtual machine and automatically configure your shared folders and Nginx sites.

To destroy the machine, you may use the `vagrant destroy --force` command.

<a name="per-project-installation"></a>
### Per Project Installation

Instead of installing Homestead globally and sharing the same Homestead box across all of your projects, you may instead configure a Homestead instance for each project you manage. Installing Homestead per project may be beneficial if you wish to ship a `Vagrantfile` with your project, allowing others working on the project to simply `vagrant up`.

To install Homestead directly into your project, require it using Composer:

    composer require laravel/homestead --dev

Once Homestead has been installed, use the `make` command to generate the `Vagrantfile` and `Homestead.yaml` file in your project root. The `make` command will automatically configure the `sites` and `folders` directives in the `Homestead.yaml` file.

Mac / Linux:

    php vendor/bin/homestead make

Windows:

    vendor\\bin\\homestead make

Next, run the `vagrant up` command in your terminal and access your project at `http://homestead.app` in your browser. Remember, you will still need to add an `/etc/hosts` file entry for `homestead.app` or the domain of your choice.

<a name="installing-mariadb"></a>
### Installing MariaDB

If you prefer to use MariaDB instead of MySQL, you may add the `mariadb` option to your `Homestead.yaml` file. This option will remove MySQL and install MariaDB. MariaDB serves as a drop-in replacement for MySQL so you should still use the `mysql` database driver in your application's database configuration:

    box: laravel/homestead
    ip: "192.168.20.20"
    memory: 2048
    cpus: 4
    provider: virtualbox
    mariadb: true

<a name="daily-usage"></a>
## Daily Usage

<a name="accessing-homestead-globally"></a>
### Accessing Homestead Globally

Sometimes you may want to `vagrant up` your Homestead machine from anywhere on your filesystem. You can do this on Mac / Linux systems by adding a Bash function to your Bash profile. On Windows, you may accomplish this by adding a "batch" file to your `PATH`. This scripts will allow you to run any Vagrant command from anywhere on your system and will automatically point that command to your Homestead installation:

#### Linux

    function homestead() {
        ( cd ~/Homestead && vagrant $* )
    }

Make sure to tweak the `~/Homestead` path in the function to the location of your actual Homestead installation. Once the function is installed, you may run commands like `homestead up` or `homestead ssh` from anywhere on your system.

#### Windows

Create a `homestead.bat` batch file anywhere on your machine with the following contents:

    @echo off

    set cwd=%cd%
    set homesteadVagrant=C:\Homestead

    cd /d %homesteadVagrant% && vagrant %*
    cd /d %cwd%

    set cwd=
    set homesteadVagrant=

Make sure to tweak the example `C:\Homestead` path in the script to the actual location of your Homestead installation. After creating the file, add the file location to your `PATH`. You may then run commands like `homestead up` or `homestead ssh` from anywhere on your system.

<a name="connecting-via-ssh"></a>
### Connecting Via SSH

You can SSH into your virtual machine by issuing the `vagrant ssh` terminal command from your Homestead directory.

But, since you will probably need to SSH into your Homestead machine frequently, consider adding the "function" described above to your host machine to quickly SSH into the Homestead box.

<a name="connecting-to-databases"></a>
### Connecting To Databases

A `homestead` database is configured for both MySQL and Postgres out of the box. For even more convenience, Laravel's `.env` file configures the framework to use this database out of the box.

To connect to your MySQL or Postgres database from your host machine's database client, you should connect to `127.0.0.1` and port `33060` (MySQL) or `54320` (Postgres). The username and password for both databases is `homestead` / `secret`.

> {note} You should only use these non-standard ports when connecting to the databases from your host machine. You will use the default 3306 and 5432 ports in your Laravel database configuration file since Laravel is running _within_ the virtual machine.

<a name="adding-additional-sites"></a>
### Adding Additional Sites

Once your Homestead environment is provisioned and running, you may want to add additional Nginx sites for your Laravel applications. You can run as many Laravel installations as you wish on a single Homestead environment. To add an additional site, simply add the site to your `~/.homestead/Homestead.yaml` file:

    sites:
        - map: homestead.app
          to: /home/vagrant/Code/Laravel/public
        - map: another.app
          to: /home/vagrant/Code/another/public

If Vagrant is not automatically managing your "hosts" file, you may need to add the new site to that file as well:

    192.168.10.10  homestead.app
    192.168.10.10  another.app

Once the site has been added, run the `vagrant reload --provision` command from your Homestead directory.

<a name="configuring-cron-schedules"></a>
### Configuring Cron Schedules

Laravel provides a convenient way to [schedule Cron jobs](/docs/{{version}}/scheduling) by scheduling a single `schedule:run` Artisan command to be run every minute. The `schedule:run` command will examine the job schedule defined in your `App\Console\Kernel` class to determine which jobs should be run.

If you would like the `schedule:run` command to be run for a Homestead site, you may set the `schedule` option to `true` when defining the site:

    sites:
        - map: homestead.app
          to: /home/vagrant/Code/Laravel/public
          schedule: true

The Cron job for the site will be defined in the `/etc/cron.d` folder of the virtual machine.

<a name="ports"></a>
### Ports

By default, the following ports are forwarded to your Homestead environment:

- **SSH:** 2222 &rarr; Forwards To 22
- **HTTP:** 8000 &rarr; Forwards To 80
- **HTTPS:** 44300 &rarr; Forwards To 443
- **MySQL:** 33060 &rarr; Forwards To 3306
- **Postgres:** 54320 &rarr; Forwards To 5432

#### Forwarding Additional Ports

If you wish, you may forward additional ports to the Vagrant box, as well as specify their protocol:

    ports:
        - send: 93000
          to: 9300
        - send: 7777
          to: 777
          protocol: udp

<a name="network-interfaces"></a>
## Network Interfaces

The `networks` property of the `Homestead.yaml` configures network interfaces for your Homestead environment. You may configure as many interfaces as necessary:

    networks:
        - type: "private_network"
          ip: "192.168.10.20"

To enable a [bridged](https://www.vagrantup.com/docs/networking/public_network.html) interface, configure a `bridge` setting and change the network type to `public_network`:

    networks:
        - type: "public_network"
          ip: "192.168.10.20"
          bridge: "en1: Wi-Fi (AirPort)"

To enable [DHCP](https://www.vagrantup.com/docs/networking/public_network.html), just remove the `ip` option from your configuration:

    networks:
        - type: "public_network"
          bridge: "en1: Wi-Fi (AirPort)"

<a name="updating-homestead"></a>
## Updating Homestead

You can update Homestead in two simple steps. First, you should update the Vagrant box using the `vagrant box update` command:

    vagrant box update

Next, you need to update the Homestead source code. If you cloned the repository you can simply `git pull origin master` at the location you originally cloned the repository.

If you have installed Homestead via your project's `composer.json` file, you should ensure your `composer.json` file contains `"laravel/homestead": "^4"` and update your dependencies:

    composer update

<a name="old-versions"></a>
## Old Versions

You can easily override the version of the box that Homestead uses by adding the following line to your `Homestead.yaml` file:

    version: 0.6.0

An example:

    box: laravel/homestead
    version: 0.6.0
    ip: "192.168.20.20"
    memory: 2048
    cpus: 4
    provider: virtualbox

When you use an older version of the Homestead box you need to match that with a compatible version of the Homestead source code. Below is a chart which shows the supported box versions, which version of Homestead source code to use, and the version of PHP provided:

|   | Homestead Version | Box Version |
|---|---|---|
| PHP 7.0 | 3.1.0 | 0.6.0 |
| PHP 7.1 | 4.0.0 | 1.0.0 |
