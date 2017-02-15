# Представления

- [Создание представлений](#creating-views)
- [Передача данных в представления](#passing-data-to-views)
    - [Передача данных во все представления](#sharing-data-with-all-views)
- [Композеры представлений](#view-composers)

<a name="creating-views"></a>
## Создание представлений

Представления содержат HTML-разметку вашего приложения, и отделяют контроллеры / бизнес-логику от логики отображения данных. Представления расположены в директории `resources/views`. Итак, давайте рассмотрим, как выглядит простое представление:

    <!-- View stored in resources/views/greeting.blade.php -->

    <html>
        <body>
            <h1>Привет, {{ $name }}</h1>
        </body>
    </html>

Так как представление хранится в `resources/views/greeting.blade.php`, мы можем получить его с помощью хелпера `view` следующим образом:

    Route::get('/', function () {
        return view('greeting', ['name' => 'Иван']);
    });

Как видите, первый передаваемый в хелпер `view` аргумент соответствует названию файла представления в папке `resources/views`. Вторым аргументом передается массив с данными, которые будут доступны в представлении. В данном случае мы передаем переменную `name`, которая отображается с помощью [синтаксиса Blade](/docs/{{version}}/blade).

Конечно, представления могут находиться в поддиректориях внутри `resources/views`. Для обращения к ним следует использовать синтаксис "точка". Например, если представление хранится в `resources/views/admin/profile.blade.php`, то вы можете обратиться к нему следующим образом:

    return view('admin.profile', $data);

#### Определение наличия представления

Если нужно определить существует ли представление, вы можете использовать фасад `View`. Метод `exists` вернёт `true`, если оно существует:

    use Illuminate\Support\Facades\View;

    if (View::exists('emails.customer')) {
        //
    }

<a name="passing-data-to-views"></a>
## Передача данных в представления

Как было показано в предыдущих примерах, вы можете передать массив с данными в представление:

    return view('greetings', ['name' => 'Виктория']);

При передаче информации таким способом, `$data` должен быть массивом с парами ключ/значение. Внутри представления вы можете получить доступ к каждому значению, используя соответствующий ключ, такой как: `<?php echo $key; ?>`. В качестве альтернативы передачи массива данных в хелпер-функцие `view`, можно использовать метод `with` для добавления отдельных данных в представление:

    return view('greeting')->with('name', 'Виктория');

<a name="sharing-data-with-all-views"></a>
#### Передача данных во все представления

В некоторых ситуациях придется передать часть данных во все представления, которые используются в вашем приложении. Вы можете сделать это с помощью метод `share` внутри метода `boot` сервис-провайдера. Его легко можно добавить в провайдер `AppServiceProvider`, или создать отдельный провайдер для этого:

    <?php

    namespace App\Providers;

    use Illuminate\Support\Facades\View;

    class AppServiceProvider extends ServiceProvider
    {
        /**
         * Bootstrap any application services.
         *
         * @return void
         */
        public function boot()
        {
            View::share('key', 'value');
        }

        /**
         * Register the service provider.
         *
         * @return void
         */
        public function register()
        {
            //
        }
    }

<a name="view-composers"></a>
## Композеры представлений

Композеры представлений - это функции обратного вызова или методы класса, которые вызываются при рендере вашего шаблона. Если у вас есть данные, которые вы хотели бы отправлять в представление при каждом его отображении, то композеры могут помочь организовать такую логику в одном месте.

Для примера, давайте зарегистрируем композер представления внутри [сервис провайдера](/docs/{{version}}/providers). Мы будем использовать фасад `View` для доступа к основному контракту `Illuminate\Contracts\View\Factory`. По умолчанию в Laravel нет папки для хранения композеров представления. Вы можете организовать его где посчитаете нужным. Например, можете создать папку `App\Http\ViewComposers`:

    <?php

    namespace App\Providers;

    use Illuminate\Support\Facades\View;
    use Illuminate\Support\ServiceProvider;

    class ComposerServiceProvider extends ServiceProvider
    {
        /**
         * Register bindings in the container.
         *
         * @return void
         */
        public function boot()
        {
            // Using class based composers...
            View::composer(
                'profile', 'App\Http\ViewComposers\ProfileComposer'
            );

            // Using Closure based composers...
            View::composer('dashboard', function ($view) {
                //
            });
        }

        /**
         * Register the service provider.
         *
         * @return void
         */
        public function register()
        {
            //
        }
    }

> {note} Помните, если вы создаёте новый сервис-провайдер для хранения ваших компосеров, то также следует добавить его в массив `providers` внутри конфигурационного файла `config/app.php`.

Теперь, когда мы зарегистрировали композер, метод `ProfileComposer@compose` будет вызываться при каждом рендере представления `profile`. Давайте определим класс композера:

    <?php

    namespace App\Http\ViewComposers;

    use Illuminate\View\View;
    use App\Repositories\UserRepository;

    class ProfileComposer
    {
        /**
         * The user repository implementation.
         *
         * @var UserRepository
         */
        protected $users;

        /**
         * Create a new profile composer.
         *
         * @param  UserRepository  $users
         * @return void
         */
        public function __construct(UserRepository $users)
        {
            // Dependencies automatically resolved by service container...
            $this->users = $users;
        }

        /**
         * Bind data to the view.
         *
         * @param  View  $view
         * @return void
         */
        public function compose(View $view)
        {
            $view->with('count', $this->users->count());
        }
    }

Перед началом рендера представления, композер вызовет метод `compose` с экземпляром `Illuminate\View\View`. Вы можете использовать метод `with` для передачи данных в представление.

> {tip} Все композеры подключаются через [сервис контейнер](/docs/{{version}}/container), поэтому можно применить любую инъекцию зависимостей внутри конструктора.

#### Подключение композера к нескольким представлениям

Вы можете подключить к композеру несколько представлений одновременно, передав их в качестве первого аргумента метода `composer`:

    View::composer(
        ['profile', 'dashboard'],
        'App\Http\ViewComposers\MyViewComposer'
    );

Метод `composer` также принимает специальный символ `*`, что позволяет подключить его ко всем представлениям:

    View::composer('*', function ($view) {
        //
    });

#### Создатель представлений
Создатели представлений очень похожи на композеры, однако они выполняются сразу после инициализации представлений, не дожидаясь их рендера. Для регистрации создателя используется метод `creator`:

    View::creator('profile', 'App\Http\ViewCreators\ProfileCreator');
