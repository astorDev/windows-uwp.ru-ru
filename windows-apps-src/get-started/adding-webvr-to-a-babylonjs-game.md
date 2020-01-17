---
title: Добавление поддержки WebVR в трехмерную игру Babylon.js
description: Узнайте, как добавить поддержку WebVR в существующую трехмерную игру Babylon.js.
ms.date: 11/29/2017
ms.topic: article
keywords: webvr, edge, веб-разработка, babylon, babylonjs, babylon.js, javascript
ms.localizationpriority: medium
ms.openlocfilehash: ff350f8ce08f566b8c95c3c46faad330923e4b2e
ms.sourcegitcommit: 26bb75084b9d2d2b4a76d4aa131066e8da716679
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/06/2020
ms.locfileid: "75685203"
---
# <a name="adding-webvr-support-to-a-3d-babylonjs-game"></a>Добавление поддержки WebVR в трехмерную игру Babylon.js

Если вы создали трехмерную игру с помощью Babylon.js и считаете, что она может отлично смотреться в виртуальной реальности (VR), выполните простые действия, описанные в этом руководстве, чтобы воплотить это в реальность.

В этом руководстве описано, как настроить и запустить трехмерную игру с использованием WebVR. Мы будем использовать гарнитуру [Windows Mixed Reality](https://developer.microsoft.com/mixed-reality), способную использовать поддержку WebVR в Microsoft Edge. После применения этих изменений к игре можно ожидать, что она также будет работать в других комбинациях браузера и гарнитуры, которые поддерживают WebVR.



## <a name="prerequisites"></a>Предварительные условия

- Текстовый редактор (например, [Visual Studio Code](https://code.visualstudio.com/download)).
- Контроллер Xbox, подключенный к компьютеру.
- Windows 10 Creators Update.
- Компьютер с [минимальными требуемыми характеристиками для запуска Windows Mixed Reality](https://developer.microsoft.com/windows/mixed-reality/immersive_headset_setup).
- Устройство Windows Mixed Reality (необязательно). 



## <a name="getting-started"></a>Начало работы

Самый простой способ начать работу — перейти в [репозиторий GitHub Windows-tutorials-web](https://github.com/Microsoft/Windows-tutorials-web), нажать зеленую кнопку **Clone or download** (Клонировать или скачать), а затем выбрать **Open in Visual Studio** (Открыть в Visual Studio).

![Кнопка "Clone or download" (Клонировать или скачать)](images/3dclone.png)

Если вы не хотите клонировать проект, его можно скачать в виде ZIP-файла.
У вас будет две папки [before](https://github.com/Microsoft/Windows-tutorials-web/tree/master/BabylonJS-game-with-WebVR/before) и [after](https://github.com/Microsoft/Windows-tutorials-web/tree/master/BabylonJS-game-with-WebVR/after). Папка "before" является нашей игрой до добавления функций виртуальной реальности, а папка "after" — готовой игрой с поддержкой виртуальной реальности.

Папки "before" и "after" содержат следующие файлы:
-   **textures/** — папка, содержащая все изображения, используемые в игре.
-   **css/** — папка, содержащая таблицы стилей CSS для игры.
-   **js/**  — папка, содержащая файлы JavaScript. Файл main.js — это сама игра, а другие файлы — используемые библиотеки.
-   **models/**  — папка с трехмерными моделями. Для этой игры используется лишь одна модель — модель динозавра.
-   **index.html** — веб-страница, на которой размещен отрисовщик игры. Открытие этой страницы в Microsoft Edge запускает игру.

Обе версии игры можно проверить, открыв их соответствующие файлы index.html в Microsoft Edge.



## <a name="the-mixed-reality-portal"></a>Портал смешанной реальности

Если вы не знакомы с Windows Mixed Reality и на вашем компьютере с совместимой графической картой установлено обновление Windows 10 Creators Update, попробуйте открыть приложение **Портал смешанной реальности** из меню "Пуск" в Windows 10.

![Служба поиска портала смешанной реальности](images/mixed-reality-portal.png)

Если выполнены все требования, можно включить функции разработчика и имитировать гарнитуру Windows Mixed Reality, подсоединенную к компьютеру. Если вам повезло и у вас есть реальная гарнитура, подключите ее и запустите программу установки.

> [!IMPORTANT]
> Портал смешанной реальности должен быть открыт все время, пока вы проходите обучение с помощью этого руководства.

Теперь вы готовы к взаимодействию WebVR с Microsoft Edge.

## <a name="2d-ui-in-a-virtual-world"></a>Двухмерный пользовательский интерфейс в виртуальном мире

>[!NOTE]
> Захватите папку [**before**](https://github.com/Microsoft/Windows-tutorials-web/tree/master/BabylonJS-game-with-WebVR/before), чтобы получить начальный код.

[Babylon.GUI](https://doc.babylonjs.com/how_to/gui) — это библиотека с поддержкой виртуальной реальности, которая позволяет создавать простые интерактивные пользовательские интерфейсы, хорошо подходящие для дисплеев с поддержкой виртуальной реальности и обычных дисплеев.
Библиотека `GUI` — это расширение для Babylon.js. Она используется в примере для создания двухмерных элементов.


Двухмерный текстовый элемент `GUI` можно создать с помощью нескольких строк, в зависимости от количества атрибутов, которые вы хотите настроить.
Следующий фрагмент кода уже находится в папке [**before**](https://github.com/Microsoft/Windows-tutorials-web/tree/master/BabylonJS-game-with-WebVR/before) примера, но давайте по шагам рассмотрим, что происходит.
Сначала мы создадим объект [`AdvancedDynamicTexture`](https://doc.babylonjs.com/how_to/gui#advanceddynamictexture), чтобы определить область наложения графического пользовательского интерфейса. Пример задает значение `CreateFullScreenUI()`, то есть наш пользовательский интерфейс будет охватывать весь экран. После `AdvancedDynamicTexture` создадим двухмерное текстовое поле, которое отображается при запуске игр с помощью `GUI.Rectanlge()` и `GUI.TextBlock()`.


Этот код добавляется в [**main.js**](https://github.com/Microsoft/Windows-tutorials-web/blob/master/BabylonJS-game-with-WebVR/before/js/main.js#L157-L168).
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


Этот пользовательский интерфейс отображается после создания, но его можно включить или отключить с помощью `isVisible`, в зависимости от того, что происходит в игре.
```javascript
startUI.isVisible = false;
```



## <a name="detecting-headsets"></a>Определение гарнитур

Для приложений виртуальной реальности рекомендуется использовать два типа камер, чтобы можно было поддерживать несколько сценариев. Для этой игры мы будем поддерживать одну камеру, для которой требуется подключить работающую гарнитуру, и еще одну камеру, которая не использует гарнитуру. Чтобы определить, какую из них будет использовать игра, сначала необходимо проверить, можно ли обнаружить гарнитуру. Чтобы сделать это, мы используем [`navigator.getVRDisplays()`](https://developer.mozilla.org/en-US/docs/Web/API/Navigator/getVRDisplays).


Добавьте этот код над `window.addEventListener('DOMContentLoaded')` в **main.js**.
```javascript
var headset;
// If a VR headset is connected, get its info
navigator.getVRDisplays().then(function (displays) {
    if (displays[0]) {
        headset = displays[0];
    }
});
```

Используя данные, хранящиеся в переменной `headset`, мы теперь можем выбрать камеру, которая подходит для пользователя.


## <a name="creating-and-selecting-the-initial-camera"></a>Создание и выбор начальной камеры

Благодаря Babylon.js можно быстро добавить WebVR, воспользовавшись [`WebVRFreeCamera`](https://doc.babylonjs.com/api/classes/babylon.webvrfreecamera). Эта камера может принять ввод с клавиатуры и позволяет использовать гарнитуру виртуальной реальности для управления вращением вашей "головой".


### <a name="step-1-checking-for-headsets"></a>Шаг 1. Проверка наличия гарнитуры

Для нашей резервной камеры мы будем использовать [`UniversalCamera`](https://doc.babylonjs.com/api/classes/babylon.universalcamera), которая используется в данный момент в исходной игре.

Мы проверим нашу переменную `headset`, чтобы определить, можем ли мы использовать камеру `WebVRFreeCamera`.

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
Мы привяжем эту функцию к щелчку кнопкой мыши.


Вставьте код в функцию `createScene()` после `camera.applyGravity = true;`.
```javascript
        scene.onPointerDown = function () {
            scene.onPointerDown = undefined
            camera.attachControl(canvas, true);
        }
```

Теперь щелчок в игре создает запрос, который выглядит, как показано ниже, или отображает игру в гарнитуре сразу, если пользователь ответил на запрос ранее.

![Иммерсивный запрос](images/immersiveview.png)

Мы также можем добавить фрагмент кода, который будет отображать представление `UniversalCamera` прежде, чем мы переключимся на `WebVRFreeCamera`, позволяя пользователю смотреть на игру, а не на синее окно. 

Добавьте приведенный ниже код после `engine.runRenderLoop(function () {`.
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

Так как `WebVRFreeCamera` изначально не поддерживает геймпады, мы изначально сопоставим кнопки нашего геймпада с клавишами со стрелками на клавиатуре. Мы сделаем это, углубившись в свойство камеры `inputs`. После добавления соответствующих кодов для движений левого аналогового джойстика вверх, вниз, влево и вправо для сопоставления с клавишами со стрелками наш игровой контроллер снова работает.


Добавьте следующий код под вызовом `scene.onPointerDown = function() {...}`.
``` javascript
    // Custom input, adding Xbox controller support for left analog stick to map to keyboard arrows
    camera.inputs.attached.keyboard.keysUp.push(211);    // Left analog up
    camera.inputs.attached.keyboard.keysDown.push(212);  // Left analog down
    camera.inputs.attached.keyboard.keysLeft.push(214);  // Left analog left
    camera.inputs.attached.keyboard.keysRight.push(213); // Left analog right
```


### <a name="step-4-give-it-a-try"></a>Шаг 4. Попробуйте!

Если мы откроем **index.html** с помощью гарнитуры и подключенного геймпада, то можем щелкнуть левой кнопки мыши голубое окно игры, чтобы переключить ее в режим виртуальной реальности. Продолжите и наденьте гарнитуру, чтобы проверить результаты. 



## <a name="conclusion"></a>Заключение

Поздравляем! Теперь у вас есть полная игра Babylon.js с поддержкой WebVR. Вы можете воспользоваться полученными знаниями, чтобы создать игру еще лучше или использовать эту игру.
