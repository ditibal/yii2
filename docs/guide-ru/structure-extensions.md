Расширения
==========

Расширения - это распространяемые программные пакеты, специально разработанные для использования в приложениях Yii и
содержащие готовые функции. Например, расширение [yiisoft/yii2-debug](https://github.com/yiisoft/yii2-debug) добавляет удобную отладочную
панель в нижнюю часть каждой страницы вашего приложения, чтобы помочь вам разобраться в том, как генерируются
страницы. Вы можете использовать расширения для ускорения процесса разработки. Вы также можете оформить ваш код как
расширение, чтобы поделиться с другими людьми результатами вашей работы.

> Info: Мы используем термин "расширение" для специфичных для Yii программных пакетов. Программные пакеты
  общего назначения, которые могут быть использованы без Yii, мы будем называть "пакет" или "библиотека".


## Использование расширений <span id="using-extensions"></span>

Чтобы использовать расширение, вам необходимо установить его. Большинство расширений распространяются как пакеты
[Composer](https://getcomposer.org/), которые могут быть установлены посредством следующих двух шагов:

1. Отредактируйте файл вашего приложения `composer.json`, указав, какие расширения (пакеты Composer) вы хотите
   установить.
2. Выполните команду `php composer.phar install`, чтобы установить указанные расширения.

Обратите внимание, что вам может потребоваться установить [Composer](https://getcomposer.org/), если у вас его нет.

По умолчанию, Composer устанавливает пакеты, зарегистрированные на [Packagist](https://packagist.org/) - крупнейшем
репозитории для пакетов Composer с открытым исходным кодом. Вы также можете
[создать свой репозиторий](https://getcomposer.org/doc/05-repositories.md#repository) и настроить Composer для его
использования. Это полезно, если вы разрабатываете закрытые расширения и хотите использовать их в нескольких своих
проектах.

Расширения, установленные Composer'ом, хранятся в директории `BasePath/vendor`, где `BasePath` -
[базовая директория](structure-applications.md#basePath) приложения. Composer - это менеджер зависимостей, и поэтому
после установки пакета он также установит все зависимые пакеты.

Например, для установки расширения `yiisoft/yii2-imagine` нужно отредактировать ваш `composer.json` как показано далее:

```json
{
    // ...

    "require": {
        // ... другие зависимости

        "yiisoft/yii2-imagine": "~2.0.0"
    }
}
```

После установки вы можете увидеть директорию `yiisoft/yii2-imagine`, находящуюся по пути `BasePath/vendor`. Также вы
можете увидеть директорию `imagine/imagine`, которая содержит зависимый пакет.

> Info: `yiisoft/yii2-imagine` является базовым расширением, которое разрабатывает и поддерживает команда
  разработчиков Yii. Все базовые расширения размещены на [Packagist](https://packagist.org/) и называются
  `yiisoft/yii2-xyz`, где `xyz` является названием расширения.

Теперь вы можете использовать установленное расширение как часть вашего приложения. Следующий пример показывает, как вы
можете использовать класс `yii\imagine\Image`, который содержится в расширении `yiisoft/yii2-imagine`.

```php
use Yii;
use yii\imagine\Image;

// генерация миниатюры изображения
Image::thumbnail('@webroot/img/test-image.jpg', 120, 120)
    ->save(Yii::getAlias('@runtime/thumb-test-image.jpg'), ['quality' => 50]);
```

> Info: Классы расширений автоматически загружаются [автозагрузчиком классов Yii](concept-autoloading.md).


### Ручная установка расширений <span id="installing-extensions-manually"></span>

В некоторых редких случаях вы можете захотеть установить некоторые расширения вручную, а не полагаться на Composer.
Чтобы сделать это, вы должны

1. загрузить архив с файлами расширения и распаковать его в директорию `vendor`.
2. установить автозагрузчики классов, предоставляемые расширениями, если таковые имеются.
3. загрузить и установить все зависимые расширения в соответствии с инструкциями.

Если расширение не имеет автозагрузчика классов, но следует [стандарту PSR-4](http://www.php-fig.org/psr/psr-4/), то вы
можете использовать автозагрузчик классов, предоставленный Yii для загрузки классов расширений. Всё, что вам нужно
сделать, это объявить [псевдоним](concept-aliases.md#defining-aliases) для корневого каталога расширения. Например,
если вы установили расширение в директорию `vendor/mycompany/myext` и классы расширения находятся в пространстве имён
`myext`, то вы можете включить следующий код в конфигурацию вашего приложения:

```php
[
    'aliases' => [
        '@myext' => '@vendor/mycompany/myext',
    ],
]
```


## Создание расширений <span id="creating-extensions"></span>

Вы можете захотеть создать расширение, когда чувствуете необходимость поделиться своим хорошим кодом с другими людьми.
Расширение может содержать любой код, который вам нравится, например, класс-помощник, виджет, модуль и т.д.

Рекомендуется создавать расширение как [пакет Composer](https://getcomposer.org/), для того, чтобы его можно было
легко установить и использовать, как описано в предыдущей главе.

Ниже приведены основные шаги, которым нужно следовать, чтобы создать пакет Composer.

1. Создайте проект для вашего расширения и разместите его в VCS репозитории, таком как [github.com](https://github.com).
   Разработка и поддержка расширения должна выполняться в этом репозитории.
2. В корневой директории проекта создайте файл под названием `composer.json`, в соответствии с требованиями Composer.
   Вы можете обратиться к следующему разделу за более подробной информацией.
3. Зарегистрируйте ваше расширение в репозитории Composer, таком как [Packagist](https://packagist.org/), чтобы другие
   пользователи могли найти и установить ваше расширение, используя Composer.


### `composer.json` <span id="composer-json"></span>

Каждый пакет Composer должен иметь файл `composer.json` в своей корневой директории. Этот файл содержит метаданные о
пакете. Вы можете найти полную спецификацию по этому файлу в
[Руководстве Composer](https://getcomposer.org/doc/01-basic-usage.md#composer-json-project-setup). Следующий пример
демонстрирует файл `composer.json` для расширения `yiisoft/yii2-imagine`:

```json
{
    // название пакета
    "name": "yiisoft/yii2-imagine",

    // тип пакета
    "type": "yii2-extension",

    "description": "The Imagine integration for the Yii framework",
    "keywords": ["yii2", "imagine", "image", "helper"],
    "license": "BSD-3-Clause",
    "support": {
        "issues": "https://github.com/yiisoft/yii2/issues?labels=ext%3Aimagine",
        "forum": "https://forum.yiiframework.com/",
        "wiki": "https://www.yiiframework.com/wiki/",
        "irc": "ircs://irc.libera.chat:6697/yii",
        "source": "https://github.com/yiisoft/yii2"
    },
    "authors": [
        {
            "name": "Antonio Ramirez",
            "email": "amigo.cobos@gmail.com"
        }
    ],

    // зависимости пакета
    "require": {
        "yiisoft/yii2": "~2.0.0",
        "imagine/imagine": "v0.5.0"
    },

    // указание автозагрузчика классов
    "autoload": {
        "psr-4": {
            "yii\\imagine\\": ""
        }
    }
}
```


#### Название пакета <span id="package-name"></span>

Каждый пакет Composer должен иметь название, которое однозначно идентифицирует пакет среди остальных. Название пакета
имеет формат `имяРазработчика/названиеПроекта`. Например, в пакете `yiisoft/yii2-imagine`, `yiisoft` является именем
разработчика, а `yii2-imagine` - названием пакета.

НЕ используйте `yiisoft` в качестве имени разработчика, так как оно зарезервировано для использования в коде ядра Yii.

Мы рекомендуем использовать префикс `yii2-` в названии проекта для пакетов, являющихся расширениями Yii 2, например,
`моёИмя/yii2-mywidget`. Это позволит пользователям легче определить, что пакет является расширением Yii 2.


#### Тип пакета <span id="package-type"></span>

Важно указать тип пакета вашего расширения как `yii2-extension`, чтобы пакет можно было распознать как расширение Yii во
время установки.

Когда пользователь запускает команду `php composer.phar install` для установки расширения, файл
`vendor/yiisoft/extensions.php` будет автоматически обновлён, чтобы включить информацию о новом расширении. Из этого
файла приложение Yii может узнать, какие расширения установлены (информацию можно получить с помощью
[[yii\base\Application::extensions]]).


#### Зависимости <span id="dependencies"></span>

Ваше расширение зависит от Yii (естественно). Вы можете посмотреть список зависимостей в секции `require`, входящей в
файл `composer.json`. Если ваше расширение зависит от других расширений или сторонних библиотек, то вы также должны их
перечислить. Убедитесь, что в ограничениях вы указали соответствующую версию (например, `1.*`, `@stable`) для каждой
зависимости. Используйте стабильные версии зависимостей, когда будет выпущена стабильная версия вашего расширения.


#### Автозагрузка классов <span id="class-autoloading"></span>

Для того, чтобы ваши классы были загружены автозагрузчиком классов Yii или автозагрузчиком классов Composer, вы должны
внести секцию `autoload` в файл `composer.json`, как показано ниже:

```json
{
    // ....

    "autoload": {
        "psr-4": {
            "yii\\imagine\\": ""
        }
    }
}
```

Вы можете перечислить один или несколько корневых пространств имён и соответствующие им пути.

Когда расширение установлено в приложение, Yii для каждого указанного корневого пространства имён создаст
[псевдоним](concept-aliases.md#extension-aliases), который указывает на директорию, соответствующую пространству имён.
Например, указанная в секции `autoload` запись будет соответствовать псевдониму `@yii/imagine`.


### Рекомендованные практики <span id="recommended-practices"></span>

Поскольку расширения предназначены для использования другими людьми, вам придётся приложить дополнительные усилия в
процессе разработки. Ниже приведены некоторые общие и рекомендованные практики для создания высококачественных
расширений.


#### Пространства имён <span id="namespaces"></span>

Во избежание конфликтов имён, а также для того, чтобы ваши классы были автозагружаемыми, вы должны следовать
[стандарту PSR-4](http://www.php-fig.org/psr/psr-4/) или [стандарту PSR-0](http://www.php-fig.org/psr/psr-0/) в
использовании пространств имён и названии классов вашего расширения.

Пространства имён в ваших классах должны начинаться с `имяРазработчика\названиеРасширения`, где `названиеРасширения`
совпадает с названием проекта в названии пакета, за исключением того, что оно не должно содержать префикса `yii2-`.
Например, для расширения `yiisoft/yii2-imagine` мы используем `yii\imagine` в качестве пространства имён.

Не используйте `yii`, `yii2` или `yiisoft` в качестве имени разработчика. Эти имена являются зарезервированными для
использования в коде ядра Yii.


#### Классы начальной загрузки <span id="bootstrapping-classes"></span>

Иногда вы можете захотеть выполнить некоторый код своего расширения в стадии
[начальной загрузки](runtime-bootstrapping.md) приложения. Например, ваше расширение может ответить на событие
приложения `beginRequest`, чтобы установить некоторые настройки окружения. Вы можете в инструкции по установке вашего
приложения написать, что необходимо назначить обработчик события `beginRequest`, но лучшим способом будет сделать это
автоматически.

Для достижения этой цели вы можете создать так называемый *класс начальной загрузки*, реализовав интерфейс
[[yii\base\BootstrapInterface]]. Например,

```php
namespace myname\mywidget;

use yii\base\BootstrapInterface;
use yii\base\Application;

class MyBootstrapClass implements BootstrapInterface
{
    public function bootstrap($app)
    {
        $app->on(Application::EVENT_BEFORE_REQUEST, function () {
             // остальной код
        });
    }
}
```

Затем нужно добавить этот класс в файл `composer.json` вашего расширения, как показано далее,

```json
{
    // ...

    "extra": {
        "bootstrap": "myname\\mywidget\\MyBootstrapClass"
    }
}
```

Когда расширение будет установлено в приложение, Yii автоматически инициирует экземпляр класса начальной загрузки и
вызовет его метод [[yii\base\BootstrapInterface::bootstrap()|bootstrap()]]  в процессе начальной загрузки каждого
запроса.


#### Работа с базами данных <span id="working-with-databases"></span>

Ваше расширение может иметь доступ к базам данных. Не думайте, что приложения, которые используют ваше расширение,
всегда используют `Yii::$db` в качестве соединения с БД. Вместо этого вам следует объявить свойство `db` в классах,
которым необходим доступ в БД. Это свойство позволит пользователям вашего расширения настроить соединение с БД,
которое они будут использовать в вашем расширении. В качестве примера вы можете обратиться к классу
[[yii\caching\DbCache]] и посмотреть, как он объявляет и использует свойство `db`.

Если в вашем приложении необходимо создать определённые таблицы БД или сделать изменения в схеме БД, вы должны

- создать файлы [миграций](db-migrations.md) для изменения схемы БД вместо простых SQL-файлов;
- попытаться сделать миграции, применимые к различным СУБД;
- избегать использования [Active Record](db-active-record.md) в миграциях.


#### Использование ресурсов <span id="using-assets"></span>

Если ваше расширение является виджетом или модулем, то есть вероятность, что оно потребует некоторых
[ресурсов](structure-assets.md) для работы. Например, модуль может отображать некоторые страницы, которые содержат
изображения, JavaScript и CSS. Так как все файлы расширения находятся в директории, недоступной из интернета, у вас
есть два варианта сделать директорию ресурсов непосредственно доступной из интернета:

- попросить пользователей расширения вручную скопировать файлы ресурсов в определённую, доступную из интернета папку;
- объявить [связку ресурсов](structure-assets.md) и полагаться на механизм публикации ресурсов, который автоматически
  копирует файлы, описанные в связке ресурсов в папку, доступную из интернета.

Мы рекомендуем вам использовать второй подход, чтобы ваше расширение было более простым в использовании для других
людей.


### Интернационализация и локализация <span id="i18n-l10n"></span>

Ваше расширение может быть использовано в приложениях, поддерживающих разные языки! Поэтому, если ваше расширение
отображает содержимое конечному пользователю, вы должны попробовать
[интернационализовать и локализовать](tutorial-i18n.md) его. В частности,

- Если расширение отображает сообщения, предназначенные для конечных пользователей, сообщения должны быть обернуты в
  метод `Yii::t()` так, чтобы они могли быть переведены. Сообщения, предназначенные для разработчиков (например,
  внутренние сообщения исключений), не нужно переводить.
- Если расширение отображает числа, даты и т.п., они должны быть отформатированы, используя [[yii\base\Formatter]] с
  соответствующими правилами форматирования.

Для более подробной информации вы можете обратиться к разделу [Интернационализация](tutorial-i18n.md)


#### Тестирование <span id="testing"></span>

Вы хотите, чтобы ваше расширение было стабильным и не приносило проблем другим людям. Для достижения этой цели вы
должны протестировать ваше расширение перед его публикацией.

Рекомендуется создавать различные тесты для покрытия кода вашего расширения, а не вручную тестировать его. Каждый раз
перед тем, как выпустить новую версию расширения, вы можете просто запустить эти тесты чтобы убедиться, что всё
работает правильно. Yii имеет поддержку тестирования, которая может помочь вам легче писать модульные, приёмочные и
функциональные тесты. Для более подробной информации вы можете обратиться в раздел [Тестирование](test-overview.md).


#### Версионирование <span id="versioning"></span>

Вы можете давать каждому выпуску вашего расширения номер версии (например, `1.0.1`). Мы рекомендуем вам придерживаться
практик [семантического версионирования](http://semver.org) при определении, какой номер версии должен использоваться.

#### Публикация <span id="releasing"></span>

Чтобы позволить другим людям узнать о вашем расширении, необходимо опубликовать его.

Если это первый выпуск вашего расширения, вы должны зарегистрировать его в репозитории Composer, таком, как
[Packagist](https://packagist.org/). После этого вам остаётся только создать тег выпуска (например, `v1.0.1`) в VCS
репозитории вашего расширения и уведомить репозиторий Composer о новом выпуске. Люди смогут найти новую версию и
установить или обновить расширение через репозиторий Composer.

В выпусках вашего расширения помимо файлов с кодом вы также должны рассмотреть вопрос о включении следующих файлов,
которые помогут людям изучить и использовать ваше расширение:

* Файл readme в корневой директории пакета: он описывает, что ваше расширение делает, а также как его установить и
  использовать. Мы рекомендуем вам написать его в формате [Markdown](http://daringfireball.net/projects/markdown/) и
  дать ему название `readme.md`.
* Файл changelog в корневой директории пакета: он описывает, какие изменения произошли в каждом выпуске. Этот файл
  может быть написан в формате Markdown и назван `changelog.md`.
* Файл upgrade в корневой директории пакета: он даёт инструкции о том, как обновить старые версии расширения. Этот
  файл может быть написан в формате Markdown и назван `upgrade.md`.
* Руководства пользователя, демо-версии, скриншоты и т.д.: они необходимы, если ваше расширение предоставляет много
  возможностей, которые невозможно полностью описать в файле readme.
* Документация API: ваш код должен быть документирован, чтобы позволить другим людям легко читать и понимать его. Вы
  можете обратиться к [файлу класса BaseObject](https://github.com/yiisoft/yii2/blob/master/framework/base/BaseObject.php),
  чтобы узнать, как нужно документировать код.

> Info: Ваши комментарии к коду могут быть написаны в формате Markdown. Расширение `yiisoft/yii2-apidoc`
  предоставляет инструмент для генерации документации API на основе ваших комментариев.

> Info: Пока это не обязательно, но мы всё-таки рекомендуем вам придерживаться определённого стиля кодирования.
  Вы можете обратиться к [стилю кодирования фреймворка](https://github.com/yiisoft/yii2/blob/master/docs/internals/core-code-style.md).


## Базовые расширения <span id="core-extensions"></span>

Yii предоставляет следующие базовые расширения, (или ["Official Extensions"](https://www.yiiframework.com/extensions/official)) которые разрабатывает и поддерживает команда разработчиков Yii. Они все
зарегистрированы на [Packagist](https://packagist.org/) и могут быть легко установлены, как описано в подразделе
[Использование расширений](#using-extensions).

- [yiisoft/yii2-apidoc](https://www.yiiframework.com/extension/yiisoft/yii2-apidoc):
  предоставляет расширяемый и высокопроизводительный генератор документации API. Оно также используется для генерации
  документации API фреймворка.
- [yiisoft/yii2-authclient](https://www.yiiframework.com/extension/yiisoft/yii2-authclient):
  предоставляет набор наиболее часто используемых клиентов авторизации, таких, как Facebook OAuth2 клиент и GitHub
  OAuth2 клиент.
- [yiisoft/yii2-bootstrap](https://www.yiiframework.com/extension/yiisoft/yii2-bootstrap):
  предоставляет набор виджетов, которые являются компонентами и плагинами [Bootstrap](http://getbootstrap.com/).
- [yiisoft/yii2-codeception](https://github.com/yiisoft/yii2-codeception) (deprecated):
  предоставляет поддержку тестирования, основанного на [Codeception](http://codeception.com/).
- [yiisoft/yii2-debug](https://www.yiiframework.com/extension/yiisoft/yii2-debug):
  предоставляет поддержку отладки в приложениях Yii. Когда это расширение используется, отладочная панель появится в
  нижней части каждой страницы. Это расширение также предоставляет набор отдельных страниц для отображения более
  подробной отладочной информации.
- [yiisoft/yii2-elasticsearch](https://www.yiiframework.com/extension/yiisoft/yii2-elasticsearch):
  предоставляет поддержку использования [Elasticsearch](https://www.elastic.co/). Оно включает в себя поддержку
  основных поисковых запросов, а также реализует шаблон проектирования [Active Record](db-active-record.md), который
  позволяет хранить записи Active Record в Elasticsearch.
- [yiisoft/yii2-faker](https://github.com/yiisoft/yii2-faker):
  предоставляет поддержку использования [Faker](https://github.com/fzaninotto/Faker) для генерации фиктивных данных.
- [yiisoft/yii2-gii](https://github.com/yiisoft/yii2-gii):
  предоставляет веб-интерфейс для генерации кода, который является весьма расширяемым и может быть использован для
  быстрой генерации моделей, форм, модулей, CRUD и т.д.
- [yiisoft/yii2-httpclient](https://github.com/yiisoft/yii2-httpclient):
  предоставляет HTTP клиент.
- [yiisoft/yii2-imagine](https://github.com/yiisoft/yii2-imagine):
  предоставляет часто используемые функции для работы с изображениями, основанные на библиотеке
  [Imagine](http://imagine.readthedocs.org/).
- [yiisoft/yii2-jui](https://github.com/yiisoft/yii2-jui):
  предоставляет набор виджетов, основанный на взаимодействиях и виджетах [JQuery UI](http://jqueryui.com/).
- [yiisoft/yii2-mongodb](https://github.com/yiisoft/yii2-mongodb):
  предоставляет поддержку использования [MongoDB](https://www.mongodb.com/). Оно включает такие возможности, как
  базовые запросы, Active Record, миграции, кэширование, генерация кода и т.д.
- [yiisoft/yii2-redis](https://github.com/yiisoft/yii2-redis):
  предоставляет поддержку использования [redis](http://redis.io/). Оно включает такие возможности, как базовые запросы,
  Active Record, кэширование и т.д.
- [yiisoft/yii2-smarty](https://github.com/yiisoft/yii2-smarty):
  предоставляет шаблонизатор, основанный на [Smarty](http://www.smarty.net/).
- [yiisoft/yii2-sphinx](https://github.com/yiisoft/yii2-sphinx):
  предоставляет поддержку использования [Sphinx](http://sphinxsearch.com). Оно включает такие возможности, как базовые
  запросы, Active Record, генерация кода и т.д.
- [yiisoft/yii2-swiftmailer](https://github.com/yiisoft/yii2-swiftmailer):
  предоставляет возможности отправки email, основанные на [swiftmailer](http://swiftmailer.org/).
- [yiisoft/yii2-twig](https://github.com/yiisoft/yii2-twig):
  предоставляет шаблонизатор, основанный на [Twig](https://twig.symfony.com/).
