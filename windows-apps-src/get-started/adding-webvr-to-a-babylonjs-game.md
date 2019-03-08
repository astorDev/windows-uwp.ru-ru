---
title: Добавление поддержки WebVR в трехмерную игру Babylon.js
description: Узнайте, как добавить поддержку WebVR в существующую трехмерную игру Babylon.js.
ms.date: 11/29/2017
ms.topic: article
keywords: webvr, edge, веб-разработка, babylon, babylonjs, babylon.js, javascript
ms.localizationpriority: medium
ms.openlocfilehash: 1d8029752790e19adc5eb4266615372fb346e001
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57638559"
---
# <a name="adding-webvr-support-to-a-3d-babylonjs-game"></a>Добавление поддержки WebVR в трехмерную игру Babylon.js

Если вы создали трехмерную игру с помощью Babylon.js и считаете, что она может отлично смотреться в виртуальной реальности (VR), выполните простые действия, описанные в этом учебнике, чтобы воплотить это в реальность.

Мы добавим поддержку WebVR для игры, показанной ниже. Продолжайте и включите контроллер Xbox, чтобы опробовать ее!


<iframe height='300' scrolling='no' title='Игра Дино Babylon.js, с помощью Babylon.GUI' src='//codepen.io/MicrosoftEdgeDocumentation/embed/preview/wrOvoj/?height=300&theme-id=23761&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>См. в разделе пера <a href='https://codepen.io/MicrosoftEdgeDocumentation/pen/wrOvoj/'>Babylon.js Дино игры с помощью Babylon.GUI</a> , документы Microsoft Edge (<a href='https://codepen.io/MicrosoftEdgeDocumentation'>@MicrosoftEdgeDocumentation</a>) на <a href='https://codepen.io'>CodePen</a>.
</iframe>

