# InSales uploader
Менеджер ассетов для платформы [InSales](http://www.insales.ru/).

[![npm version](https://badge.fury.io/js/insales-uploader.svg)](https://badge.fury.io/js/insales-uploader)
[![Dependency Status](https://gemnasium.com/badges/github.com/VladimirIvanin/insales-uploader.svg)](https://gemnasium.com/github.com/VladimirIvanin/insales-uploader)

## Установка

```
npm install insales-uploader --save-dev
```

## Пример

```javascript
// Подключение
var InsalesUploader = require('insales-uploader');

// Настройки
var option = {
  account: {
    id: '0123456798',
    token: '0123456798',
    url: 'shop-41324.myinsales.ru',
    http: true
  },
  theme: {
    id: '123456',
    root: 'my-shop',
    update: true,
    excludeFiles: ['**/*.DS_Store', '**/*.log'],
    startBackup: true
  },
  tools:{
    debugMode: false,
    openBrowser: {
      start: true,
      app: 'firefox'
    },
    browserSync: {
      start: false,
      uploadRestart: false,
      browser: 'firefox'
    },
    autoprefixer: {
      use: true,
      config: {
          browsers: ['last 21 versions'],
          cascade: true
      }
    },
    eslint: {
      use: true,
      stopOnFail: true
    },
    stylelint: {
      use: true,
      stopOnFail: true,
      config: {
        "rules": {
          "property-no-unknown": true
        }
      }
    }
  }
}

// Инициализация
var InsalesUp = new InsalesUploader(option)

// Метод вызывает загрузку темы на компьютер
InsalesUp.download()

```

## Методы

`download` — загрузка темы на компьютер

`pullTheme` — загрузка темы на компьютер. Перед началом загрузки, все локальные файлы удаляются

`pushTheme` — загрузка темы на сервер с полным обновлением файлов. `Внимание`, используя этот метод велика вероятность того, что ссылки поставленные через фильтр asset_url обновятся не сразу, так же могут не собраться файлы в которых используется `#= require`.

`upload` — загрузка файлов темы на сервер (принимает параметр `update`, если update:true, файлы на сервере перезаписываются, иначе пропускаются. Лишние файлы на сервере не удаляются)

`stream` — отслеживание изменений в файлах

`backup` — создание архива с резервной копией

`initAssets` — сортировка ассетов из папки media

`diffLocalAssets` — Метод сравнивает список файлов на сервере со списком в локальной копии

`openBrowser` — открыть сайт в браузере

`triggerFile(event, path)` - добавить/удалить/изменить файл на сервере. Event - строка события, что произошло с файлом (add/change/unlink). Path - абсолютный путь к файлу

> Внимание! Методы *download* и *stream* не следует запускать одновременно!


---

## Настройки
#### account
* id, token — необходимо сгенерировать в бэк-офисе: Приложения -> Разработчикам -> Создать новый ключ доступа;
* url — url магазина из бэк-офиса, ссылка не должна содержать 'http://' и '/';
* http — если в админ панели нет поддержки https, следует выставить значение - true;

#### theme
* id — id темы;
* root — корнеевая папка для сохранения темы;
* update — при значении 'true' локальные файлы будут перезаписываться при повторной загрузке;
* startBackup — при значении 'true' во время загрузки темы через метод **download** в папке 'backup' будут сохранятся резервные копии темы.
* excludeFiles - массив путей, которые будут игнорироваться при отслеживании изменений.

#### tools
* browserSync — синхронизация браузера и изменений в файлах. Синхронизация включается при запуске метода `stream`. Поумолчанию перезагрузка браузера отключена для загружаемых файлов, перезагрузка срабатывает на изменение и удаление. Так же можно указать доп. параметры согласно api [browsersync](https://www.browsersync.io/docs/options). `Возможен редирект на основной домен, в этом случае в конфиге попробуйте указать основной домен.
* openBrowser — открытие браузера при запуске метода `stream`. Чтобы включить данную опцию, в объект свойства нужно добавить `start: true`. Будет открыт браузер поумолчанию. Для старта сайта в определенном браузере, нужно добавить свойство `app`. Свойство `app` специфично работает в разных операционных системах, например чтобы открыть `Chrome`, пользователям MacOs нужно указать `google chrome`, пользователям Linux `google-chrome`, пользователям Windows `chrome`.
* debugMode — логирование операций.
* stylelint — линтер стилей, можно добавлять свои настройки согласно api [stylelint](https://stylelint.io/user-guide/rules/). `use` - использовать линтер? `stopOnFail` - останавливать отправление файла на сервер при ошибках?
* autoprefixer — добавление префиксов к стилям, можно добавлять свои настройки согласно api [autoprefixer](https://github.com/postcss/autoprefixer). `use` - использовать autoprefixer?
* eslint — js линтер, можно добавлять свои настройки согласно api [ESLint ](http://eslint.org/docs/rules/). `use` - использовать ESLint?

> [Интегрированно в Devu](https://github.com/VladimirIvanin/devu)

> [Пример использования **insales-uploader** с Gulp.js](https://github.com/brainmurder/InSales-uploader-gulp-test)

### Структура папок

Папки `media` и `assets`, дублирут друг друга. Когда запущен стрим изменения попадают в обе папки. Так же при скачивании файлы раскладываются в `media` и `assets`. Assets создаётся для удобства работы.
Папка `media` является приоритетной, так как она предусмотрена архитектурой тем на платформе InSales.

```
root/
    |-- assets/
        |-- fonts/
        |-- img/
        |-- js/
        |-- media/
        |-- style/
        |-- svg/
    |-- config/
    |-- media/
    |-- snippets/
    |-- templates/
    |-- backup/
```

![InSales](https://cdn.rawgit.com/brainmurder/insales-uploader/master/insales.png)
