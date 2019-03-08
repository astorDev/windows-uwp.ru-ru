---
title: Создание игры UWP на языке JavaScript
description: Простые игры для Microsoft Store, написанных на JavaScript и CreateJS UWP
ms.date: 02/09/2017
ms.topic: article
keywords: windows 10, uwp
ms.assetid: 01af8254-b073-445e-af4c-e474528f8aa3
ms.localizationpriority: medium
ms.openlocfilehash: 4d10dbf52f0ed01d46f9e5cba83cd14d48bfc88d
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57639179"
---
# <a name="create-a-uwp-game-in-javascript"></a>Создание игры UWP на языке JavaScript

## <a name="a-simple-2d-uwp-game-for-the-microsoft-store-written-in-javascript-and-createjs"></a>Простая двухмерная игра UWP для Microsoft Store, написанная на языках JavaScript и CreateJS


![Лист спрайтов с ходящим динозавром](images/JS2D_1.png)


## <a name="introduction"></a>Введение


Публикация приложения в Microsoft Store средства можно поделиться им (или продавать!) с миллионами людей на различных устройствах.  

Чтобы опубликовать приложение в Microsoft Store, оно должно быть написано как приложение универсальной платформы Windows (для универсальной платформы Windows). Однако UWP — очень гибкая платформа и поддерживает множество языков и технологий. Мы продемонстрируем это на примере простой игры, которая написана на языке JavaScript и использует несколько библиотек CreateJS. Также в примере показано, как рисовать спрайты, создавать игровой цикл, настраивать поддержку клавиатуры и мыши и адаптацию к различным размерам экрана.

Этот проект создается на языке JavaScript с помощью Visual Studio. Если внести некоторые небольшие изменения, его также можно разместить на веб-сайте или адаптировать для других платформ. 

**Примечание.** Это не является игра завершения (или хорошо!); он предназначен для демонстрации того, с помощью JavaScript и третий библиотека стороннего производителя, чтобы создать приложение готово к публикации в Microsoft Store.


## <a name="requirements"></a>Требования

Для этого проекта понадобятся:

