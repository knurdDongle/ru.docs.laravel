# Шаблоны (Views)

- [Создание шаблонов](#creating-views)
- [Передача данных в шаблон](#passing-data-to-views)
    - [Передача данных между шаблонами](#sharing-data-with-all-views)
- [Композеры](#view-composers)

<a name="creating-views"></a>
## Создание шаблонов

Внутри шаблонов содержится HTML разметка, предназначенная для удобства работы в приложении. С ее помощью можно отделять бизнес-логику от представления. Views расположены в директории `resources/views`. Итак, давайте рассмотрим, как выглядит простой шаблон:

    <!-- View stored in resources/views/greeting.blade.php -->

    <html>
        <body>
            <h1>Привет, {{ $name }}</h1>
        </body>
    </html>

Так как этот шаблон хранится в `resources/views/greeting.blade.php`, можно использовать helper `view` и вернуть его из контроллера следующим образом:

    Route::get('/', function () {
        return view('greeting', ['name' => 'Иван']);
    });

Как вы видите, первый параметр в helper `view` соответствует названию файла в папке `resources/views`. Вторым аргументом передается массив с данными, который доступен в представлении. В этом случае мы передаем переменную `name`, которая отображается в шаблоне с помощью [синтаксиса Blade](/docs/{{version}}/blade).

Конечно, шаблоны могут быть вложены в поддиректории, в директории `resources/views`. Для ссылки на вложенные шаблоны используем нотацию "точка". Для примера, если шаблон хранится внутри: `resources/views/admin/profile.blade.php`, вы можете сослаться на него следующим образом :

    return view('admin.profile', $data);

#### Определение наличия шаблона

Если нужно определить существует ли ваше представление, вы можете использовать фасад `View`. Метод `exists`  вернёт `true`, если существующий шаблон:

    use Illuminate\Support\Facades\View;

    if (View::exists('emails.customer')) {
        //
    }

<a name="passing-data-to-views"></a>
## Передача данных в шаблон

Как вы видели в предыдущих примерах, можно передать массив с данными в  представление:

    return view('greetings', ['name' => 'Виктория']);

Когда мы передаем информацию таким способом, `$data` должен быть массивом с парой ключ/значение. Внутри шаблона, можно получить доступ к каждому значению, используя соответствующий ключ, такой как: `<?php echo $key; ?>`. В качестве альтернативы передачи полного массива в функцию helper `view`, можно использовать метод `with` для добавления данных в представление:

    return view('greeting')->with('name', 'Виктория');

<a name="sharing-data-with-all-views"></a>
#### Передача данных во все шаблоны

Время от времени придется поделиться частью своих данных со всеми представлениями, которые находятся в вашем приложении. Можно реализовать это, используя метод фасада `share`. Как правило, нужно вызывать метод `share` внутри метода `boot` сервис-провайдеров. Его легко можно добавить в `AppServiceProvider` или создать отдельные провайдеры для этого:

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
## Композеры

Композеры - это callbacks или методы класса, которые вызываются при рендере вашего шаблона. Если есть данные, которые хотите связать с шаблоном при каждом его отображении, то композеры могут помочь организовать эту логику в одном месте.

Для примера, давайте зарегистрируем композер внутри [сервис провайдера](/docs/{{version}}/providers). Мы будем использовать фасад `View` для доступа к реализации основного контракта `Illuminate\Contracts\View\Factory`. Помните, в Laravel по умолчанию нет папки для композеров, поэтому вы можете сами создать папку. Для примера, мы можем создать папку `App\Http\ViewComposers`

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

> {note}    Помните, если вы создали сервис провайдер, внутри которого зарегистрирован композер, необходимо добавить его в массив `providers` внутри конфигурационного файла `config/app.php`.

Теперь, когда мы зарегистрировали композер, метод `ProfileComposer@compose` будет вызываться каждый раз, когда `profile` будет рендериться. Давайте определим класс композера:

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
Перед тем как ваш шаблон начнет рендериться, композер вызовет метод `compose` с экземпляром `Illuminate\View\View`. Вы можете использовать метод  `with` для привязки данных к представлению.

> {tip} Все композеры подключаются через [сервис контейнер](/docs/{{version}}/container), поэтому можно применить любую инъекцию зависимостей внутри конструктора.

#### Назначение композера для нескольких шаблонов

Вы можете прикрепить к композеру несколько представлений одновременно, указав массив шаблонов в первом аргументе метода `composer`:

    View::composer(
        ['profile', 'dashboard'],
        'App\Http\ViewComposers\MyViewComposer'
    );

Метод `composer` так же принимает специальный символ `*`, что позволяет назначить его для всех представлений:

    View::composer('*', function ($view) {
        //
    });

#### Создатель шаблонов
Создатели очень похожи на композеры, за тем исключением, что выполняются сразу после создания шаблона и не дожидаются его рендера. Для того, чтобы зарегистрировать создателя, используйте метод `creator`:

    View::creator('profile', 'App\Http\ViewCreators\ProfileCreator');
