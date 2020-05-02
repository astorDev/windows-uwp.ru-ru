---
title: Создание одностраничного веб-приложения с серверной частью на основе REST API
description: Используйте популярные веб-технологии для создания размещенного веб-приложения для Microsoft Store.
keywords: размещенное веб-приложение, HWA, REST API, одностраничное приложение, SPA
ms.date: 05/10/2017
ms.topic: article
ms.localizationpriority: medium
ms.openlocfilehash: b9a6b80034dc8272226c9563960079434dff8e41
ms.sourcegitcommit: 76e8b4fb3f76cc162aab80982a441bfc18507fb4
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/29/2020
ms.locfileid: "75684785"
---
# <a name="create-a-single-page-web-app-with-rest-api-backend"></a>Создание одностраничного веб-приложения с серверной частью на основе REST API

**Создание размещенного веб-приложения для Microsoft Store с помощью популярных веб-технологий создания приложений на стороне клиента и сервера**

![Простая игра для тренировки памяти в виде одностраничного веб-приложения](images/fullstack.png)

Это руководство из двух частей содержит краткий обзор современной веб-разработки приложений на стороне клиента и сервера при создании простой игры для тренировки памяти, которая работает в браузере и как размещенное веб-приложение для Microsoft Store. В первой части вам потребуется создать простую службу REST API для серверной части игры. Размещая логику игры в облаке в качестве службы API, вы сохраняете состояние игры, чтобы пользователь мог продолжать играть в один и тот же экземпляр игры на разных устройствах. Во второй части вам потребуется создать клиентский пользовательский интерфейс в виде одностраничного веб-приложения с гибким макетом.