Это игра с трехмерной графикой, которая хорошо подходит для плоских экранов, но как обстоят дела с VR?
В этом руководстве мы рассмотрим несколько шагов, необходимых, чтобы это работало с WebVR. Мы будем использовать гарнитуру [Windows Mixed Reality](https://developer.microsoft.com/en-us/windows/mixed-reality), способную использовать поддержку WebVR в Microsoft Edge. После применения этих изменений к игре можно ожидать, что она также будет работать в других комбинациях браузера и гарнитуры, которые поддерживают WebVR.



## <a name="prerequisites"></a>Предварительные условия

- Текстовый редактор (например, [Visual Studio Code](https://code.visualstudio.com/download))
- Контроллер Xbox, подключенный к компьютеру
- Windows 10 Creators Update
- Компьютер с [минимальными требуемыми характеристиками для запуска Windows Mixed Reality](https://developer.microsoft.com/en-us/windows/mixed-reality/immersive_headset_setup)
- Устройство Windows Mixed Reality (необязательно) 



## <a name="getting-started"></a>Начало работы

Самый простой способ начать работу — перейти в [репозиторий GitHub Windows-tutorials-web](https://github.com/Microsoft/Windows-tutorials-web), нажать зеленую кнопку **Клонировать или скачать**, а затем **Открыть в Visual Studio**.

![Кнопка "Клонировать или скачать"](images/3dclone.png)

Если вы не хотите клонировать проект, его можно скачать в виде ZIP-файла.
У вас будет две папки [до](https://github.com/Microsoft/Windows-tutorials-web/tree/master/BabylonJS-game-with-WebVR/before) и [после](https://github.com/Microsoft/Windows-tutorials-web/tree/master/BabylonJS-game-with-WebVR/after). Папка "до" является нашей игрой до добавления функций VR, а папка "после" – законченной игрой с поддержкой VR.

Папки "До" и "После" папки содержат следующие файлы:
-   **текстуры /** — папка, содержащая все образы, используемых в игре.
-   **CSS /** — папка, содержащая код CSS для игры.
-   **js /** — папка, содержащая файлы JavaScript. Файл main.js — это сама игра, а другие файлы — используемые библиотеки.
-   **модели /** — папка, содержащая трехмерных моделей. Для этой игры используется лишь одна модель — модель динозавра.
-   **index.HTML** -веб-страницы, на котором размещена игры модуля подготовки отчетов. Открытие этой страницы в Microsoft Edge запускает игры.

Обе версии игры можно проверить, открыв их файлы соответствующих index.html в Microsoft Edge.



## <a name="the-mixed-reality-portal"></a>Портал смешанной реальности

Если вы не знакомы с Windows Mixed Reality и на вашем компьютере установлено обновление Windows 10 Creators Update с совместимой графической картой, попробуйте открыть приложение **Портал смешанной реальности** из меню "Пуск" в Windows 10.

![Служба поиска портала смешанной реальности](images/mixed-reality-portal.png)

Если выполнены все требования, можно включить функции разработчика и имитировать гарнитуру Windows Mixed Reality, подсоединенную к компьютеру. Если вам повезло и у вас есть реальная гарнитура, подключите ее и запустите программу установки.

> [!IMPORTANT]
> Портал смешанной реальности должен быть открыт все время, пока вы проходите обучение с помощью этого руководства.

Теперь вы готовы добиться WebVR с помощью Microsoft Edge.

## <a name="2d-ui-in-a-virtual-world"></a>Двухмерный пользовательский интерфейс в виртуальном мире

>[!NOTE]
> Захватите [ **перед** ](https://github.com/Microsoft/Windows-tutorials-web/tree/master/BabylonJS-game-with-WebVR/before) папку для получения начального образца.

[Babylon.GUI](https://doc.babylonjs.com/how_to/gui) — это библиотека с поддержкой виртуальной Реальности, позволяя создавать простые, интерактивных пользовательских интерфейсов, хорошо подходят для виртуальной Реальности и не VR отображает.
Это расширение для Babylon.js `GUI` библиотека — throuhout используется пример приложения для создания 2D элементов.


2D текст `GUI` элемент могут создаваться с помощью нескольких строк в зависимости от количества атрибутов, которые вы хотите изменить.
В следующем фрагменте кода уже находится в наших [ **перед** ](https://github.com/Microsoft/Windows-tutorials-web/tree/master/BabylonJS-game-with-WebVR/before) пример, но давайте Пошаговое руководство, что происходит.
Сначала мы предоставляем [ `AdvancedDynamicTexture` ](https://doc.babylonjs.com/how_to/gui#advanceddynamictexture) объект для формирования графического пользовательского интерфейса, которые будут рассмотрены. Образец устанавливает для `CreateFullScreenUI()`, то есть наш пользовательский Интерфейс будет охватывают весь экран. С помощью `AdvancedDynamicTexture` создан, затем выполним 2D текстовое поле, которое отображается при запуске игр с помощью `GUI.Rectanlge()` и `GUI.TextBlock()`.


Этот код добавляется в [ **main.js**](https://github.com/Microsoft/Windows-tutorials-web/blob/master/BabylonJS-game-with-WebVR/before/js/main.js#L157-L168).
```javascript
// GUI
var advancedTexture = BABYLON.GUI.AdvancedDynamicTexture.CreateFullscreenUI("UI");

// Start UI
startUI = new BABYLON.GUI.Rectangle("start");
startUI.background = "black"
startUI.alpha = .8;
startUI.thickness = 0;
startUI.height = "60px";
startUI.width = "400px";
advancedTexture.addControl(startUI); 
var tex2 = new BABYLON.GUI.TextBlock();
tex2.text = "Stay away from the dinosaur! \n Plug in an Xbox controller and press A to start";
tex2.color = "white";
startUI.addControl(tex2); 
```


Этот пользовательский Интерфейс отображается, когда создан, но можно включить или отключить с помощью `isVisible` в зависимости от того, что происходит в игре.
```javascript
startUI.isVisible = false;
```



## <a name="detecting-headsets"></a>Определение гарнитур

Рекомендуется для приложений виртуальной Реальности иметь два вида камеры, чтобы несколько сценариев может поддерживаться. Для этой игры мы будем поддерживать одну камеру, для которой требуется подключить работающую гарнитуру, и еще одну камеру, которая не использует гарнитуру. Чтобы определить, какую из них будет использовать игра, сначала необходимо проверить, можно ли обнаружить гарнитуру. Чтобы сделать это, мы будем использовать [ `navigator.getVRDisplays()` ](https://developer.mozilla.org/en-US/docs/Web/API/Navigator/getVRDisplays).


Добавьте код, приведенный выше `window.addEventListener('DOMContentLoaded')` в **main.js**.
```javascript
var headset;
// If a VR headset is connected, get its info
navigator.getVRDisplays().then(function (displays) {
    if (displays[0]) {
        headset = displays[0];
    }
});
```

С использованием данных, хранящихся в переменной `headset`, мы теперь можем выбрать камеру, которая подходит для пользователя.


## <a name="creating-and-selecting-the-initial-camera"></a>Создание и Выбор начальной камеры

С помощью Babylon.js, WebVR могут добавляться быстро с помощью [ `WebVRFreeCamera` ](https://doc.babylonjs.com/classes/3.1/webvrfreecamera). Эта камера может принять ввод с клавиатуры и позволяет вам использовать гарнитуру VR для управления вращением вашей "головой".


### <a name="step-1-checking-for-headsets"></a>Шаг 1. Проверка применяется

Для наших резервный камеры, мы будем использовать [ `UniversalCamera` ](https://doc.babylonjs.com/classes/3.1/universalcamera) , используемого в данный момент в оригинальную игру.

Мы проверим, наши `headset` переменной, можно определить, может ли мы использовать `WebVRFreeCamera` камеры.

Замените `camera = new BABYLON.UniversalCamera("Camera", new BABYLON.Vector3(0, 18, -45), scene);` следующим кодом.
```javascript
        if(headset){
            // Create a WebVR camera with the trackPosition property set to false so that we can control movement with the gamepad
            camera = new BABYLON.WebVRFreeCamera("vrcamera", new BABYLON.Vector3(0, 14, 0), scene, true, { trackPosition: false });
            camera.deviceScaleFactor = 1;
        } else {
            // No headset, use universal camera
            camera = new BABYLON.UniversalCamera("camera", new BABYLON.Vector3(0, 18, -45), scene);
        }
```


### <a name="step-2-activating-the-webvrfreecamera"></a>Шаг 2. Активация WebVRFreeCamera
Для активации этой камеры в большинстве браузеров пользователю необходимо выполнить некоторое взаимодействие, которое запрашивает виртуальный интерфейс.
Мы будем подключить эту функцию до щелчка кнопкой мыши.


Вставьте код в функцию  `createScene()` после `camera.applyGravity = true;`.
```javascript
        scene.onPointerDown = function () {
            scene.onPointerDown = undefined
            camera.attachControl(canvas, true);
        }
```

Щелчок в игре теперь создает запрос, как показано ниже, или отображает игры в гарнитура прямо сейчас, если пользователь уже принял запрос перед.

![иммерсивный запрос](images/immersiveview.png)

Мы также можем добавить фрагмент кода, который будет отображать `UniversalCamera` просмотреть, прежде чем мы переключиться наших `WebVRFreeCamera`, позволяя пользователю просмотрите игры вместо синий окна. 

Добавьте следующий код после `engine.runRenderLoop(function () {`.
```javascript
            if (headset) {
                if (!(headset.isPresenting)) {
                    var camera2 = new BABYLON.UniversalCamera("Camera", new BABYLON.Vector3(0, 18, -45), scene);
                    scene.activeCamera = camera2;
                } else {
                    scene.activeCamera = camera;
                }
            }
```

### <a name="step-3-adding-gamepad-support"></a>Шаг 3. Добавление поддержки геймпада

Так как `WebVRFreeCamera` игровые планшеты, изначально не поддерживает мы сопоставим наши кнопки игровой клавиши со стрелками. Мы сделаем это с углубиться в `inputs` свойство камеры. После добавления соответствующих кодов для движений левого аналогового джойстика вверх, вниз, влево и вправо для сопоставления с клавишами со стрелками наш игровой контроллер снова работает.


Добавьте следующий код ниже `scene.onPointerDown = function() {...}` вызова.
``` javascript
    // Custom input, adding Xbox controller support for left analog stick to map to keyboard arrows
    camera.inputs.attached.keyboard.keysUp.push(211);    // Left analog up
    camera.inputs.attached.keyboard.keysDown.push(212);  // Left analog down
    camera.inputs.attached.keyboard.keysLeft.push(214);  // Left analog left
    camera.inputs.attached.keyboard.keysRight.push(213); // Left analog right
```


### <a name="step-4-give-it-a-try"></a>Шаг 4. Попробуйте!

Если мы откроем **index.html** с нашими гарнитуры и игровой контроллер подсоединено слева щелкните синий окна игры будет переключиться в нашей игре VR режим! Продолжите и наденьте гарнитуру, чтобы проверить результаты. 


<iframe height='300' scrolling='no' title='Игра Дино Babylon.js, с помощью Babylon.GUI - WebVR' src='//codepen.io/MicrosoftEdgeDocumentation/embed/preview/RjgpJd/?height=300&theme-id=23761&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>См. в разделе пера <a href='https://codepen.io/MicrosoftEdgeDocumentation/pen/RjgpJd/'>Babylon.js Дино игры с помощью Babylon.GUI - WebVR</a> , документы Microsoft Edge (<a href='https://codepen.io/MicrosoftEdgeDocumentation'>@MicrosoftEdgeDocumentation</a>) на <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="conclusion"></a>Заключение

Поздравляем! Теперь у вас есть полная игра Babylon.js с поддержкой WebVR. Теперь вы можете воспользоваться полученными знаниями, чтобы создать игру еще лучше или использовать эту игру.
