---
title: "Объяснение структуры каталогов"
translation: "getting-started/directory-structure"
---

Корневой каталог MODX разделен на несколько подкаталогов, каждый со своим набором обязанностей и задач. Некоторые из этих каталогов можно переименовывать и перемещать, а их расположение можно настраивать во время установки.

## connectors/

Cконнекторы - это, по сути, точки входа для запросов AJAX в MODX. Они не делают никаких манипуляций с базой данных самостоятельно: они просто загружают основной класс MODX, очищают любые данные запроса, а затем обрабатывают запрос, указывая на соответствующий файл процессора.

Например, когда мы создаем ресурс, мы запрашиваем `connectors/resource/index.php?action=create`. `index.php` файл будет включать в себя файл базового соединителя (`connectors/index.php`), который создает экземпляр основного объекта MODX, обрабатывает любые пользовательские [Контекст](building-sites/contexts "Контексты") переключение и дезинфекция запроса `GET` или `POST`. Затем `connectors/resource/index.php` «обработает» запрос и вызовет правильный файл процессора, который мы обсудим позже.

### Известные файлы

- **connectors/index.php**- Этот файл особенно полезен при создании ваших собственных коннекторов. Просто включите этот файл в ваши коннекторы, а затем обработайте запрос, используя `$modx->request->handleRequest()`;

## core/

Ядро - это то, что делает MODX, MODX. Это база для всех библиотек Revolution. Большая часть всего, что вам нужно, за исключением файлов менеджера и установочных файлов, находится в этом каталоге.

### core/cache/

Каталог кеша содержит все файлы кеша, сгенерированные MODX. Лексиконы, элементы, ресурсы, данные RSS и Smarty генерируются по требованию MODX, что означает, что они кэшируются только после первого доступа.

#### core/cache/logs/

Регистрация всех файлов в MODX производится здесь. Здесь вы найдете файл `error.log`, который содержит дату, время, файл и ошибку, записанную MODX.

Чтобы записать запись в этот файл, вы можете использовать метод `$modx->log()`.

#### core/cache/mgr/

Этот каталог содержит данные кэша для контекста mgr (Manager). Как и любой кэш контекста, он будет кешировать любые параметры контекста, которые были переопределены из их системных настроек по умолчанию.

#### core/cache/rss/

Кеш каждого RSS-канала в MODX.

#### core/cache/web/

В отличие от кеша в MODX Evolution, кеш MODX Revolution разделен на несколько частей. Каждый контекст (т.е. `web` и `mgr`) имеет файл `context.cache.php`. Этот файл похож на файл `config.cache.php`, за исключением того, что он кэширует только те настройки, которые были переопределены из их системных настроек по умолчанию. Любой контекст может переопределить настройку системы.

Кроме того, кэш веб-контекста будет содержать отдельные каталоги для ресурсов и элементов. Ресурс с идентификатором 12 будет найден в `cache / web / resources / 12.cache.php`. Этот новый механизм кэширования означает, что время загрузки уменьшится, а ограничение количества кэшируемых ресурсов исчезнет.

### core/components/

Когда вы устанавливаете пакет, используя [Менеджер пакетов](extending-modx/transport-packages "Менеджер пакетов"), в `core/components/` / будет создан каталог для хранения любых файлов, необходимых для запуска установленного компонента. Как правило, все файлы, необходимые для запуска в диспетчере, такие как контроллеры, данные модели / схемы, процессоры и файлы классов, должны храниться здесь, а также файлы, которые вам не нужны для доступа в Интернет.

### core/config/

Этот каталог содержит файл конфигурации для MODX Revolution. Он устанавливает учетные данные базы данных и ряд MODX\_ константы для правильной работы вашего сайта.

### core/docs/

Этот каталог содержит файл `changelog.txt`, лицензию GPL и все учебные пособия, созданные для Revolution.

### core/error/

Он содержит шаблоны по умолчанию для сообщений об ошибках в клиентской части Revolution. Вы можете настроить эти страницы здесь.

### core/export/

После запуска функции экспорта в MODX Revolution экспортированные файлы HTML для вашего сайта будут находиться здесь.

### core/import/

Чтобы запустить функцию импорта в MODX Revolution, вам нужно переместить HTML-файлы в этот каталог.

### core/lexicon/

`[Lexicons]` в Revolution отличаются от языковых файлов в Evolution по двум основным причинам.

Во-первых, в Revolution файлы лексиконов разделяются на отдельные каталоги в зависимости от их двузначного кода IANA (например, английские лексиконы хранятся в `/core/lexicon/en /`). Внутри этих подкаталогов находятся несколько файлов в формате `topic.inc.php`. "topic" - это просто один файл лексикона. Разделение лексиконов по темам означает, что загружаются только строки `_required_language`, что экономит память и время загрузки.