Мы будем использовать некоторые из самых популярных веб-технологий, в том числе среду выполнения [Node.js](https://nodejs.org/en/) и [Express](https://expressjs.com/), для разработки серверной части, инфраструктуру пользовательского интерфейса [Bootstrap](https://getbootstrap.com/), обработчик шаблонов [Pug](https://www.npmjs.com/package/pug) и [Swagger](https://swagger.io/tools/) для создания интерфейсов REST API. Вы также получите опыт работы с [порталом Azure](https://ms.portal.azure.com/) для размещения в облаке и работы с редактором [Visual Studio Code](https://code.visualstudio.com/).

## <a name="prerequisites"></a>Предварительные условия

Если эти ресурсы еще отсутствуют на вашем компьютере, перейдите по этим ссылкам для скачивания:

 - [Node.js](https://nodejs.org/en/download/): убедитесь, что вы выбрали этот параметр, чтобы добавить элемент Node в параметр PATH.

 - [Генератор Express](https://expressjs.com/en/starter/generator.html): после установки Node установите Express, выполнив команду `npm install express-generator -g`.

 - [Visual Studio Code](https://code.visualstudio.com/)

Если вы хотите выполнить последние этапы размещения службы API и игрового приложения для развития памяти на Microsoft Azure, необходимо [создать бесплатную учетную запись Azure](https://azure.microsoft.com/free/), если вы еще это не сделали.

Если вы решите пропустить (или отложить) часть, посвященную Azure, просто пропустите последние разделы частей 1 и 2, в которых рассматривается размещение на Azure и упаковка приложения для Microsoft Store. Служба API и веб-приложение, которое вы создадите, будет по-прежнему работать локально (на `http://localhost:8000` и `http://localhost:3000` соответственно) на вашем компьютере.

## <a name="part-i-build-a-rest-api-backend"></a>Часть 1. Сборка серверной части REST API

Сначала мы создадим API простой игры для развития памяти, чтобы обеспечить работу этого игрового веб-приложения. Мы будем использовать [Swagger](https://swagger.io/) для определения API, а также создания кода формирования шаблонов и пользовательского веб-интерфейса для ручного тестирования.

Если вы хотите пропустить эту часть и перейти непосредственно к разделу [Часть 2. Создание одностраничного веб-приложения](#part-ii-build-a-single-page-web-application), то здесь вы можете получить [готовый код для части 1](https://github.com/Microsoft/Windows-tutorials-web/tree/master/Single-Page-App-with-REST-API/backend). Выполните инструкции из *файла сведений*, чтобы запустить код в локальной системе, или перейдите к подразделу *5. Размещение службы API в Azure и включение CORS*, чтобы запустить его из Azure.

### <a name="game-overview"></a>Обзор игры

*Память* (также известна как [*Концентрация*](https://en.wikipedia.org/wiki/Concentration_(game)) и [*Пельманизм*](https://en.wikipedia.org/wiki/Pelmanism_(system))) является простой игрой, состоящей из колоды парных карт. Карты размещаются лицевой стороной вниз на столе, и игрок проверяет значения карт по две одновременно, отыскивая совпадения. После каждого хода карты снова помещаются лицевой стороной вниз, если не будет найдена одинаковая пара. В этом случае эти две карты будут удалены из игры. Цель игры — найти все пары карт за меньшее количество ходов.

В целях обучения структура игры, которую мы будем использовать, очень простая: это одна игра с одним игроком. Однако игровая логика размещается на стороне сервера (а не стороне клиента) для сохранения состояния игры, чтобы игрок мог продолжать играть в одну и ту же игру на разных устройствах.

Структура данных игры для развития памяти состоит просто из массива объектов JavaScript, каждый из которых представляет одну карту с индексами в массиве, действующими как идентификаторы карт. На сервере каждый объект карты имеет значение и флаг **cleared**. Например, поле для 2 совпадений (4 карты) может быть случайным образом создано и сериализовано подобным образом.

```json
[
    { "cleared":"false",
        "value":"0",
    },
    { "cleared":"false",
        "value":"1",
    },
    { "cleared":"false",
        "value":"1",
    },
    { "cleared":"false",
        "value":"0",
    }
]
```

Когда массив поля передается клиенту, ключи значений удаляются из массива для предотвращения мошенничества (например, проверка тела ответа HTTP с помощью инструментов браузера F12). Вот как будет выглядеть эта же новая игра для клиента, вызывающего конечную точку REST **GET /game**.

```json
[{ "cleared":"false"},{ "cleared":"false"},{ "cleared":"false"},{ "cleared":"false"}]
```

Говоря о конечных точках, служба игры для развития памяти будет состоять из трех интерфейсов REST API.

#### <a name="post-new"></a>POST /new
Инициализирует новое поле игры заданного размера (количество совпадений).

| Параметр | Описание |
|-----------|-------------|
| int *size* |Количество совпадающих пар перемешиваются случайным образом и помещаются на игровое "поле". Пример: `http://memorygameapisample/new?size=2`|

| Ответ | Описание |
|----------|-------------|
| 200 OK. | Новая игра для развития памяти указанного размера готова.|
| 400 — ошибочный запрос| Запрошенный размер вне допустимого диапазона.|


#### <a name="get-game"></a>GET /game
Получает текущее состояние поля игры для развития памяти.

*Нет параметров*

| Ответ | Описание |
|----------|-------------|
| 200 OK | Возвращает массив JSON объектов карт. Каждая карта имеет свойство **cleared**, показывающее, была ли уже найдена ее пара. Одинаковые карты также сообщают свое значение **value**. Пример: `[{"cleared":"false"},{"cleared":"false"},{"cleared":"true","value":1},{"cleared":"true","value":1}]`|

#### <a name="put-guess"></a>PUT /guess
Определяет карту, которую необходимо открыть, и проверяет ее соответствие ранее открытой карте.

| Параметр | Описание |
|-----------|-------------|
| int *card* | Идентификатор карты (индекс в массиве игрового поля) для отображения. Каждое полное "угадывание" состоит из двух указанных карт (то есть двух вызовов к **/guess** с допустимыми и уникальными значениями *card*). Пример: `http://memorygameapisample/guess?card=0`|

| Ответ | Описание |
|----------|-------------|
| 200 OK | Возвращает JSON с **идентификатором** и **значением** указанной карты. Пример: `[{"id":0,"value":1}]`|
| 400 — ошибочный запрос |  Ошибка с указанной картой. Просмотрите тело ответа HTTP для получения дополнительных сведений.|

### <a name="1-spec-out-the-api-and-generate-code-stubs"></a>1. Спецификация API и создание заглушек кода

Мы будем использовать [Swagger](https://swagger.io/) для преобразования структуры API игры для развития памяти в работающий код сервера Node.js. Вот как можно определить [интерфейсы API игры для развития памяти как метаданные Swagger](https://github.com/Microsoft/Windows-tutorials-web/blob/master/Single-Page-App-with-REST-API/backend/api.json). Мы будем использовать это для создания заглушек кода сервера.

1. Создайте новую папку (например, в вашем локальном каталоге *GitHub*) и скачайте файл [**api.json**](https://raw.githubusercontent.com/Microsoft/Windows-tutorials-web/master/Single-Page-App-with-REST-API/backend/api.json?token=ACEfklXAHTeLkHYaI5plV20QCGuqC31cks5ZFhVIwA%3D%3D), содержащий определения API нашей игры для развития памяти. Убедитесь, что имя папки не содержит пробелов.

2. Откройте свою предпочитаемую оболочку ([или используйте интегрированный терминал Visual Studio Code](https://code.visualstudio.com/docs/editor/integrated-terminal)) для этой папки и выполните следующую команду диспетчера пакетов узла (NPM), чтобы установить средство создания шаблонов кода [Yeoman](https://yeoman.io/) (yo) и генератор Swagger для глобальной ( **-g**) среды Node.

    ```
    npm install -g yo
    npm install -g generator-swaggerize
    ```

3. Теперь мы создадим код шаблонов сервера с помощью Swagger.

    ```
    yo swaggerize
    ```

4. Команда **swaggerize** задаст вам несколько вопросов.
    - Путь (или URL-адрес) для документа swagger: **api.json**.
    - Платформа: **express**.
    - Как вы хотите назвать этот проект (YourFolderNameHere): **[введите]** .

    Ответьте на все остальные вопросы по собственному усмотрению. Сведения в основном заполняют файл *package.json* контактными данными, таким образом вы можете распространять свой код в виде пакета NPM.

5. Наконец, установите все зависимости (перечисленные в *package.json*) для нового проекта и поддержку [пользовательского интерфейса Swagger](https://swagger.io/swagger-ui/).

    ```
    npm install
    npm install swaggerize-ui
    ```

    Теперь запустите VS Code и выберите элементы **Файл** > **Открыть папку...** и перейдите в каталог MemoryGameAPI. Это сервер API Node.js, который вы только что создали. Он использует популярную платформу веб-приложений [ExpressJS](https://expressjs.com/en/4x/api.html) для структурирования и запуска проекта.

### <a name="2-customize-the-server-code-and-setup-debugging"></a>2. Настройка серверного кода и отладка установки

Файл *server.js* в корне проекта действует как "основная" функция сервера. Откройте его в VS Code и скопируйте в него следующие данные. Строки, измененные из созданного кода, содержат комментарии с дальнейшим объяснением.

```javascript
'use strict';

var port = process.env.PORT || 8000; // Better flexibility than hardcoding the port

var Http = require('http');
var Express = require('express');
var BodyParser = require('body-parser');
var Swaggerize = require('swaggerize-express');
var SwaggerUi = require('swaggerize-ui'); // Provides UI for testing our API
var Path = require('path');

var App = Express();
var Server = Http.createServer(App);

App.use(function(req, res, next) {  // Enable cross origin resource sharing (for app frontend)
    res.header('Access-Control-Allow-Origin', '*');
    res.header('Access-Control-Allow-Methods', 'GET,PUT,POST,OPTIONS');
    res.header('Access-Control-Allow-Headers', 'Content-Type, Authorization, Content-Length, X-Requested-With');

    // Prevents CORS preflight request (for PUT game_guess) from redirecting
    if ('OPTIONS' == req.method) {
      res.send(200);
    }
    else {
      next(); // Passes control to next (Swagger) handler
    }
});

App.use(BodyParser.json());
App.use(BodyParser.urlencoded({
    extended: true
}));

App.use(Swaggerize({
    api: Path.resolve('./config/swagger.json'),
    handlers: Path.resolve('./handlers'),
    docspath: '/swagger'   //  Hooks up the testing UI
}));

App.use('/', SwaggerUi({    // Serves the testing UI from our base URL
  docs: '/swagger'          //
}));

Server.listen(port, function () {  // Starts server with our modfied port settings
 });
```

Итак, пришло время запустить ваш сервер! Давайте настроим Visual Studio Code для отладки узла, пока мы работаем над этим. Щелкните значок панели **Отладка** (Ctrl+Shift+D), а затем значок с изображением шестеренки (открывает launch.json) и измените настройки на указанные ниже.

```json
"configurations": [
    {
        "type": "node",
        "request": "launch",
        "name": "Launch Program",
        "program": "${workspaceRoot}/server.js"
    }
]
```

Теперь нажмите клавишу F5 и откройте браузер на странице [https://localhost:8000](https://localhost:8000). Эта страница должна открыться с пользовательским интерфейсом Swagger для нашего API игры для развития памяти, в котором можно развернуть сведения и поля ввода для каждого из методов. Можно даже попробовать вызвать интерфейсы API, хотя их ответы будут содержать только макетированные данные (предоставляемые модулем [Swagmock](https://www.npmjs.com/package/swagmock)). Пришло время добавить логику игры, чтобы сделать эти API реальными.

### <a name="3-set-up-your-route-handlers"></a>3. Настройка обработчиков маршрута

Файл Swagger (config\swagger.json) указывает нашему серверу, как обрабатывать различные HTTP-запросы клиента путем сопоставления каждого пути URL-адреса, которые он определяет для файла обработчика (в \handlers), а также каждого метода, определенного для этого пути (например, **GET**, **POST**) для `operationId` (функция) в рамках этого файла обработчика.

На этом уровне нашей программы мы добавим некоторую проверку входных данных, прежде чем передать различные запросы клиента к нашей модели данных. Скачайте (или скопируйте и вставьте содержимое):

 - этот код [game.js](https://raw.githubusercontent.com/Microsoft/Windows-tutorials-web/master/Single-Page-App-with-REST-API/backend/handlers/game.js?token=ACEfkvhw6BUnkeSsZgnzVe086T5WLwjfks5ZFhW5wA%3D%3D) в файл **handlers\game.js**;
 - этот код [guess.js](https://raw.githubusercontent.com/Microsoft/Windows-tutorials-web/master/Single-Page-App-with-REST-API/backend/handlers/guess.js?token=ACEfkswel02rHVr0e61bVsNdpv_i1Rtuks5ZFhXPwA%3D%3D) в файл **handlers\guess.js**;
 - этот код [new.js](https://raw.githubusercontent.com/Microsoft/Windows-tutorials-web/master/Single-Page-App-with-REST-API/backend/handlers/new.js?token=ACEfkgk2QXJeRc0aaLzY5ulFsAR4Juidks5ZFhXawA%3D%3D) в файл **handlers\new.js**.

 Можно просмотреть комментарии в этих файлах, чтобы получить дополнительные сведения об этих изменениях, но фактически они проверяют на наличие базовых ошибок ввода (например, клиент запрашивает новую игру с меньше чем одним совпадением) и отправляют сообщения с описаниями ошибки при необходимости. Обработчики также перенаправляют действительные запросы клиента к соответствующим файлам данных (в \data) для дальнейшей обработки. А теперь давайте поработаем с ними.

### <a name="4-set-up-your-data-model"></a>4. Настройка модели данных

Пора заменить заполняющую службу, имитирующую данные, моделью реальных данных нашего игрового поля.

Этот уровень программы представляет сами карты памяти и предоставляет большую часть игровой логики, в том числе "перемешивание" колоды для новой игры, определение пар соответствующих карт и отслеживание состояния игры. Скопируйте и вставьте:

 - этот код [game.js](https://raw.githubusercontent.com/Microsoft/Windows-tutorials-web/master/Single-Page-App-with-REST-API/backend/data/game.js?token=ACEfksAceJNQmhF82aHjQTx78jILYKfCks5ZFhX4wA%3D%3D) в файл **data\game.js**;
 - этот код [guess.js](https://raw.githubusercontent.com/Microsoft/Windows-tutorials-web/master/Single-Page-App-with-REST-API/backend/data/guess.js?token=ACEfkvY69Zr1AZQ4iXgfCgDxeinT21bBks5ZFhYBwA%3D%3D) в файл **data\guess.js**;
 - этот код [new.js](https://raw.githubusercontent.com/Microsoft/Windows-tutorials-web/master/Single-Page-App-with-REST-API/backend/data/new.js?token=ACEfkiqeDN0HjZ4-gIKRh3wfVZPSlEmgks5ZFhYPwA%3D%3D) в файл **data\new.js**.

Для простоты мы сохраняем наше игровое поле в глобальной переменной (`global.board`) на сервере узла. Но в реальности вы бы использовали облачное хранилище (например, Google [Cloud Datastore](https://cloud.google.com/datastore/) или Azure [DocumentDB](https://azure.microsoft.com/services/cosmos-db/)), чтобы превратить это в рабочую службу API игры для развития памяти, которая поддерживает одновременно несколько игр и игроков.

Убедитесь, что вы сохранили все изменения в VS Code, запустите свой сервер еще раз (нажмите клавишу F5 в VS Code или выполните команду `npm start` в оболочке, а затем перейдите на страницу [https://localhost:8000](https://localhost:8000)) для тестирования API игры.

Каждый раз нажимайте кнопку **Try it out!** (Попробовать!) во время одной из операций **/game**, **/guess** или **/new**, проверяйте полученные **тело ответа** и **код ответа** ниже, чтобы убедиться, что все работает правильно.

Попробуйте следующее. 

1. Создание игры `size=2`.

    ![Начните новую игру для развития памяти из пользовательского интерфейса Swagger](images/swagger_new.png)

2. Угадывание нескольких значений.

    ![Угадайте карту из пользовательского интерфейса Swagger](images/swagger_guess.png)

3. Проверка игрового поля в ходе игры.

    ![Проверьте состояние игры из пользовательского интерфейса Swagger](images/swagger_game.png)

Если все в порядке, ваша служба API готова для размещения в Azure. Если у вас возникают проблемы, попробуйте закомментировать следующие строки в \data\game.js.

```javascript
for (var i=0; i < board.length; i++){
    var card = {};
    card.cleared = board[i].cleared;

    // Only reveal cleared card values
    //if ("true" == card.cleared) {        // To debug the board, comment out this line
        card.value = board[i].value;
    //}                                    // And this line
    currentBoardState.push(card);
}
```

После этого изменения метод **GET /game** будет возвращать все значения карт (в том числе те, которые еще не были удалены). Это полезный отладочный прием, применяемый на этапе разработки клиентской части игры для развития памяти.

### <a name="5-optional-host-your-api-service-on-azure-and-enable-cors"></a>5. (Необязательно.) Размещение службы API в Azure и включение CORS

Документация Azure поможет выполнить следующие задачи:

 - [Регистрация нового *приложения API* на портале Azure](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-rest-api#createapiapp).
 - [Настройка развертывания Git для вашего приложения API](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-rest-api#deploy-the-api-with-git) и
 - [развертывание кода приложения API в Azure](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-rest-api#deploy-the-api-with-git).

При регистрации приложения старайтесь сделать максимально узнаваемым *имя приложения* (во избежание конфликтов с другими запрашиваемыми вариантами по URL-адресу *http://memorygameapi.azurewebsites.net* ).

Если вы дошли так далеко и Azure теперь обслуживает ваш пользовательский интерфейс Swagger, остался только последний этап до завершения серверной части игры на развитие памяти. На [портале Azure](https://portal.azure.com) выберите только что созданную *службу приложения*, затем выберите или найдите параметр **CORS**. В разделе **Разрешенные источники** добавьте звездочку (`*`) и нажмите кнопку **Сохранить**. Это позволит выполнять вызовы из разных источников к службе API из клиентской части игры для развития памяти при разработке на локальном компьютере. После завершения клиентской части игры для развития памяти и ее развертывания в Azure эту запись можно будет заменить конкретным URL-адресом веб-приложения.

### <a name="going-further"></a>Дальнейшая работа

Чтобы сделать API игры для развития памяти действующей серверной службой для рабочего приложения, необходимо расширить код для поддержки нескольких игроков и игр. Для этого вам, вероятно, потребуется подключить [аутентификацию](https://swagger.io/docs/specification/authentication/) (для управления удостоверениями игрока), [базу данных NoSQL](https://azure.microsoft.com/blog/dear-documentdb-customers-welcome-to-azure-cosmos-db/) (для отслеживания игр и игроков) и некоторое базовое [модульное тестирование](https://apigee.com/about/blog/api-technology/swagger-test-templates-test-your-apis) для API.

Ниже перечислено несколько полезных ресурсов для дальнейшей работы:

 - [Расширенная отладка Node.js с помощью Visual Studio Code](https://code.visualstudio.com/docs/nodejs/nodejs-debugging)

 - [Документация Azure по Интернету и мобильным устройствам](https://docs.microsoft.com/azure/#pivot=services&panel=web)

 - [Документация по Azure DocumentDB](https://azure.microsoft.com/blog/dear-documentdb-customers-welcome-to-azure-cosmos-db/)

## <a name="part-ii-build-a-single-page-web-application"></a>Часть 2. Создание одностраничного веб-приложения

После того, как вы создали (или [скачали](https://github.com/Microsoft/Windows-tutorials-web/tree/master/Single-Page-App-with-REST-API/backend)) [серверную часть REST API](#part-i-build-a-rest-api-backend) из части 1, вы готовы приступить к разработке клиентской части одностраничной игры для развития памяти с помощью [Node](https://nodejs.org/en/), [Express](https://expressjs.com/) и [Bootstrap](https://getbootstrap.com/).

Часть 2 этого руководства ознакомит вас со следующими материалами. 

* [Node.js](https://nodejs.org/en/): для создания размещения вашей игры на сервере
* [jQuery](https://jquery.com/): библиотека JavaScript.
* [Express](https://expressjs.com/): для платформы веб-приложений.
* [Pug](https://pugjs.org/) (ранее Jade): для модуля создания шаблонов.
* [Bootstrap](https://getbootstrap.com/): для гибкого макета.
* [Visual Studio Code](https://code.visualstudio.com/): для разработки кода, просмотра разметки и отладки кода.

### <a name="1-create-a-nodejs-application-by-using-express"></a>1. Создание приложения Node.js с помощью Express

Начнем с создания проекта Node.js с помощью Express.

1. Откройте командную строку и перейдите в каталог, в котором вы хотите сохранить игру. 
2. Используйте генератор Express для создания нового приложения *memory* с помощью модуля создания шаблонов *Pug*.

    ```
    express --view=pug memory
    ```

3. В каталог памяти установите зависимости, указанные в файле package.json. В корневом каталоге проекта будет создан файл package.json. Этот файл содержит модули, которые необходимы для вашего приложения Node.js.  

    ```
    cd memory
    npm install
    ```

    После выполнения этой команды вы должны увидеть папку node_modules, содержащую все модули, необходимые для этого приложения. 

4. Теперь запустите приложение.

    ```
    npm start
    ```

5. Просмотрите приложение, перейдя на страницу [https://localhost:3000/](https://localhost:3000/).

    ![Снимок экрана страницы http://localhost:3000/](./images/express.png)

6. Измените заголовок веб-приложения по умолчанию, изменив index.js в каталоге memory\routes. Измените `Express` в строке ниже на `Memory Game` (или на другой заголовок по выбору).

    ``` javascript
    res.render('index', { title: 'Express' });
    ```

7. Чтобы обновить приложение для отображения нового заголовка, остановите приложение, нажав клавиши **CTRL+C**, введите **Y** в командной строке, а затем перезапустите его с помощью команды `npm start`.

### <a name="2-add-client-side-game-logic-code"></a>2. Добавление кода логики игры на стороне клиента
Файлы, необходимые для этой части руководства, расположены в папке [Start](https://github.com/Microsoft/Windows-tutorials-web/tree/master/Single-Page-App-with-REST-API/frontend/Start). Если вы запутаетесь, готовый код доступен в папке [Final](https://github.com/Microsoft/Windows-tutorials-web/tree/master/Single-Page-App-with-REST-API/frontend/Final). 

1. Скопируйте файл scripts.js, который находится внутри папки [Start](https://github.com/Microsoft/Windows-tutorials-web/tree/master/Single-Page-App-with-REST-API/frontend/Start), и вставьте его в memory\public\javascripts. Этот файл содержит всю игровую логику, необходимую для запуска игры, в том числе:

    * Создание и запуск новой игры.
    * Восстановление игры, сохраненной на сервере.
    * Отрисовка игрового поля и карт на основе выбора пользователя.
    * Перевертывание карт.

2. В script.js начнем с изменения функции `newGame()`. Эта функция:

    * Обрабатывает размер выбора игры от пользователя.
    * Извлекает [массив игрового поля](#part-i-build-a-rest-api-backend) с сервера.
    * Вызывает функцию `drawGameBoard()` для размещения игрового поля на экране.

    Добавьте следующий код в `newGame()` после комментария `// Add code from Part 2.2 here`.

    ``` javascript
    // extract game size selection from user
    var size = $("#selectGameSize").val();

    // parse game size value
    size = parseInt(size, 10);
    ```

    Этот код получает значение из раскрывающегося меню с `id="selectGameSize"` (его мы создадим позднее) и сохраняет его в переменной (`size`).  Мы используем функцию [`parseInt()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/parseInt) для анализа строкового значения из раскрывающегося списка для возвращения целого числа, чтобы можно было передать значение `size` запрошенного игрового поля на сервер. 

    Мы используем метод [`/new`](#part-i-build-a-rest-api-backend), созданный в первой части этого руководства для отправки выбранного размера игрового поля на сервер. Этот метод возвращает один массив карт JSON и значения `true/false`, указывающие, совпали ли карты. 

3. Затем заполните функцию `restoreGame()`, которая восстанавливает последнюю игру, в которую играли. Ради простоты приложение всегда загружает последнюю игру. Если на сервере нет сохраненной игры, используйте раскрывающееся меню, чтобы начать новую игру. 

    Скопируйте и вставьте этот код в `restoreGame()`.

   ``` javascript 
   // reset the game
   gameBoardSize = 0;
   cardsFlipped = 0;

   // fetch the game state from the server 
   $.get("http://localhost:8000/game", function (response) {
       // store game board size
       gameBoardSize = response.length;

       // draw the game board
       drawGameBoard(response);
   });
   ```

    Игра теперь будет получать состояние игры с сервера. Подробнее о методе [`/game`](#part-i-build-a-rest-api-backend), используемом на этом шаге, рассказывается в части 1 этого руководства. Если вы используете Azure (или другую службу) для размещения серверной части API, замените адрес *localhost* выше URL-адресом рабочего развертывания.

4. Теперь нужно создать функцию `drawGameBoard()`.  Эта функция:

    * Определяет размер игры и применяет определенные стили CSS.
    * Создает карты в HTML.
    * Добавляет карты на страницу.

    Скопируйте и вставьте приведенный код в функцию `drawGameBoard()` в *scripts.js*.

    ``` javascript
    // create output
    var output = "";
    // detect board size CSS class
    var css = "";
    switch (board.length / 4) {
        case 1:
            css = "rows1";
            break;
        case 2:
            css = "rows2";
            break;
        case 3:
            css = "rows3";
            break;
        case 4:
            css = "rows4";
            break;   
    }
    // generate HTML for each card and append to the output
    for (var i = 0; i < board.length; i++) {
        if (board[i].cleared == "true") {
            // if the card has been cleared apply the .flip class
            output += "<div class=\"flipContainer col-xs-3 " + css + "\"><div class=\"cards flip matched\" id=\"" + i + "\" onClick=\"flipCard(this)\">\
                <div class=\"front\"><span class=\"glyphicon glyphicon-question-sign\"></span></div>\
                <div class=\"back\">" + lookUpGlyphicon(board[i].value) + "</div>\
                </div></div>";
        } else {
            output += "<div class=\"flipContainer col-xs-3 " + css + "\"><div class=\"cards\" id=\"" + i + "\" onClick=\"flipCard(this)\">\
                <div class=\"front\"><span class=\"glyphicon glyphicon-question-sign\"></span></div>\
                <div class=\"back\"></div>\
                </div></div>";
        }
    }
    // place the output on the page
    $("#game-board").html(output);
    ```

5. Затем нам понадобится завершить функцию `flipCard()`.  Эта функция обрабатывает основную часть игровой логики, в том числе получение значений выбранных карт от сервера с помощью метода [`/guess`](#part-i-build-a-rest-api-backend), созданного в первой части руководства. Не забудьте заменить адрес *localhost* URL-адресом рабочего развертывания, если вы размещаете серверную часть REST API в облаке.

    В функции `flipCard()` раскомментируйте следующий код.

    ``` javascript
    // post this guess to the server and get this card's value
    $.ajax({
        url: "http://localhost:8000/guess?card=" + selectedCards[0],
        type: 'PUT',
        success: function (response) {
            // display first card value
            $("#" + selectedCards[0] + " .back").html(lookUpGlyphicon(response[0].value));

            // store the first card value
            selectedCardsValues.push(response[0].value);
        }
    });
    ```

> [!TIP] 
> Если вы используете Visual Studio Code, выберите все строки кода, который вы хотите раскомментировать, и нажмите клавиши CRTL+K и U.

Здесь мы используем [`jQuery.ajax()`](https://api.jquery.com/jQuery.ajax/) и метод **PUT**[`/guess`](#part-i-build-a-rest-api-backend), созданный в части 1. 

Этот код выполняется в следующем порядке.

* `id` первой карты, выбранной пользователем, добавляется как первое значение в массив selectedCards[]: `selectedCards[0]`. 
* Значение (`id`) в `selectedCards[0]` передается на сервер с помощью метода [`/guess`](#part-i-build-a-rest-api-backend).
* Сервер отвечает, возвращая значение `value` этой карты (целое число).
* [Значок глифа Bootstrap](https://getbootstrap.com/components/) добавляется на рубашку карты, чей `id` — `selectedCards[0]`.
* Параметр `value` первой карты (от сервера) сохраняется в массиве `selectedCardsValues[]`: `selectedCardsValues[0]`. 

Второй ход пользователя следует той же логике. Если идентификаторы карт, которые выбрал пользователь, совпадают (например, `selectedCards[0] == selectedCards[1]`), то карты также совпадают. Класс CSS `.matched` добавляется к совпадающим картам (окрашивая их в зеленый цвет), и эти карты остаются перевернутыми.

Теперь нам необходимо добавить логику для проверки, подобрал ли пользователь пары всем картам и выиграл ли игру. Внутри функции `flipCard()` добавьте следующие строки кода под комментарием `//check if the user won the game`. 

``` javascript
if (cardsFlipped == gameBoardSize) {
    setTimeout(function () {
        output = "<div id=\"playAgain\"><p>You Win!</p><input type=\"button\" onClick=\"location.reload()\" value=\"Play Again\" class=\"btn\" /></div>";
        $("#game-board").html(output);
    }, 1000);
}   
```

Если количество перевернутых карт совпадает с размером игрового поля (например, `cardsFlipped == gameBoardSize`), значит больше нет неперевернутых карт и пользователь выиграл игру. Мы добавим простой код HTML в `div` с `id="game-board"`, чтобы сообщить пользователю, что он выиграл и может сыграть еще раз.  

### <a name="3-create-the-user-interface"></a>3. Создание пользовательского интерфейса 
Теперь давайте посмотрим на этот код в действии, создав пользовательский интерфейс. В этом руководстве мы используем модуль создания шаблонов [Pug](https://pugjs.org/) (ранее Jade).  *Pug* является чистым синтаксисом, учитывающим пробелы, для написания HTML. Пример. 

```
body
    h1 Memory Game
    #container
        p We love tutorials!
```

становится

``` html
<body>
    <h1>Memory Game</h1>
    <div id="container">
        <p>We love tutorials!</p>
    </div>
</body>
```


1. Замените файл layout.pug в memory\views предоставленным файлом layout.pug в папке Start. Внутри layout.pug вы увидите ссылки на:

    * Bootstrap;
    * jQuery;
    * пользовательский CSS-файл;
    * файл JavaScript, который мы только что закончили изменять.

2. Откройте файл index.pug в каталоге memory\views.
Этот файл расширяет файл layout.pug и прорисует нашу игру. В layout.pug вставьте следующие строки кода.

    ```
    extends layout
    block content  
        div
            form(method="GET")
            select(id="selectGameSize" class="form-control" onchange="newGame()")
                option(value="0") New Game
                option(value="2") 2 Matches
                option(value="4") 4 Matches
                option(value="6") 6 Matches
                option(value="8") 8 Matches         
            #game-board
                script restoreGame();
    ```

> [!TIP] 
> Помните: Pug учитывает пробелы. Убедитесь, что все ваши отступы поставлены верно!

### <a name="4-use-bootstraps-grid-system-to-create-a-responsive-layout"></a>4. Использование системы сетки Bootstrap для создания гибкого макета
[Система сетки](https://getbootstrap.com/css/#grid) Bootstrap — это гибкая система сетки, которая масштабирует сетку по мере изменения окна просмотра устройства. Карты в этой игре используют предопределенные классы системы сетки Bootstrap для макета, в том числе:
* `.container-fluid`: задает гибкий контейнер для сетки;
* `.row-fluid`: задает гибкие строки;
* `.col-xs-3`: задает количество столбцов.

Система сетки Bootstrap позволяет системе сетки свернуться в один столбец по вертикали, как если вы бы смотрели на меню навигации на мобильном устройстве.  Тем не менее, так как нам нужно, чтобы в нашей игре всегда были столбцы, мы используем предопределенный класс `.col-xs-3`, который постоянно поддерживает сетку в горизонтальном положении. 

Система сетки позволяет использовать до 12 столбцов. Так как мы хотим использовать только 4 столбца в игре, мы используем класс `.col-xs-3`. Этот класс указывает, что нам нужно, чтобы каждый столбец занимал по ширине 3 из 12 доступных столбцов, указанных ранее. На рисунке показана сетка из 12 столбцов и сетка из 4 столбцов, как в этой игре.

![Сетка Bootstrap с 12 и 4 столбцами](./images/grid.png)

1. Откройте scripts.js и найдите функцию `drawGameBoard()`.  В блоке кода, в котором мы создаем HTML для каждой карты, вы можете заметить элемент `div` на `class="col-xs-3"`? 

2. В index.pug давайте добавим предопределенные классы Bootstrap, упомянутые ранее, для создания нашего гибкого макета.  Измените index.pug в соответствии со следующим примером.

    ```
    extends layout

    block content   

        .container-fluid
            form(method="GET")
            select(id="selectGameSize" class="form-control" onchange="newGame()")
                option(value="0") New Game
                option(value="2") 2 Matches
                option(value="4") 4 Matches
                option(value="6") 6 Matches
                option(value="8") 8 Matches         
            #game-board.row-fluid 
                script restoreGame();
    ```

### <a name="5-add-a-card-flip-animation-with-css-transforms"></a>5. Добавление анимации переворота карты с помощью преобразований CSS
Замените файл style.css в memory\public\stylesheets файлом style.css из папки Start.

Добавление движения переворота с помощью [преобразований CSS](https://developer.mozilla.org/docs/Web/CSS/CSS_Transforms) обеспечивает реалистичное трехмерное движение переворота карт. Карты в игре создаются с помощью следующей структуры HTML и программно добавляются на игровое поле (в функции `drawGameBoard()`, показанной ранее).

``` html
<div class="flipContainer">
    <div class="cards">
        <div class="front"></div>
        <div class="back"></div>
    </div>
</div>
```

1. Чтобы начать, задайте [перспективу](https://developer.mozilla.org/en-US/docs/Web/CSS/transform) для родительского контейнера анимации (`.flipContainer`).  Это придает иллюзию глубины для дочерних элементов: чем выше значение, тем дальше от пользователя будет отображаться этот элемент. Давайте добавим следующую перспективу в класс `.flipContainer` в style.css.

    ``` css
    perspective: 1000px; 
    ```

2. Теперь добавьте следующие свойства в класс `.cards` в style.css. `.cards` `div` — это элемент, который фактически будет осуществлять анимацию переворота, отображая лицевую или обратную сторону карты. 

    ``` css
    transform-style: preserve-3d;
    transition-duration: 1s;
    ```

    Свойство [`transform-style`](https://developer.mozilla.org/en-US/docs/Web/CSS/transform-style) задает контекст трехмерной отрисовки, и его дочерние элементы класса `.cards` (`.front` и `.back`) входят в трехмерное пространство. Добавление свойства [`transition-duration`](https://developer.mozilla.org/en-US/docs/Web/CSS/transition-duration) позволяет указать время в секундах до конца анимации. 

3.  Используя свойство [`transform`](https://developer.mozilla.org/en-US/docs/Web/CSS/transform), мы можем повернуть карту вокруг оси y.  Добавьте следующую таблицу CSS в `cards.flip`.

    ``` css
    transform: rotateY(180deg);
    ```

    Стиль, определенный в `cards.flip`, включается и выключается в функции `flipCard` с помощью [`.toggleClass()`](https://api.jquery.com/toggleClass/). 

    ``` javascript
    $(card).toggleClass("flip");
    ```

    Теперь, когда пользователь щелкает карту, она поворачивается на 180 градусов.

### <a name="6-test-and-play"></a>6. Тестирование и игра
Поздравляем! Вы завершили создание веб-приложения! Давайте его протестируем. 

1. Откройте командную строку в каталоге memory и введите команду `npm start`.

2. В браузере перейдите на страницу [https://localhost:3000/](https://localhost:3000/) и сыграйте в игру.

3. В случае возникновения ошибок можно использовать инструменты отладки Node.js Visual Studio Code, нажав клавишу F5 на клавиатуре и введя `Node.js`. Дополнительные сведения об отладке в Visual Studio Code см. в этой [статье](https://code.visualstudio.com/docs/editor/debugging#_launch-configurations). 

    Можно также сравнить свой код с кодом, предоставленным в папке Final.

4. Чтобы остановить игру, в командной строке нажмите клавиши **CTRL+C** и введите **Y**. 

### <a name="going-further"></a>Дальнейшая работа

Теперь вы можете развернуть свое приложение в Azure (или любой другой облачной службе размещения) для тестирования на разных форм-факторах устройств, например мобильных устройствах, планшетах и компьютерах. (Не забудьте выполнить тестирование и в других браузерах!) Когда ваше приложение будет готово для рабочей среды, вы сможете легко упаковать его как *размещенное веб-приложение* (HWA) для *универсальной платформы Windows* (UWP) и распространять его через Microsoft Store.

Ниже приведены основные шаги для публикации в Microsoft Store.

 1. Создайте учетную запись [разработчика Windows](https://developer.microsoft.com/store/register).
 2. Заполните [контрольный список](https://docs.microsoft.com/windows/uwp/publish/app-submissions) для отправки приложения.
 3. Отправьте приложение на [сертификацию](https://docs.microsoft.com/windows/uwp/publish/the-app-certification-process).

Ниже перечислено несколько полезных ресурсов для дальнейшей работы:

 - [Развертывание проекта по разработке приложений на веб-сайты Azure](https://docs.microsoft.com/azure/cosmos-db/documentdb-nodejs-application#_Toc395783182)

 - [Преобразование веб-приложения в приложение универсальной платформы Windows (UWP)](https://docs.microsoft.com/microsoft-edge/progressive-web-apps)

 - [Публикация приложений для Windows](https://docs.microsoft.com/windows/uwp/publish/)