* компьютер (или виртуальная машина) под управлением текущей версии Windows 10;
* копия Visual Studio. Бесплатный выпуск Visual Studio Community Edition можно скачать на [домашней странице Visual Studio](https://visualstudio.com).

В этом проекте используется инфраструктура CreateJS JavaScript. CreateJS — это бесплатный набор инструментов, выпущенный по лицензии MIT, который упрощает создание игр на основе спрайтов. Библиотеки CreateJS уже присутствуют в проекте (ищите *js/easeljs-0.8.2.min.js* и *js/preloadjs-0.6.2.min.js* в представлении обозревателя решений). Подробнее о CreateJS можно узнать на [домашней странице CreateJS](https://www.createjs.com).


## <a name="getting-started"></a>Начало работы

Полный исходный код для приложения хранится в репозитории [GitHub](https://github.com/Microsoft/Windows-appsample-get-started-js2d).

Самый простой способ начать с ним работу — перейти в этот репозиторий, нажать зеленую кнопку **Clone or download** (Клонировать или скачать), а затем **Open in Visual Studio** (Открыть в Visual Studio). 

![Клонирование репозитория](images/JS2D_2.png)

Можно также загрузить проект как ZIP-файл или использовать другие стандартные способы работы с [проектами GitHub](https://msdn.microsoft.com/en-us/windows/uwp/get-started/get-uwp-app-samples).

Загрузив проект в Visual Studio, вы увидите несколько файлов, в том числе:

* Images/ — папку, содержащую различные значки, требуемые для приложения UWP, а также лист спрайтов (SpriteSheet) игры и некоторые другие растровые изображения;
* js/ — папку, содержащую файлы JavaScript, где main.js — наша игра, а остальные файлы — EaselJS и PreloadJS;
* index.html — веб-страницу с холстом, на котором размещена графика игры.

Теперь можно запустить игру!

Нажмите клавишу **F5**, чтобы запустить приложение. Вы увидите, в открытом окне, и наши знакомы динозавр положение в ландшафта idyllic (если разреженные). Теперь проанализируем приложение, объясним некоторые важные моменты и по ходу дела добавим остальные функции.

![Самый обычный динозавр с котом-ниндзя на спине](images/JS2D_3.png)

**Примечание.** Что-то пошло не так? Убедитесь, что вы установили Visual Studio с поддержкой веб-страниц. Это можно проверить, создав новый проект. Если JavaScript не поддерживается, нужно будет переустановить Visual Studio, установив флажок *инструменты разработчика Microsoft Web*.

## <a name="walkthough"></a>Пошаговое руководство

Если вы запустили игру с помощью клавиши F5, наверняка вы задаетесь вопросом, что же сейчас происходит. А происходит совсем немного, так как значительная часть кода сейчас закомментирована. Пока вы видите только динозавра и предложение нажать клавишу ПРОБЕЛ, которое, к тому же, не срабатывает. 

### <a name="1-setting-the-stage"></a>1. Настройка рабочей области

Открыв файл **index.html**, вы увидите, что он практически пуст. Этот файл — веб-страница по умолчанию, которая содержит наше приложение. Он выполняет только две важные задачи. Во-первых, он включает исходный код JavaScript для библиотек CreateJS **EaselJS** и **PreloadJS**, а также **main.js** (наш собственный файл исходного кода).
Во-вторых, он определяет тег &lt;canvas&gt; — "холст", на котором будут отображаться все наши графические объекты. &lt;Canvas&gt; — это стандартный компонент документа HTML5. Мы присвоим ему имя (gameCanvas), чтобы наш код в **main.js** мог на него ссылаться. Кстати, если вы собираетесь написать игру на языке JavaScript с нуля, вам также нужно будет скопировать файлы **EaselJS** и **PreloadJS** в свой проект, а затем создать объект canvas.

EaselJS предоставляет нам новый объект под названием *stage* (сцена). Он связан с объектом canvas и используется для отображения текста и изображений. Любой объект, который должен отображаться на сцене, сначала нужно добавить как дочерний элемент сцены следующим образом:

```
    stage.addChild(myObject);
```

Эту строку кода можно будет увидеть в **main.js** несколько раз.

Кстати говоря, пришло время открыть **main.js**.

### <a name="2-loading-the-bitmaps"></a>2. Загрузка точечные рисунки

EaselJS предоставляет нам несколько различных типов графических объектов. Мы можем создавать простые фигуры (такие, как синий прямоугольник — небо), растровые изображения (например, облака, которые мы собираемся добавить), текстовые объекты и спрайты. Спрайтами использовать (SpriteSheet) [https://createjs.com/docs/easeljs/classes/SpriteSheet.html]: один растровое изображение, содержащее несколько образов. Например, этот SpriteSheet нужен для хранения разных кадров анимации с динозавром:

![Лист спрайтов с ходящим динозавром](images/JS2D_4.png)

Мы делаем так, чтобы динозавр ходил. Для этого определяем различные кадры и частоту их смены в этом коде:

```
    // Define the animated dino walk using a spritesheet of images,
    // and also a standing-still state, and a knocked-over state.
    var data = {
        images: [loader.getResult("dino")],
        frames: { width: 373, height: 256 },
        animations: {
            stand: 0,
            lying: { 
                frames: [0, 1],
                speed: 0.1
            },
            walk: {
                frames: [0, 1, 2, 3, 2, 1],
                speed: 0.4
            }
        }
    }

    var spriteSheet = new createjs.SpriteSheet(data);
    dino_walk = new createjs.Sprite(spriteSheet, "walk");
    dino_stand = new createjs.Sprite(spriteSheet, "stand");
    dino_lying = new createjs.Sprite(spriteSheet, "lying");

```

А теперь добавим к сцене несколько маленьких пушистых облаков. Они будут плыть по экрану во время игры. Изображение облака уже содержится в проекте, в папке *images*.

Просмотрите файл **main.js** и найдите в нем функцию **init()**. Она вызывается при запуске игры. С нее мы и начнем настройку всех графических объектов.

Найдите следующий код и удалить комментарии (\\) из строки, обращающуюся к образу облака.

```
 manifest = [
        { src: "walkingDino-SpriteSheet.png", id: "dino" },
        { src: "barrel.png", id: "barrel" },
        { src: "fluffy-cloud-small.png", id: "cloud" },
    ];
```

JavaScript нуждается в небольшой помощи при загрузке таких ресурсов, как изображения. Поэтому мы используем [LoadQueue](https://www.createjs.com/docs/preloadjs/classes/LoadQueue.html) — функцию библиотеки CreateJS, предназначенную для предварительной загрузки изображений. Мы не можем знать точно, сколько времени займет загрузка изображений, поэтому воспользуемся LoadQueue. Эта функция сообщит нам, когда изображения будут доступны. Для этого сначала мы создадим новый объект, который перечисляет все наши изображения, а затем создадим объект LoadQueue. В коде ниже видно, как настроить вызов функции **loadingComplete()**, когда все готово.

```
    // Now we create a special queue, and finally a handler that is
    // called when they are loaded. The queue object is provided by preloadjs.

    loader = new createjs.LoadQueue(false);
    loader.addEventListener("complete", loadingComplete);
    loader.loadManifest(manifest, true, "../images/");
```    

Когда функция **loadingComplete()** вызвана, это значит, что изображения загружены и готовы к использованию. Вы увидите закомментированный раздел для создания облаков; теперь их растровое изображение доступно. Удалите комментарии, чтобы он выглядел следующим образом:

```
    // Create some clouds to drift by..
    for (var i = 0; i < 3; i++) {
        cloud[i] = new createjs.Bitmap(loader.getResult("cloud"));
        cloud[i].x = Math.random()*1024; // Random start location
        cloud[i].y = 64 + i * 48;
        stage.addChild(cloud[i]);
    }
```
Этот код создает три объекта-облака с помощью нашего предварительно загруженного изображения, определяет их расположение и добавляет к сцене.

Запустите приложение снова (нажав клавишу F5), и вы увидите, что на небе появились облака.

### <a name="3-moving-the-clouds"></a>3. Перемещение облака

Теперь сделаем так, чтобы облака плыли. Секрет перемещения облаков — да и чего угодно — заключается в использовании функции [ticker](https://www.createjs.com/docs/easeljs/classes/Ticker.html), которая вызывается много раз за секунду. Каждый раз при вызове этой функции она перерисовывает графику, немного сдвигая ее расположение.

<p data-height="500" data-theme-id="23761" data-slug-hash="vxZVRK" data-default-tab="result" data-user="MicrosoftEdgeDocumentation" data-embed-version="2" data-pen-title="CreateJS - Animating clouds" data-preview="true" data-editable="true" class="codepen">См. запись <a href="https://codepen.io/MicrosoftEdgeDocumentation/pen/vxZVRK/">CreateJS — Animating clouds</a> от Microsoft Edge Docs (<a href="https://codepen.io/MicrosoftEdgeDocumentation">@MicrosoftEdgeDocumentation</a>) на <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://production-assets.codepen.io/assets/embed/ei.js"></script>
  Код для этого уже **main.js** файл, предоставляемые библиотекой CreateJS помощью EaselJS. Он выглядит следующим образом:

```
    // Set up the game loop and keyboard handler.
    // The keyword 'tick' is required to automatically animated the sprite.
    createjs.Ticker.timingMode = createjs.Ticker.RAF;
    createjs.Ticker.addEventListener("tick", gameLoop);
```

Этот код вызывает функцию **gameLoop()** с частотой от 30 до 60 кадров в секунду. Точная скорость зависит от скорости компьютера.

Найдите функцию **gameLoop()**, и немного ниже вы увидите функцию под названием **animateClouds()**. Отредактируйте ее так, чтобы она не была закомментирована.

```
    // Move clouds
    animateClouds();
```

Взглянув на определение этой функции, вы увидите, что она по очереди изменяет координаты каждого облака по оси x. Если x-координата оказывается за границей экрана, она перемещается на крайнюю правую его сторону. К тому же, каждое облако передвигается с немного другой скоростью.

```
function animate_clouds()
{
    // Move the cloud sprites across the sky. If they get to the left edge, 
    // move them over to the right.

    for (var i = 0; i < 3; i++) {    
        cloud[i].x = cloud[i].x - (i+1);
        if (cloud[i].x < -128)
            cloud[i].x = width + 128;
    }
}
```

Теперь, запустив приложение, вы увидите, что облака уже плывут по небу. Наконец у нас появилось движение!

### <a name="4-adding-keyboard-and-mouse-input"></a>4. Добавление клавиатуру и мышь

Игра, с которой вы не можете взаимодействовать, — это вовсе и не игра. Поэтому мы позволим игроку выполнять какие-то действия с помощью клавиатуры или мыши. Вернувшись к функции **loadingComplete()**, вы увидите следующее. Удалите комментарии.

```
    // This code will call the method 'keyboardPressed' is the user presses a key.
    this.document.onkeydown = keyboardPressed;

    // Add support for mouse clicks
    stage.on("stagemousedown", mouseClicked);
```

Теперь у нас есть две функции, которые вызываются всякий раз, когда игрок нажимает клавишу или щелкает мышью. Оба события вызывают **userDidSomething()**  — функцию, которая анализирует переменную gamestate и по ней определяет, что сейчас происходит в игре и что должно случиться в результате.

Gamestate — конструктивный шаблон, который широко используется в играх. Все события происходят в функции **gameLoop()**, которая вызывается таймером функции ticker. GameLoop() с помощью переменной отслеживает состояния игры, определенные разработчиком: запущена, завершена, готова к запуску или другое. Эта переменная состояния проверяется в операторе switch, который определяет, какие другие функции нужно вызвать. Если состояние игры — "запущена", будут вызваны функции, которые отвечают за прыжки динозавра и движение бочек. Если динозавр погиб, переменная gamestate обретет значение "игра завершена", и на экране появится сообщение "Конец игры". Если вас интересуют конструктивные шаблоны, вам поможет книга [Game Programming Patterns](https://gameprogrammingpatterns.com/).

Запустите приложение еще раз, и вы наконец-то сможете приступить к игре. Нажмите клавишу ПРОБЕЛ (или щелкните мышью либо коснитесь экрана), чтобы в игре что-то начало происходить. 

Вы увидите котящиеся бочки. Нажимайте ПРОБЕЛ или щелкайте мышью в нужный момент, чтобы динозавр подпрыгивал. Если вы не рассчитали время — игра закончена.

Бочка анимируется так же, как и облака (хоть и двигается с каждым разом быстрее), и мы каждый раз проверяем положения динозавра и бочки, не столкнулись ли они между собою:

```
 // Very simple check for collision between dino and barrel
                if ((barrel.x > 220 && barrel.x < 380)
                    &&
                    (!jumping))
                {
                    barrel.x = 380;
                    GameState = GameStateEnum.GameOver;
                }
```

Если динозавр не подпрыгнул и бочка уже возле него, код изменяет состояние переменной, присваивая ему значение, которое мы назвали *GameOver*. Несложно догадаться, что *GameOver* останавливает игру.

Итак, основной механизм нашей игры готов.

### <a name="5-resizing-support"></a>5. Поддержка изменения размера

Мы почти закончили! Осталось последнее — устранить одну досадную проблему. Попробуйте изменить размер окна во время игры. Вы увидите, что очень быстро на экране восцаряет хаос — объекты оказываются не там, где нужно. Мы можем позаботиться об этом с помощью обработчика для события изменения размера окна. Он будет создаваться, когда игрок меняет размер окна или ориентация экрана устройства меняется с альбомной на книжную либо наоборот.

Соответствующий код для этого у нас уже есть. По сути, мы его вызываем при первоначальном запуске игры, чтобы удостовериться, что срабатывает размер окна по умолчанию. Ведь мы никогда не можем быть уверены, на каком экране будет запущено приложение UWP.

Просто раскомментируйте эту строку, чтобы вызывать функцию при событиях изменения размера экрана:

```
    // This code makes the app call the method 'resizeGameWindow' if the user resizes the current window.
     window.addEventListener('resize', resizeGameWindow);
```

Если вы снова запустите приложение, то уже сможете успешно менять размер окна.

## <a name="publishing-to-the-microsoft-store"></a>Публикация в Microsoft Store

Теперь у вас есть приложение UWP, можно опубликовать его в Microsoft Store, (при условии, что имеют улучшенную сначала!) 

Для этого нужно выполнить несколько шагов.

1. [Зарегистрируйтесь](https://developer.microsoft.com/en-us/store/register) в качестве разработчика приложений для Windows.
2. Заполните [контрольный список](https://msdn.microsoft.com/windows/uwp/publish/app-submissions) для отправки приложения.
3. Отправьте приложение на [сертификацию](https://msdn.microsoft.com/windows/uwp/publish/the-app-certification-process).

Дополнительные сведения см. в разделе [публикации приложения UWP](https://developer.microsoft.com/en-us/store/publish-apps).

## <a name="suggestions-for-other-features"></a>Идеи для расширения возможностей

Что делать дальше? Вот несколько идей, какие еще возможности можно добавить к вашему приложению (которое, конечно же, вскоре станет бешено популярным!).

1. Подключите звуковые эффекты. Библиотека CreateJS включает поддержку звука через библиотеку под названием [SoundJS](https://www.createjs.com/soundjs).
2. Добавьте поддержку геймпада. Для этого [доступен специальный API](https://gamedevelopment.tutsplus.com/tutorials/using-the-html5-gamepad-api-to-add-controller-support-to-browser-games--cms-21345).
3. Сделайте игру намного, намного лучше! Тут все зависит только от вас. А в Интернете вы найдете множество полезных ресурсов, которые вам помогут. 

## <a name="other-links"></a>Прочие ссылки

* [Создать простой игры Windows с JavaScript](https://www.sitepoint.com/creating-a-simple-windows-8-game-with-javascript-game-basics-createjseaseljs/)
* [Выбор механизма игр HTML/JS](https://html5gameengine.com/)
* [Использование CreateJS в вашей JS на основе игры](https://blogs.msdn.microsoft.com/cbowen/2012/09/19/using-createjs-in-your-javascript-based-windows-8-game/)
* [Курсы по разработке игр на LinkedIn Learning](https://www.linkedin.com/learning/topics/game-development)
