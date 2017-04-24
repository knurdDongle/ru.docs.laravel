# Руководство по обновлению

- [Обновление с  5.3 до 5.4.0](#upgrade-5.4.0)

<a name="upgrade-5.4.0"></a>
## Обновление с  5.3 до 5.4.0

#### Примерное время обновления: 1-2 часа 

> {note} Мы стараемся задокументировать все изменения, которые могут вызывать проблемы. Однако так как некоторые изменения фреймворка находятся в малоизвестных областях, только некоторые из них могут действительно повлиять на работу вашего приложения.

### Обновление зависимостей

В вашем файле `composer.json` обновите зависимость `laravel/framework` до `5.4.*`. Кроме того, необходимо обновить зависимость `phpunit/phpunit` до `~5.0`.

#### Удаление файла скомпилированных сервисов

Вы можете удалить файл `bootstrap/cache/compiled.php`, если он существует. Он больше не используется фреймворком.

#### Очистка кэша

После обновления всех пакетов вам следует выполнить команду `php artisan view:clear`, чтобы избежать ошибок Blade, связанных с удалением `Illuminate\View\Factory::getFirstLoop()`. Также вам может потребоваться выполнить `php artisan route:clear` для очистки кэша роутов.

#### Laravel Cashier

Laravel Cashier уже совместим с Laravel 5.4.

#### Laravel Passport

Вышел Laravel Passport `2.0.0`, предоставляющий совместимость с Laravel 5.4 и JavaScript-библиотекой [Axios](https://github.com/mzabriskie/axios). Если вы обновляете с Laravel 5.3 и используете предварительно созданные компоненты Vue Passport, стоит убедиться в том, что библиотека Axios глобально доступна вашему приложению через кючевое слово `axios`.

#### Laravel Scout

Вышел Laravel Scout `3.0.0`, предоставляющий совместимость с Laravel 5.4.

#### Laravel Socialite

Вышел Laravel Socialite `3.0.0`, предоставляющий совместимость с Laravel 5.4.

#### Laravel Tinker

Чтобы продолжить использовать Artisan команду `tinker`, вам следует установить пакет `laravel/tinker`:

    composer require laravel/tinker

Как только пакет будет установлен, необходимо добавить `Laravel\Tinker\TinkerServiceProvider::class` в массив `providers` в конфигурационном файле `config/app.php`.

#### Guzzle

Laravel 5.4 требует Guzzle 6.0 или выше.

### Авторизация

#### Метод `getPolicyFor`

Раньше, вызывая метод `Gate::getPolicyFor($class)`, возникало исключение в случае, если политика не была найдена. Теперь метод возвращает `null`, если политика для данного класса не будет найдена. Если вы вызываете этот метод напрямую, убедитесь в том, что в вашем коде происходит проверка на возвращаемое значение `null`:

```php
$policy = Gate::getPolicyFor($class);

if ($policy) {
    // code that was previously in the try block
} else {
    // code that was previously in the catch block
}
```

### Blade

#### Экранирование `@section`

В Laravel 5.4, контент, переданный в секцию, автоматически экранируется:

    @section('title', $content)

Если вы хотите передать в секцию неэкранированный контент, вам необходимо определять секцию с помощью традиционного стиля "длинной формы":

    @section('title')
        {!! $content !!}
    @stop

### Bootstrappers

Если вы вручную переопределяете массив `$bootstrappers` в HTTP-ядре или ядре консоли, то вам следует переименовать `DetectEnvironment` на `LoadEnvironmentVariables`.

### Широковещание

#### Связывание моделей каналов

При определении заполнителя названия канала в Laravel 5.3 используется символ '*'. В Laravel 5.4 вам следует определять их с помощью синтаксиса `{foo}`, как маршруты:

    Broadcast::channel('App.User.{userId}', function ($user, $userId) {
        return (int) $user->id === (int) $userId;
    });

### Коллекции

#### Метод `every` 

Функционал метода `every` был перемещён в метод `nth` для соответсвия названию метода, определенному в Lodash.

#### Метод `random`

Вызов `$collection->random(1)` теперь будет возвращать новый экземпляр коллекции с одним элементом. Ранее, это возращало единственный объект. Этот метод будет возвращать единственный объект только в случае, если аргументы не переданы.

### Контейнер

#### Псевдонимы через `bind` / `instance`

В предыдущих версиях Laravel вы могли передать массив в качестве первого аргумента методов `bind` или `instance` для регистрации псевдонима:

    $container->bind(['foo' => FooContract::class], function () {
        return 'foo';
    });

Однако, это поведение было удалено в Laravel 5.4. Для регистрации псевдонима теперь необходимо использовать метод `alias`:

    $container->alias(FooContract::class, 'foo');

#### Связывание классов через косую черту

Связывание классов в контейнер через косую черту больше не поддерживаются. Этот функционал требовал значительного количества вызовов форматирования строки внутри контейнера. Вместо этого просто зарегистрируйте свои классы без косой черты:

    $container->bind('Class\Name', function () {
        //
    });

    $container->bind(ClassName::class, function () {
        //
    });

####  Метод `make` 

Метод контейнера `make` больше не принимает вторым аргументом массив параметров. Этот функционал был признаком некачественного кода. Вы всегда можете построить объект другим путём, что является более интуитивным вариантом.

#### Резолвинг анонимных функций 

Методам контейнера `resolving` и `afterResolving` необходимо передавать название класса или ключ в качестве первого аргумента:

    $container->resolving('Class\Name', function ($instance) {
        //
    });

    $container->afterResolving('Class\Name', function ($instance) {
        //
    });

#### Удален метод `share`

Метод `share` был удален из контейнера. Он считался устаревшим и не упоминался в документации уже несколько лет. Если вы используете его, то в качестве альтернативы вам следует использовать метод `singleton`:

    $container->singleton('foo', function () {
        return 'foo';
    });

### Консоль

#### Трейт `Illuminate\Console\AppNamespaceDetectorTrait`

Если вы напрямую ссылаетесь на трейт `Illuminate\Console\AppNamespaceDetectorTrait`, то внесите изменения в свой код, где будете ссылаться на трейт `Illuminate\Console\DetectsApplicationNamespace`.

### База данных

#### Кастомные соединения

Если ранее вы указывали в контейнере свой ключ для соединения с базой данных как `db.connection.{driver-name}`, теперь необходимо использовать `Illuminate\Database\Connection::resolverFor` в методе `register` вашего `AppServiceProvider`:

    use Illuminate\Database\Connection;

    Connection::resolverFor('driver-name', function ($connection, $database, $prefix, $config) {
        //
    });

#### Режим выбора

Laravel больше не включает возможность настройки PDO в "режиме выбора" в зависимости от ваших конфигурационных файлов. Взамен этого всегда используется  `PDO:: FETCH_OBJ`. Если все еще есть необходимость настроить режим для вашего приложения, можно прослушивать событие `Illuminate\Database\Events\StatementPrepared`:

    Event::listen(StatementPrepared::class, function ($event) {
        $event->statement->setFetchMode(...);
    });

### Eloquent

#### Событие Date

Выброс события `date`,  теперь преобразует столбец в объект  `Carbon` и вызывает метод `startOfDay`. Если вы хотите сохранить время, вы должны использовать `datetime`.

#### Соглашение для внешнего ключа

Если внешний ключ не  задан в явном виде при определении отношений, Eloquent, будет использовать имя таблицы и первичного ключа модели для  создания внешний ключа. Для подавляющего большинства приложений это поведение не изменяется. Например:

    public function user()
    {
        return $this->belongsTo(User::class);
    }

Точно так же, как и в предыдущих версиях Laravel эти отношения будут  использовать `user_id` в качестве внешнего ключа. Однако поведение могло отличаться от предыдущих выпусков, если вы переопределяли метод`getKeyName` модели `User`. Например:

    public function getKeyName()
    {
        return 'key';
    }

Когда дело обстоит так, Laravel будет уважать вашу настройку и решит, что имя столбца внешнего ключа - `user_key` вместо `user_id`.

#### Has One / Many `createMany`

Метод `createMany` имеющий `hasOne` или  `hasMany`  теперь возвращает объект с коллекцией, а не массив.

#### Соединения моделей с отношениями

Отношение моделей теперь будут использовать те же соединения, что и родительская модель, к примеру если выполнить запрос :

    User::on('example')->with('posts');

Eloquent будет запрашивать таблицу posts через соединение `example` вместо соединения базы данных по умолчанию. Если вы хотите прочитать `posts` через соединение по умолчанию, необходимо явно установить соединение модели на подключение вашего приложения.


#### Методы `create` & `forceCreate`

Методы `Model::create` & `Model:: forceCreate` были перемещены в класс `Illuminate\Database\Eloquent\Builder` для обеспечения лучшей поддержки создания моделей для нескольких соединений. Однако, если расширять эти методы в свои собственных моделях, потребуется изменить реализацию, чтобы вызвать метод `create` из строителя. Например:

    public static function create(array $attributes = [])
    {
        $model = static::query()->create($attributes);

        // ...

        return $model;
    }

#### Метод `hydrate`

Если вы передаете имя соединения, необходимо использовать метод `on`:

    User::on('connection')->hydrate($records);

#### Метод `hydrateRaw` 

Метод `Model::hydrateRaw` был переименован в `fromQuery`. Eсли вы передаете имя настраиваемого подключения  к этому методу, нужно использовать  `on`:


    User::on('connection')->fromQuery('...');

#### Метод `whereKey`

Метод `whereKey($id)`  сейчас добавляет условие "where" для получения первичного ключа. Ранее, это передавалось в условие "where" и внутри него еще добавлялось "where" для столбца "key". Если вы используете метод `whereKey`, чтобы динамически добавлять условие для столбцов `key`, вместо этого необходимо использовать `where('key', ...)`.

#### Хелпер `factory`

Вызов `factory(User::class, 1)->make()` или `factory(User::class, 1)->create()`, возвращает коллекцию с одним элементом. Метод будет возвращает одну модель, если число не передается в параметры.

### События

#### Изменения в Contract 

Если вы самостоятельно  реализуете контракт  `Illuminate\Contracts\Events\Dispatcher` в своем  приложение или пакете, нужно переименовать метод  `fire` в  метод  `dispatch`.

#### Приоритет событий

Поддержка  события  "priorities"  была удалена. Это возможность, не описывалась в документации и обычно указывает на злоупотребление функцией события. Взамен рассмотрите использование серий синхронных вызовов. 

#### Сигнатура обработчиков событий

Обработчики событий теперь получают в качестве первого аргумента имя события  и массив с  данными  в качестве второго аргумента.Метод `Event::firing` был удален :

    Event::listen('*', function ($eventName, array $data) {
        //
    });

#### Событие `kernel.handled`

Событие `kernel.handled` сейчас объект на основе событий, используя класс `Illuminate\Foundation\Http\Events\RequestHandled`.

#### Событие `locale.changed`

Событие `locale.changed` сейчас объект на основе, используя класс `Illuminate\Foundation\Events\LocaleUpdated`.

#### Событие `illuminate.log` 

Событие `illuminate.log` сейчас объект на основе используя класс  `Illuminate\Log\Events\MessageLogged`.

### Исключения

`Illuminate\Http\Exception\HttpResponseException` был переименован 
`Illuminate\Http\Exceptions\HttpResponseException`. Отметим, что `Exceptions` теперь во множественном числе. кроме того, `Illuminate\Http\Exception\PostTooLargeException` был переименован в `Illuminate\Http\Exceptions\PostTooLargeException`.

### Почта

#### Синтаксис `Class@method`

Отправка почты с помощью `Class@method`  уже долгое время не поддерживается. Например:

    Mail::send('view.name', $data, 'Class@send');

Если вы отправляете почту таким образом, нужно конвертировать эти вызовы [mailables](/docs/{{version}}/mail).

#### Новые конфигурации

Для того, чтобы обеспечить поддержку новых компонентов с разметкой Markdown в Laravel 5.4 необходимо добавить следующий блок в нижней части файла конфигурации `mail`.

    'markdown' => [
        'theme' => 'default',

        'paths' => [
            resource_path('views/vendor/mail'),
        ],
    ],

#### Queueing Mail With Closures

Для организации почтовой очереди используйте  [mailable](/docs/{{version}}/mail). Методы `Mail :: queue` и` Mail :: later`  больше не поддерживают использование анонимных функций для настройки почтового сообщения. Эта функция требует использования специальных библиотек для закрытия, поскольку PHP не поддерживает эту функцию.

### Redis

#### Улучшена поддержка кластеризации.

Что Laravel 5.4  улучшена поддержка кластера Redis.

Если вы используете кластеры для Redis, нужно разместить свои соединения в блоке  `clusters` внутри файла конфигурации  `config/database.php`:

    'redis' => [

        'client' => 'predis',

        'options' => [
            'cluster' => 'redis',
        ],

        'clusters' => [
            'default' => [
                [
                    'host' => env('REDIS_HOST', '127.0.0.1'),
                    'password' => env('REDIS_PASSWORD', null),
                    'port' => env('REDIS_PORT', 6379),
                    'database' => 0,
                ],
            ],
        ],

    ],

### Роутинг

#### Middleware

Класс `Illuminate\Foundation\Http\Middleware\VerifyPostSize` был переименован в  `Illuminate\Foundation\Http\Middleware\ValidatePostSize`.

#### Метод `middleware`

Метод `middleware` в классе `Illuminate\Routing\Router` был переименован в `aliasMiddleware()`. Вероятно большинство приложений не вызывает этот метод вручную, 
как правило, это происходит  через  HTTP kernel в массиве `$routeMiddleware`.

#### Метод `Route` 

Метод `getUri` из класса `Illuminate\Routing\Route` был удален. Вместо него необходимо использовать метод `uri`.

Метод `getMethods`  из класса `Illuminate\Routing\Route` был удален. Вместо него необходимо использовать метод `methods`.

TheМетод`getParameter`  из класса `Illuminate\Routing\Route` был удален. Вместо него необходимо использовать метод `parameter`.

### Сессия

#### Symfony совместимость

Обработчики сессий Laravel больше не реализуют Symfony интерфейс `SessionInterface`. Реализация этого интерфейса требует посторонних опций, которые не были необходимы во фреймворке. Взамен этого, был определен новый интерфейс  `Illuminate\Contracts\Session\Session`. Следующие изменения кода должны быть также быть применены:

Все вызовы метода `->set()` должны быть изменены на `->put()`. Обычно, в приложениях Laravel  метод `set` не используются, поскольку это не было описано в документации Laravel. Тем не менее, это указано из за осторожности.

Все вызовы `->getToken()` следует заменить на  `->token()`.
 
Все вызовы `$request->setSession()` следует заменить на  `setLaravelSession()`.

### Тестирование

В Laravel 5.4 слой отвечающий за тестирования  был переписан, теперь он стал проще и легче из коробки.

Если вы хотите продолжать использовать функционал тестирования, который остался в версии Laravel 5.3 можно установить `laravel/browser-kit-testing` [пакет](https://github.com/laravel/browser-kit-testing) в ваше приложение. Он обеспечивает полную совместимость с версией Laravel 5.3. В действительности, вы можете запустить тестирование Laravel 5.4  бок-о-бок со средой тестирования из версии Laravel 5.3.

####  Запуск тестов в  Laravel 5.3 & 5.4  в одном приложении

Перед началом работы, вы должны добавить пространство имен `Tests` в файл `composer.json` в блок `autoload-dev`. Это позволит фреймворку загружать любые новые тесты, которые вы создаете с использованием тестовых генераторов из Laravel 5.4:

    "psr-4": {
        "Tests\\": "tests/"
    }

Далее установите пакет `laravel/browser-kit-testing` :

    composer require laravel/browser-kit-testing

После того, как пакет был установлен, создайте копию файла `tests/TestCase.php` и сохраните ее в директорию  `tests` как файл `BrowserKitTestCase.php`. Затем, внесите изменения в него, расширяя `Laravel\BrowserKitTesting\TestCase`. Как только это сделано, в вашей директории `tests` будет находиться два базовых класса для тестирования : `TestCase.php` и `BrowserKitTestCase.php`. Для правильной загрузки класса `BrowserKitTestCase` , возможно понадобится его добавить в `composer.json`:

    "autoload-dev": {
        "classmap": [
            "tests/TestCase.php",
            "tests/BrowserKitTestCase.php"
        ]
    },

Тесты, написанные на версии Laravel 5.3 будут наследовать класс `BrowserKitTestCase` , в то время как любые новые тесты, которые используют Laravel 5.4 будут наследовать класс `TestCase`. Класс `BrowserKitTestCase` выглядит следующим образом:

    <?php

    use Illuminate\Contracts\Console\Kernel;
    use Laravel\BrowserKitTesting\TestCase as BaseTestCase;

    abstract class BrowserKitTestCase extends BaseTestCase
    {
        /**
         * The base URL of the application.
         *
         * @var string
         */
        public $baseUrl = 'http://localhost';

        /**
         * Creates the application.
         *
         * @return \Illuminate\Foundation\Application
         */
        public function createApplication()
        {
            $app = require __DIR__.'/../bootstrap/app.php';

            $app->make(Kernel::class)->bootstrap();

            return $app;
        }
    }
После того, как класс создан, не забудьте обновить все тесты, расширяя их новым классом `BrowserKitTestCase`. Это позволит всем тестам, написанным на Laravel 5.3,  успешно продолжить работу на Laravel 5.4. После можно заняться рефакторингом с использованием  [нового синтаксиса Laravel 5.4](/docs/5.4/http-tests) или [Laravel Dusk](/docs/5.4/dusk).

> {note} Если вы пишете новые тесты и используете тестирование из  Laravel 5.4, убедитесь в том, что расширяете класс `TestCase`.

#### Установка Dusk при обновлении

Если вы хотите установить Laravel Dusk в приложении, который обновлен до версии Laravel 5.3, сначала установите его через Composer

    composer require laravel/dusk

Далее, нужно создать трейт  `CreatesApplication` в директории `tests`. Этот трейт отвечает за создание новых экземпляров для тестов. Он должен выглядеть следующим образом :

    <?php

    use Illuminate\Contracts\Console\Kernel;

    trait CreatesApplication
    {
        /**
         * Creates the application.
         *
         * @return \Illuminate\Foundation\Application
         */
        public function createApplication()
        {
            $app = require __DIR__.'/../bootstrap/app.php';

            $app->make(Kernel::class)->bootstrap();

            return $app;
        }
    }

> {note} Если вы используете пространство имен по стандарту PSR-4 для загрузки каталога 'тесты', необходимо поместить трейт `CreatesApplication` в соответствующее пространство имен.

После завершения этого подготовительного этапа, можно пройти обычную  [инструкцию по установке Dusk](/docs/{{version}}/dusk#installation).

#### Окружение

Тестовый класс  Laravel 5.4  уже не вручную  использует `putenv('APP_ENV=testing')` для каждого теста. Вместо этого, фреймворк использует переменную `APPENV` в  файле  `.env` `.

#### Фейковые события

Фейковые `события` в методе `assertFired` обновлены до `assertDispatched`, и `assertNotFired` метод  обновлен до `assertNotDispatched`. Характеристики  не изменились.

#### Фейковая почта

Фейковая `почта` была значительно упрощена для версии  Laravel 5.4. Вместо использования  метода `assertSentTo` , теперь необходимо просто использовать  `assertSent`, `hasTo`,`hasCc` и т. д. 

    Mail::assertSent(MailableName::class, function ($mailable) {
        return $mailable->hasTo('email@example.com');
    });

### Перевод

#### Шаблон `{Inf}` 

Если вы используете шаблон  `{Inf}`  для заполнения строк с переводом, нужно обновить эти строки, используя вместо этого знак `*` 

    {0} First Message|{1,*} Second Message

### Генерация URL

#### Метод  `forceSchema`

Mетод `forceSchema` из класса  `IlluminateRoutingUrlGenerator` был переименован в `forceScheme`.

### Валидация

#### Валидация формата даты

Проверка формата даты стала строже, так же поддерживает заполнители из документации по РНР [date function](http://php.net/manual/en/function.date.php). К сведению, в  предыдущих версиях Laravel часовой пояс с заполнителем `P` принимал все форматы , однако, в  Laravel 5.4 каждый пояс имеет уникальный заполнитель согласно документации PHP.

#### Метод Names

Метод `AddError` был переименован к `addFailure`. Кроме того, метод `doReplacements` был переименован в  `makeReplacements`. Конечно, эти изменения существенны, если вы расширяете класс `Validator`.

### Остальное

Так же вы можете просматривать изменения в `laravel/laravel` [GitHub репозитории](https://github.com/laravel/laravel). Пока эти изменения не требуются, можно сохранить эти файлы в вашем приложении. Некоторые из этих изменений будут также описаны этим руководством по обновлению. Однако, другие, такие как изменения в конфигурационных файлах или комментариях, описаны не будут. Есть возможность  легко отследить изменения через  [инструмент сравнения Github](https://github.com/laravel/laravel/compare/5.3...master) и выбрать, какие обновления для вас наиболее важны.
