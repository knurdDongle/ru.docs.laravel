# Участие в разработке

- [Баг-репорты](#bug-reports)
- [Обсуждение разработки](#core-development-discussion)
- [Какая ветка?](#which-branch)
- [Уязвимости безопасности](#security-vulnerabilities)
- [Стиль написания кода](#coding-style)
    - [PHPDoc](#phpdoc)
    - [StyleCI](#styleci)

<a name="bug-reports"></a>
## Баг-репорты

В целях активного развития фреймворка, Laravel настоятельно рекомендует делать пулл-реквесты, а не только баг-репорты. Баг-репорты могут быть отправлены в форме пулл-реквеста, содержащего в себе ошибку прохождения юнит-тестов.

Однако, если вы отправите баг-репорт, он должен состоять из заголовка и чёткого описания проблемы. Вам также следует включить как можно больше значимой информации и примеров кода, которые отражают проблему. Цель баг-репорта состоит в упрощении локализации и исправления проблемы.

Также помните, что отчёты об ошибках создаются в надежде, что другие пользователи с такой же проблемой смогут принять участие в её решении вместе с вами. Но не ждите, что сразу появится какая-либо активность над баг-репортом или другие побегут исправлять вашу проблему. Отчёт об ошибке призван помочь вам и другим начать совместную работу над решением проблемы.

Исходный код Laravel находится на GitHub, список репозиториев для каждого проекта Laravel:

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

Вы можете предложить добавить новый функционал или улучшить существующий на [доске проблем](https://github.com/laravel/internals/issues) в Laravel Internals. Если вы предлагаете новый функционал, пожалуйста, будьте готовы написать по крайней мере некоторый код, который потребуется для завершения реализации функционала.

Неформальное обсуждение ошибок, новых и существующих возможностей проходит в канале `#internals` в Slack-команде [LaraChat](https://larachat.co). Тейлор Отвелл, главный разработчик, обычно находится в канале по будням с 17:00 до 02:00 вечера (GMT+03:00, время московское), и иногда появляется в другое время.

<a name="which-branch"></a>
## Какая ветка?

**Все** баг-репорты должны отправляться в последнюю стабильную ветку или в текущую LTS-ветку (5.1). Баг-репорты **никогда** не должны отправляться в ветку `master` кроме случаев, когда они затрагивают функционал, который существует только в следующем релизе.

**Мелкие** изменения, которые обладают **полной обратной совместимостью** с актуальным релизом Laravel, могут быть отправлены в последнюю стабильную ветку.

**Крупные** изменения в функционале всегда должны отправляться в ветку `master`, в которой находится следующий релиз Laravel.

Если вы не знаете как квалифицировать ваше изменение, пожалуйста, спросите у Тейлора Отвелла в канале `#internals` в Slack-команде [LaraChat](https://larachat.co).

<a name="security-vulnerabilities"></a>
## Уязвимости безопасности

Если вы обнаружили уязвимость в безопасности Laravel, пожалуйста, отправьте email-письмо Тейлору Отвеллу на <a href="mailto:taylor@laravel.com">taylor@laravel.com</a>. Все подобные уязвимости будут оперативно решены.

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

Не беспокойтесь, если стилистика вашего кода не безупречна! [StyleCI](https://styleci.io/) автоматически применит стилистические правки после мержа пулл-реквеста в репозиторий Laravel. Это позволяет нам сосредоточиться на самом коде, а не его стиле.
