# Участие в разработке

- [Баг-репорты](#bug-reports)
- [Обсуждение разработки](#core-development-discussion)
- [Определение ветки](#which-branch)
- [Уязвимости безопасности](#security-vulnerabilities)
- [Стиль написания кода](#coding-style)
    - [PHPDoc](#phpdoc)
    - [StyleCI](#styleci)

<a name="bug-reports"></a>
## Баг-репорты

С целью активного развития фреймворка, Laravel настоятельно рекомендует создавать пулл-реквесты, а не только баг-репорты. Баг-репорты могут быть отправлены в форме пулл-реквеста, содержащего в себе ошибку прохождения юнит-тестов.

Помните, что если вы отправляете баг-репорт, он должен содержать заголовок и чёткое описание проблемы. Вам также следует включить как можно больше значимой информации и примеров кода, которые отражают проблему. Цель баг-репорта состоит в упрощении локализации и исправления проблемы.

Также помните, что баг-репорты создаются в надежде, что другие пользователи с такой же проблемой смогут принять участие в её решении вместе с вами. Но не ждите, что сразу появится какая-либо активность над вашим репортом или другие побегут исправлять вашу проблему. Баг-репорт призван помочь вам и другим пользователям начать совместную работу над решением проблемы.

Исходный код Laravel находится на GitHub, список репозиториев каждого из проектов Laravel:

- [Laravel Framework](https://github.com/laravel/framework)
- [Laravel Application](https://github.com/laravel/laravel)
- [Laravel Documentation](https://github.com/laravel/docs)
- [Laravel Cashier](https://github.com/laravel/cashier)
- [Laravel Cashier for Braintree](https://github.com/laravel/cashier-braintree)
- [Laravel Envoy](https://github.com/laravel/envoy)
- [Laravel Homestead](https://github.com/laravel/homestead)
- [Laravel Homestead Build Scripts](https://github.com/laravel/settler)
- [Laravel Passport](https://github.com/laravel/passport)
- [Laravel Scout](https://github.com/laravel/scout)
- [Laravel Socialite](https://github.com/laravel/socialite)
- [Laravel Website](https://github.com/laravel/laravel.com)
- [Laravel Art](https://github.com/laravel/art)

<a name="core-development-discussion"></a>
## Обсуждение разработки

Вы можете предложить новый функционал или улучшение существующего в [обсуждениях](https://github.com/laravel/internals/issues) репозитория Laravel Internals. Если вы предлагаете новый функционал, то, пожалуйста, будьте готовы написать по крайней мере часть кода, который потребуется для завершения реализации функционала.

Неформальное обсуждение ошибок, новых и существующих возможностей проходит в канале `#internals` в Slack-чате [LaraChat](https://larachat.co). Тейлор Отвелл, главный разработчик, обычно находится в канале по будням с 17:00 вечера до 02:00 ночи (время московское, UTC+03:00), и иногда появляется в другое время.

<a name="which-branch"></a>
## Определение ветки

**Все** баг-репорты должны отправляться в последнюю стабильную ветку или в текущую LTS-ветку (5.1). Баг-репорты **никогда** не должны отправляться в ветку `master` кроме случаев, когда они затрагивают функционал, существующий только в следующем релизе.

**Мелкие** изменения, которые обладают **полной обратной совместимостью** с актуальным релизом Laravel, могут отправляться в последнюю стабильную ветку.

**Крупные** изменения в функционале всегда должны отправляться в ветку `master`, в которой находится следующий релиз Laravel.

Если вы не уверены в том, как квалифицировать ваше изменение, пожалуйста, спросите у Тейлора Отвелла в канале `#internals` в Slack-чате [LaraChat](https://larachat.co).

<a name="security-vulnerabilities"></a>
## Уязвимости безопасности

Если вы обнаружили уязвимость в безопасности Laravel, пожалуйста, отправьте email-письмо Тейлору Отвеллу на <a href="mailto:taylor@laravel.com">taylor@laravel.com</a>. Все подобные уязвимости будут оперативно рассмотрены.

<a name="coding-style"></a>
## Стиль написания кода

Laravel придерживается стандарта написания кода [PSR-2](https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-2-coding-style-guide.md) и стандартна автозагрузки классов [PSR-4](https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-4-autoloader.md).

<a name="phpdoc"></a>
### PHPDoc

Ниже расположен пример корректного блока документации кода Laravel. Обратите внимание, что после атрибута `@param` идут два пробела, затем тип аргумента, после ещё два пробела, и всё завершается названием переменной:

    /**
     * Register a binding with the container.
     *
     * @param  string|array  $abstract
     * @param  \Closure|string|null  $concrete
     * @param  bool  $shared
     * @return void
     */
    public function bind($abstract, $concrete = null, $shared = false)
    {
        //
    }

<a name="styleci"></a>
### StyleCI

Не беспокойтесь, если стиль вашего кода не безупречен! [StyleCI](https://styleci.io/) автоматически применит стилистические правки после вливания пулл-реквеста в репозиторий Laravel. Это позволяет нам сосредоточиться на самом коде, а не его стиле.
