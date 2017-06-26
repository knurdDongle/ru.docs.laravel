# Laravel Homestead

- [Введение](#introduction)
- [Установка и настройка](#installation-and-setup)
    - [Первые шаги](#first-steps)
    - [Настройка Homestead](#configuring-homestead)
    - [Запуск Vagrant Box](#launching-the-vagrant-box)
    - [Установка для каждого проекта](#per-project-installation)
    - [Установка MariaDB](#installing-mariadb)
- [Ежедневное использование](#daily-usage)
    - [Доступ к Homestead Глобально](#accessing-homestead-globally)
    - [Подключение через SSH](#connecting-via-ssh)
    - [Подключение к базам данных](#connecting-to-databases)
    - [Добавление дополнительных сайтов](#adding-additional-sites)
    - [Настройка расписания cron](#configuring-cron-schedules)
    - [Порты](#ports)
- [Сетевые интерфейсы](#network-interfaces)
- [Обновление Homestead](#updating-homestead)
- [Старые версии](#old-versions)

<a name="introduction"></a>
## Введение

Laravel стремится сделать восхитительным весь опыт разработки на PHP, включая вашу локальную среду разработки. [Vagrant](https://www.vagrantup.com) предоставляет простой и элегантный способ управления и обеспечения виртуальными машинами.

Laravel Homestead - это официальный, предварительно упакованный образ Vagrant (box), который предоставляет замечательную среду разработки, без необходимости устанавливать PHP, веб-сервер и другое серверное ПО на вашей локальной машине. 

Больше не стоит беспокоиться о том что вы можете испортить свою операционную систему! Боксы Vagrant полностью одноразовые. Если что-то пойдет не так, вы сможете уничтожить и пересоздать бокс за считанные минуты!

Homestead запускается на любых системах Windows, Mac и Linux, и включает веб-сервер Nginx, PHP 7.1, MySQL, Postgres, Redis, Memcached, Node и другие вкусности, которые могут потребоваться вам для разработки потрясающих приложений на Laravel.

> {Примечание} Если вы используете Windows, Вам может потребоваться включить аппаратную виртуализацию (VT-x). Это обычно включается через ваш BIOS. Если вы используете Hyper-V на UEFI системе, вам возможно потребуется дополнительно отключить Hyper-V для доступа к VT-x.

<a name="included-software"></a>
### Включённый софт

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

Для использования провайдера VMware, вам потребуется приобрести как VMware Fusion / Workstation, так и [VMware Vagrant plug-in](https://www.vagrantup.com/vmware). Хотя это не бесплатно, VMware может предоставить быструю производительность для совместного доступа к папкам с коробки.

Для использования провайдера Parallels, вам потребуется установить [Parallels Vagrant plug-in](https://github.com/Parallels/vagrant-parallels). Это бесплатно.

#### Установка бокса Homestead Vagrant

После установки VirtualBox / VMware и Vagrant, вам следует добавить бокс `laravel/homestead` в Vagrant, используя следующую команду в терминале. Процесс загрузки бокса займет какое-то время, в зависимости от скорости вашего интернет-соединения:

```bash
vagrant box add laravel/homestead
```

Если эта команда не сработает, убедитесь в том, что у вас установлена последняя версия Vagrant.

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
### Настройка Homestead

#### Настройка вашего провайдера

Ключ `provider` в вашем файле `~/.homestead/Homestead.yaml` указывает какой Vagrant провайдер необходимо использовать: `virtualbox`, `vmware_fusion`, `vmware_workstation`, или `parallels`. Здесь вы можете установить какой провайдер вы будете использовать:

```yaml
provider: virtualbox
```

#### Конфигурирование общих папок

В свойстве `folders` файла `Homestead.yaml` указанны все папки, доступ к которым вы хотите предоставить в среде Homestead. Как только файлы будут изменены, то они тут же будут синхронизироваться между локальной машиной и средой Homestead. Настроить можно столько папок, сколько необходимо:

```yaml
folders:
    - map: ~/Code
      to: /home/vagrant/Code
```

Для включения [NFS](https://www.vagrantup.com/docs/synced-folders/nfs.html), просто добавьте флаг к конфигурации синхронизированных папок:

```yaml
folders:
    - map: ~/Code
      to: /home/vagrant/Code
      type: "nfs"
```

Вы также можете передать любые опции, поддерживаемые Vagrant [Synced Folders](https://www.vagrantup.com/docs/synced-folders/basic_usage.html) путём их перечисления в ключе `options`:

```yaml
folders:
    - map: ~/Code
      to: /home/vagrant/Code
      type: "rsync"
      options:
          rsync__args: ["--verbose", "--archive", "--delete", "-zz"]
          rsync__exclude: ["node_modules"]
```


#### Конфигурирование Nginx сайтов

Не знакомы с Nginx? Нет проблем. Свойство `sites` позволяет легко сопоставить "домен" с папкой в среде Homestead. Пример конфигурации сайта включен в файл `Homestead.yaml`. Опять же, вы можете добавить столько сайтов в свою среду Homestead, сколько необходимо. Homestead может служить удобным, виртуализированным окружением для каждого проекта Laravel, над которым вы работаете:

```yaml
sites:
    - map: homestead.app
      to: /home/vagrant/Code/Laravel/public
```

Если вы измените свойство `sites` после инициализации Homestead бокса, вы должны перезапустить `vagrant reload --provision` для обновления конфигурации Nginx на виртуальной машине.

#### Файл hosts

Вы должны добавить "домены" для ваших Nginx сайтов в файле `hosts` на вашей машине. Файл `hosts` будет делать редирект запросов для ваших Homestead сайтов в вашу Homestead машину. На Mac и Linux, этот файл находится в `/etc/hosts`. На Windows, он находится в `C:\Windows\System32\drivers\etc\hosts`. Строки, добавленные в этот файл, будут выглядеть следующим образом:

```
192.168.10.10  homestead.app
```

Убедитесь что данный IP адрес указанный в вашем файле `~/.homestead/Homestead.yaml`. После того как вы добавили домен в свой файл `hosts` и запустили Vagrant бокс, вы сможете получить доступ к сайту через свой веб-браузер:

```
http://homestead.app
```

<a name="launching-the-vagrant-box"></a>
### Запуск Vagrant Box

После того, как вы отредактировали `Homestead.yaml` по своему вкусу, запустите команду` vagrant up` из своего каталога Homestead. Vagrant загрузит виртуальную машину и автоматически настроит ваши общие папки и сайты Nginx.

Чтобы уничтожить машину, вы можете использовать команду `vagrant destroy --force`.

<a name="per-project-installation"></a>
### Установка для каждого проекта

Вместо того, чтобы устанавливать Homestead глобально и использовать один и тот же Homestead бокс во всех ваших проектах, вы можете настроить экземпляр Homestead для каждого управляемого вами проекта. Установка Homestead для каждого проекта может быть полезна, если вы хотите отправлять `Vagrantfile` с вашим проектом, позволяя другим, работать над проектом, просто используйте `vagrant up`.

Для установки Homestead напрямую в ваш проект, требуется использовать Composer:

```
composer require laravel/homestead --dev
```

После того, как Homestead был установлен, используйте команду `make` для генерации ` Vagrantfile` и `Homestead.yaml` файла в корне проекта. Команда `make` автоматически настроит директивы` sites` и `folders` в файле` Homestead.yaml`.

Mac / Linux:

```
php vendor/bin/homestead make
```

Windows:

```
vendor\\bin\\homestead make
```

Затем запустите команду `vagrant up` в вашем терминале и получите доступ к вашему проекту через `http://homestead.app` в вашем браузере. Помните, вам все равно следует добавить запись в файл `/etc/hosts` для `homestead.app` или домена по вашему выбору.

<a name="installing-mariadb"></a>
### Установка MariaDB

Если вы предпочитаете использовать MariaDB вместо MySQL, вы можете добавить опцию `mariadb` в ваш файл `Homestead.yaml`. Эта опция удалит MySQL и установит MariaDB. MariaDB является заменой MySQL, но вы все равно должны использовать драйвер базы данных `mysql` в конфигурации базы данных вашего приложения:

```
box: laravel/homestead
ip: "192.168.20.20"
memory: 2048
cpus: 4
provider: virtualbox
mariadb: true
```

<a name="daily-usage"></a>
## Ежедневное использование

<a name="accessing-homestead-globally"></a>
### Доступ к Homestead Глобально

Иногда вам может понадобиться использовать `vagrant up` вашей Homestead машины с любого места вашей файловой системы. Вы можете сделать это в системах Mac / Linux, добавив функцию Bash в ваш профиль Bash. В Windows вы можете сделать это, добавив файл "batch" в ваш `PATH`. Эти скрипты позволят вам запустить любую команду Vagrant с любого места вашей системы и автоматически будут направлять эту команду в место с установленным Homestead:

#### Linux

```
function homestead() {
    ( cd ~/Homestead && vagrant $* )
}
```

Убедитесь, что путь `~ / Homestead` в функции настроен к месту фактической установки Homestead. Как только функция установлена, вы можете запускать команды, например `homestead up` или `homestead ssh` из любой точки вашей системы.

#### Windows

Создайте пакетный (batch) файл `homestead.bat` в любом месте на вашем компьютере со следующим содержимым:

```
@echo off

set cwd=%cd%
set homesteadVagrant=C:\Homestead

cd /d %homesteadVagrant% && vagrant %*
cd /d %cwd%

set cwd=
set homesteadVagrant=
```

Убедитесь, что в данном примере путь `C:\Homestead` в скрипте был изменен на фактическое местоположение с установленным Homestead. После создания файла, добавьте его в ваш `PATH`. Затем вы можете запускать команды, такие как `homestead up` или` homestead ssh` из любой точки вашей системы.

<a name="connecting-via-ssh"></a>
### Подключение через SSH

Вы можете подключаться по SSH к вашей виртуальной машине, используя команду в терминале `vagrant ssh` из вашего каталога Homestead.

Так как вероятней всего, вам часто понадобится подключаться по SSH к вашей машине Homestead, подумайте о том, чтобы добавить данную "функцию", описанную выше, в вашу хостевую машину для быстрого доступа по SSH к Homestead боксу.

<a name="connecting-to-databases"></a>
### Подключение к базам данных

База данных `homestead` настроена для MySQL и Postgres из коробки. Для ещё большего удобства, файл Laravel `.env` настраивает фреймворк для этой базы данных из коробки.

Для подключения к вашей базе MySQL или Postgres из вашей хостевой машины клиентом для баз данных, вам нужно подключаться к `127.0.0.1` и использовать порт `33060` (MySQL) или `54320` (Postgres). Пользователь и пароль для обеих баз данных `homestead` / `secret`.

> {note} Эти нестандартные порты вы должны использовать только для подключения к базам данных из вашей хостевой машины. Вам нужно использовать стандартные порты `3306` и `5432` для вашей конфигурации базы данных Laravel поскольку Laravel запускается в виртуальной машине.

<a name="adding-additional-sites"></a>
### Добавление дополнительных сайтов

Как только ваша среда Homestead будет подготовлена и запущена, вы можете добавить дополнительные сайты Nginx для ваших Laravel приложений. Вы можете запускать столько установок Laravel, сколько пожелаете в одной среде Homestead. Чтобы добавить дополнительный сайт, просто добавьте сайт в ваш файл `~/.homestead/Homestead.yaml`:

```yaml
sites:
    - map: homestead.app
      to: /home/vagrant/Code/Laravel/public
    - map: another.app
      to: /home/vagrant/Code/another/public
```

Если Vagrant автоматически не управляет вашим файлом "hosts", вам может потребоваться добавить новый сайт в этот файл:

```
192.168.10.10  homestead.app
192.168.10.10  another.app
```

После того, как сайт был добавлен, запустите команду `vagrant reload --provision` из вашей директории Homestead.

<a name="configuring-cron-schedules"></a>
### Настройка расписания cron

Laravel предоставляет удобный способ [планировки задач Cron](/docs/{{version}}/scheduling) с помощью одной команды Artisan `schedule:run`, настроенной на выполнение каждую минуту. Команда `schedule:run` изучит определённое в классе `App\Console\Kernel` расписание задач, чтобы определить, какие задачи следует запустить.

Если вы хотите настроить выполнение команды `schedule:run` на сайте в Homestead, то, при определении сайте, вам следует установить параметр `schedule` со значением `true`:

    sites:
        - map: homestead.app
          to: /home/vagrant/Code/Laravel/public
          schedule: true

Задача cron для этого сайта будет размещена в папке `/etc/cron.d` в вашей виртуальной машине.

<a name="ports"></a>
### Порты

По умолчанию следующие порты перенаправляются в вашу среду Homestead:

- **SSH:** 2222 &rarr; перенаправляется на 22
- **HTTP:** 8000 &rarr; перенаправляется на 80
- **HTTPS:** 44300 &rarr; перенаправляется на 443
- **MySQL:** 33060 &rarr; перенаправляется на 3306
- **Postgres:** 54320 &rarr; перенаправляется на 5432

#### Перенаправление дополнительных портов

При необходимости вы можете перенаправить на вашу Vagrant-машину дополнительные порты, а также указать их протоколы:

    ports:
        - send: 93000
          to: 9300
        - send: 7777
          to: 777
          protocol: udp

<a name="network-interfaces"></a>
## Сетевые интерфейсы

Свойство `networks` в файле `Homestead.yaml` настраивает сетевые интерфейсы в вашей среде Homestead. Вы можете настроить столько интерфейсов, сколько вам необходимо:

    networks:
        - type: "private_network"
          ip: "192.168.10.20"

Для включения [сетевого моста](https://www.vagrantup.com/docs/networking/public_network.html), укажите параметр `bridge` и измените тип сети на `public_network`:

    networks:
        - type: "public_network"
          ip: "192.168.10.20"
          bridge: "en1: Wi-Fi (AirPort)"

Для включения [DHCP](https://www.vagrantup.com/docs/networking/public_network.html), просто удалите параметр `ip` из вашей конфигурации:

    networks:
        - type: "public_network"
          bridge: "en1: Wi-Fi (AirPort)"

<a name="updating-homestead"></a>
## Обновление Homestead

Вы можете обновить Homestead в два простых шага. Сначала вам следует обновить Vagrant box с помощью команды `vagrant box update`:

    vagrant box update

Далее, вам следует обновить исходный код Homestead. Если вы склонировали репозиторий, то в его папке вы можете просто запустить команду `git pull origin master`.

Если вы установили Homestead с помощью файла проекта `composer.json`, то вам следует убедиться в том, что в файле `composer.json` расположена строка `"laravel/homestead": "^4"` и после этого обновить зависимости:

    composer update

<a name="old-versions"></a>
## Старые версии

Вы можете легко изменить версию Vagrant box, который использует Homestead, добавив следующую строку в ваш файл `Homestead.yaml`:

    version: 0.6.0

Пример:

    box: laravel/homestead
    version: 0.6.0
    ip: "192.168.20.20"
    memory: 2048
    cpus: 4
    provider: virtualbox

При использовании старой версии Homestead box вам необходимо убедиться в совпадении её версии с версией исходного кода Homestead. Ниже расположена таблица, показывающая поддерживаемые версии Homestead box, какую версию исходного кода стоит использовать, а также предоставляемую версию PHP:

Below is a chart which shows the supported box versions, which version of Homestead source code to use, and the version of PHP provided:

|   | Версия Homestead | Версия Box |
|---|---|---|
| PHP 7.0 | 3.1.0 | 0.6.0 |
| PHP 7.1 | 4.0.0 | 1.0.0 |
