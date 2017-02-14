# Локализация

- [Введение](#introduction)
- [Определение строк перевода](#defining-translation-strings)
    - [Использование кратких ключей](#using-short-keys)
    - [Использование строк перевода как ключей](#using-translation-strings-as-keys)
- [Получение строк перевода](#retrieving-translation-strings)
    - [Подстановка параметров в строках перевода](#replacing-parameters-in-translation-strings)
    - [Плюрализация](#pluralization)
- [Переопределение языковых файлов пакета](#overriding-package-language-files)

<a name="introduction"></a>
## Введение

Возможности локализации Laravel предоставляют удобный способ получения строк на нескольких языках, что позволяет легко организовать поддержку мультиязычности вашим приложением. Языковые строки хранятся в файлах, располагающихся внутри директории `resources/lang`. В ней должны находиться подпапки для каждого языка, поддерживаемого вашим приложением:

    /resources
        /lang
            /en
                messages.php
            /es
                messages.php

Все языковые файлы возвращают простой массив ключей и строк. Например:

    <?php

    return [
        'welcome' => 'Welcome to our application'
    ];

### Настройка локали

Стандартный язык вашего приложения указан в конфигурационном файле `config/app.php`. Конечно же, вы можете изменить это значение на основе потребностей вашего приложения. Вы также можете изменить активный язык в процессе работы приложения с помощью метода `setLocale` фасада `App`:

    Route::get('welcome/{locale}', function ($locale) {
        App::setLocale($locale);

        //
    });

Вы можете настроить "запасной язык", который будет использоваться в случаях, когда активный язык не содержит заданной строки перевода. Как и стандартный язык, запасной язык также настраивается в конфигурационном файле `config/app.php`:

    'fallback_locale' => 'en',

#### Определение текущей локали

Вы можете использовать методы `getLocale` и `isLocale` фасада `App` для определения текущей локали или проверки совпадения локали с заданным значением:

    $locale = App::getLocale();

    if (App::isLocale('en')) {
        //
    }

<a name="defining-translation-strings"></a>
## Определение строк перевода

<a name="using-short-keys"></a>
### Использование кратких ключей

Обычно строки перевода хранятся в файлах, располагающихся внутри директории `resources/lang`. В ней должны находиться подпапки для каждого языка, поддерживаемого вашим приложением:

    /resources
        /lang
            /en
                messages.php
            /es
                messages.php

Все языковые файлы возвращают простой массив ключей и строк. Например:

    <?php

    // resources/lang/en/messages.php

    return [
        'welcome' => 'Welcome to our application'
    ];

<a name="using-translation-strings-as-keys"></a>
### Использование строк перевода как ключей

Для приложений с большими требованиями к переводу, определение каждой строки с помощью "краткого ключа" может привести к путанице при обращении к ним из ваших представлений. По этой причине, Laravel также предоставляет поддержку определения строк перевода с использованием стандартного перевода в качестве ключа.

Файлы переводов, которые используют строки перевода в качестве ключей, хранятся в JSON-файлах в директории `resources/lang`. Например, если приложение имеет поддержку испанского языка, то вы должны создать файл `resources/lang/es.json`:

    {
        "I love programming.": "Me encanta la programación."
    }

<a name="retrieving-translation-strings"></a>
## Получение строк перевода

Вы можете получать строки из языковых файлов с помощью функции `__`. Она принимает файл и ключ строки перевода в качестве первого параметра. Например, давайте получим строку перевода `welcome` из языкового файла `resources/lang/messages.php`:

    echo __('messages.welcome');

    echo __('I love programming.');

Конечно, если вы используете [шаблонизатор Blade](/docs/{{version}}/blade), то вы можете использовать синтаксис `{{ }}` для отображения строки перевода, или использовать директиву `@lang`:

    {{ __('messages.welcome') }}

    @lang('messages.welcome')

Если запрошенной строки перевода не существует, то функция `__` вернёт ключ строки перевода. Таким образом, при использовании примера выше, функция `__` вернула бы `messages.welcome` при отсутствии строки перевода.

<a name="replacing-parameters-in-translation-strings"></a>
### Подстановка параметров в строках перевода

При желании, вы можете указывать плейсхолдеры в ваших строках перевода. Все плейсхолдеры начинаются с символа `:`. Например, вы можете создать приветственное сообщение с плейсхолдером для имени:

    'welcome' => 'Welcome, :name',

Для замены плейсхолдеров, при получении строки перевода, передайте массив с необходимыми заменами в качестве второго параметра функции `__`:

    echo __('messages.welcome', ['name' => 'dayle']);

Если ваш плейсхолдер содержит только прописные буквы, или только первая его буква является прописной, то переведённое значение будет преобразовано соответствующим образом:

    'welcome' => 'Welcome, :NAME', // Welcome, DAYLE
    'goodbye' => 'Goodbye, :Name', // Goodbye, Dayle


<a name="pluralization"></a>
### Плюрализация

Плюрализация является комплексной проблемой, так как в разных языках существуют различные сложные правила плюрализации. С помощью символа "вертикальной черты" вы можете разграничить одиночную и множественную форму строки:

    'apples' => 'There is one apple|There are many apples',

Вы даже можете создать сложные правила плюрализации, которые определят строки перевода для нескольких диапазонов чисел:

    'apples' => '{0} There are none|[1,19] There are some|[20,*] There are many',

После определения строки перевода с вариантами плюрализации, вы можете использовать функцию `trans_choice` для получения строки для заданного "числа". В данном примере, так как число больше нуля и единицы, была возвращена форма множественного числа этой строки:

    echo trans_choice('messages.apples', 10);

<a name="overriding-package-language-files"></a>
## Overriding Package Language Files

Some packages may ship with their own language files. Instead of changing the package's core files to tweak these lines, you may override them by placing files in the `resources/lang/vendor/{package}/{locale}` directory.

So, for example, if you need to override the English translation strings in `messages.php` for a package named `skyrim/hearthfire`, you should place a language file at: `resources/lang/vendor/hearthfire/en/messages.php`. Within this file, you should only define the translation strings you wish to override. Any translation strings you don't override will still be loaded from the package's original language files.