Во-вторых, все лексиконы хранятся в базе данных MODX, а затем кешируются по требованию. Это позволяет управлять лексиконами непосредственно из менеджера в области «Управление лексиконами».

Чтобы загрузить лексикон, можно использовать такой формат:

``` php
$modx->lexicon->load( 'lang:namespace:topic' );
```

\# **lang** - 2-значный код IANA. Это необязательно, и по умолчанию 'en'.

1. **namespace** - У каждого лексикона свое [Пространство имен](extending-modx/namespaces "Пространство имен"). Встроенное пространство имен для MODX - «ядро». Создатели пакетов также смогут создавать собственные пространства имен, а пользователи Manager также могут создавать свои собственные пространства имен.
2. **topic** - Конкретная тема/файл, который вы хотите загрузить.

### core/model/

Это модель. Что за модель, говорите? Ну, это M в MVC (модель-представление-контроллер), которая является парадигмой ОО, которая утверждает, что в приложении должно быть как минимум три части. Модель, которая содержит структуру базы данных и привязки к ней View, который является частью графического интерфейса приложения, который не содержит никакой логики - только представление; и контроллеры, которые соединяют модель с представлением.

Итак, MODX делает модель своего рода похожей. На самом деле мы делаем модель MVC/C, в которой мы добавляем в модель точку доступа Connector и процессоры. Мы объясним это, когда придем к ним. Что вам нужно знать, так это то, что модель содержит все классы PHP, которые запускают Revolution, в том числе процессоры, которые обрабатывают определенные функции, такие как сохранение фрагментов, удаление фрагментов и т.д.

#### core/model/modx/

«Подожди! Я думал, что мы уже в директории modx? Почему другой подкаталог modx?» Хороший вопрос. Ну, MODX Revolution использует xPDO для управления базой данных. xPDO использует идею «пакетов» для разных соединений с разными моделями. Итак, если бы я хотел создать свои пользовательские таблицы, я бы создал новый пакет xPDO и добавил его во время выполнения. Таким образом, я мог использовать созданные карты и классы, не изменяя ядро MODX. Это показано в [Создание стороннего компонента](extending-modx/tutorials/developing-an-extra "Написание стороннего компонента в MODX Revolution") руководстве.

Таким образом, можно сказать, что каталог `core/model/modx` ссылается на пакет "modx". Пойдемте внутрь, и вы увидите массу занятий. Это классы, которые являются либо `xPDOObjects` - которые являются PHP-классами, представляющими таблицы в БД (т.е. `modsnippet.class.php` - это PHP-класс, который является объектом `modx_site_snippets`), либо они являются функциональными классами, как `modcachemanager.class.php`.

Подкаталоги в этой папке - не включая mysql или процессоры - являются подкатегориями классов, которые загружаются следующим образом: `$modx->loadClass('transport.modPackageBuilder');` с "." будучи разделением каталогов.

##### core/model/modx/mysql/

Этот каталог содержит файлы классов и карт для каждого объекта xPDO. Карты - это просто массивы PHP, содержащие структуру таблицы базы данных, на которую они ссылаются.

Другие платформы баз данных, такие как pgsql, mssql и другие, также появятся здесь.

##### core/model/modx/processors/

Этот каталог содержит отдельные файлы процессора, используемые при манипулировании базой данных. К ним никогда не обращаются напрямую, а вместо этого получают доступ через соединители. Это позволяет заблокировать их для предотвращения несанкционированного доступа.

#### core/model/schema/

Схема - это XML-представление базы данных MODX. Это используется при создании новых карт и классов, но никогда не читается и не анализируется, когда MODX работает. По большей части вы можете игнорировать этот каталог, так как он в основном используется для разработки. В учебнике по [Созданию стороннего компонента](extending-modx/tutorials/developing-an-extra "Написание стороннего компонента в MODX Revolution") узнаете больше о схемах.

#### core/model/smarty/

Это содержит библиотеки Smarty. Это просто извлечение файлов Smarty, которые вы можете получить с <http://smarty.php.net>. Ничто в этой папке не настроено для MODX - это происходит в другом месте.

Smarty - это интеллектуальный объектно-ориентированный шаблонизатор, использующий динамические, модифицируемые заполнители. Большинство страниц, видимых в диспетчере и во время установки, представляют собой файлы шаблонов Smarty (.tpl), с которыми взаимодействует MODX.

Например, когда вы редактируете ресурс (часто документ) в Менеджере, вы просматриваете страницу, сгенерированную контроллером, в `manager/controllers/resource/staticresource/update.php` После установки характеристик ресурса в массиве `$resource` этот код отображает страницу:

``` php
$modx->smarty->assign('resource',$resource); return $modx->smarty->fetch('resource/staticresource/update.tpl');
```

Плейсхолдеры Smarty в update.tpl заполняются данными, хранящимися в массиве `$resource`.

### core/packages/

Здесь вы найдете любые транспортные пакеты, которые вы скачали через [Менеджер пакетов](extending-modx/transport-packages "Менеджер пакетов") раздел Revolution, такой как TinyMCE, Ditto и т.д. Базовый пакет также находится здесь. Это позволяет легко устанавливать и удалять, а также удаленно обновлять установленные пакеты.

Когда вы создаете пакет (например, после извлечения из Git), транспортный пакет будет храниться здесь.

### core/xpdo/

MODX Revolution был разработан для использования OpenExpedio (xPDO), расширения для PDO. Он обеспечивает единый интерфейс для управления базами данных и позволяет MODX поддерживать различные платформы баз данных, кроме MySQL.

В этом каталоге содержатся все файлы классов, необходимые xPDO для выполнения всего: от кэширования запросов до построения транспортных пакетов и вывода данных в виде удобного объекта JSON.

Эти классы используются внутри MODX, и разработчикам никогда не придется иметь дело с ними напрямую.

### Известные файлы

- **core/cache/config.cache.php** - Это кеш-файл для всех [Системных настроек](building-sites/settings "Системные настройки") в MODX. Их эквиваленты базы данных находятся в \_system\_settings таблица, и их эквиваленты xPDO являются объектами modSystemSetting.
  - **_Tip_** - Если вы когда-либо были заблокированы компонентом CAPTCHA, вы можете отредактировать этот файл и установить _use\_captcha_ to '0' отключить капчу. Затем вы можете войти и отключить CAPTCHA в [Системные настройки](building-sites/settings "Системные настройки").
- **core/cache/sitePublishing.idx.php** - В MODX Evolution этот файл содержал данные кэша для всех документов, фрагментов и фрагментов. В Revolution это больше не так, и теперь этот файл отслеживает интервалы обновления кэша.
- **core/cache/mgr/actions.cache.php** - карта всех объектов modAction.

## manager/

Менеджер - это бэкенд или область администрирования MODX для создания ресурсов, управления пользователями и выполнения общих задач по обслуживанию сайта.

### manager/assets/

Этот каталог содержит [ExtJS](http://extjs.com/) библиотеки, а также пользовательская реализация ModExt. ModExt расширяет оригинальную библиотеку ExtJS, чтобы сделать разработку более удобной для пользователей.

### manager/controllers/

Контроллеры - это файлы PHP, связанные с модами. Они просто выбирают данные и возвращают или выводят их в браузер для рендеринга и отображения. Всякий раз, когда вы загружаете страницу в Менеджере, вы фактически указываете MODX загрузить определенный контроллер, который просто загружает шаблон Smarty и выводит любой необходимый JavaScript в браузер.

### manager/templates/

Этот каталог содержит файлы шаблонов для каждой страницы менеджера. Они не содержат код PHP, а используются для организации HTML. Если вы ищете файл Smarty .tpl для конкретной страницы менеджера, проверьте каталог `manager/templates/default/`.

### Известные файлы

- **manager/assets/ext2/ext-all.js** - Это основной файл библиотеки Ext, который должен быть включен на всех страницах диспетчера (или любой странице, использующей Ext). Он сжат, чтобы сэкономить место, сократить время загрузки и ускорить загрузку страниц. Однако, если вы выполняете много работы с JavaScript, вы неизбежно столкнетесь с некоторыми загадочными ошибками из-за сжатия. Лучший способ справиться с этим - просто переименовать этот файл, а затем переименовать файл ext-all.js в ext-all-debug.js, чтобы использовать несжатую версию во время разработки. Просто обязательно переключите их потом!

## setup/

Этот каталог содержит файлы, необходимые для запуска программы установки и выполнения [Fresh Installation](getting-started/installation "Fresh Installation") или обновления.

## \_build/

Этот каталог присутствует только в версии MODX Revolution, загруженной с сервера Subversion (а также в дистрибутиве SDK). Он содержит упакованные файлы данных ядра MODX, необходимые для установки MODX в базу данных.

### Известные файлы

- **\_build/transport.core.php** - Этот файл должен быть выполнен после загрузки MODX Revolution и до запуска программы установки. После завершения вы должны заменить «core» каталог внутри вашего каталога `core/packages/`, который будет содержать все необходимые Транспортные средства для установки MODX Revolution.

## assets/

Этот каталог по умолчанию отсутствует в MODX Revolution, но, здесь обычно размещают изображения, CSS, JavaScript и другие медиафайлы.

### assets/components/

Когда вы устанавливаете пакет, используя [Менеджер пакетов](extending-modx/transport-packages "Менеджер пакетов"), в каталоге `assets/components/` будет создан каталог для хранения любых необходимых файлов компонентов, таких как JavaScript или изображения.