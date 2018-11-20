---
title: Добавление поддержки WebVR в трехмерную игру Babylon.js
description: Узнайте, как добавить поддержку WebVR в существующей 3D Babylon.js игры.
author: abbycar
ms.author: abigailc
ms.date: 11/29/2017
ms.topic: article
keywords: webvr, edge, веб-разработки, babylon, babylonjs, babylon.js, javascript
ms.localizationpriority: medium
ms.openlocfilehash: 72681c3f91fc2dcbfcc4e4531359d6d668e18b80
ms.sourcegitcommit: ed0304b8a214c03b8aab74b8ef12c9f82b8e3c5f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/19/2018
ms.locfileid: "7306734"
---
# <a name="adding-webvr-support-to-a-3d-babylonjs-game"></a>Добавление поддержки WebVR в трехмерную игру Babylon.js

Если вы создали трехмерной игры с Babylon.js и думал, что он может отлично выглядят в виртуальной реальности (VR), выполните простых действий в этом руководстве, чтобы убедиться, что реальностью.

Мы добавим поддержку WebVR в игру, показано ниже. Подключите контроллера Xbox, чтобы опробовать его и перейти к!


<iframe height='300' scrolling='no' title='Игру Babylon.js динозавр, с помощью Babylon.GUI' src='//codepen.io/MicrosoftEdgeDocumentation/embed/preview/wrOvoj/?height=300&theme-id=23761&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>См. в разделе пера <a href='https://codepen.io/MicrosoftEdgeDocumentation/pen/wrOvoj/'>игру Babylon.js динозавр, с помощью Babylon.GUI</a> от Microsoft Edge Docs (<a href='https://codepen.io/MicrosoftEdgeDocumentation'>@MicrosoftEdgeDocumentation</a>) на <a href='https://codepen.io'>CodePen</a>.
</iframe>

Это трехмерной игры, которая подходит и для плоский экран, но о в VR?
В этом руководстве мы ознакомитесь несколько шагов он занимает для получения этого, работающие с помощью WebVR. Мы будем использовать гарнитуры [Смешанной реальности](https://developer.microsoft.com/en-us/windows/mixed-reality) , которые могут проникать в добавлена поддержка WebVR в Microsoft Edge. После применения этих изменений в игру, вы можете ожидать, также будет работать в сочетаний браузера и гарнитуры, которые поддерживают WebVR.



## <a name="prerequisites"></a>Необходимые условия

- Текстовом редакторе (например, [Visual Studio Code](https://code.visualstudio.com/download))
- Контроллером Xbox, подключенный к компьютеру
- Windows 10 Creators Update
- Компьютер с [минимальным требуется спецификации для запуска Windows Mixed Reality](https://developer.microsoft.com/en-us/windows/mixed-reality/immersive_headset_setup)
- Устройство смешанной реальности Windows (необязательно) 



## <a name="getting-started"></a>Начало работы

Самый простой способ, чтобы начать работу — посетить [репозитории GitHub веб Windows учебники](https://github.com/Microsoft/Windows-tutorials-web), нажать зеленую **клонировать или скачать** и выберите команду **Открыть в Visual Studio**.

![Кнопка "Клонировать или скачать"](images/3dclone.png)

Если вы не хотите клонировать проект, его можно скачать в виде ZIP-файла.
Затем вы получите две папки, [до](https://github.com/Microsoft/Windows-tutorials-web/tree/master/BabylonJS-game-with-WebVR/before) и [после](https://github.com/Microsoft/Windows-tutorials-web/tree/master/BabylonJS-game-with-WebVR/after). Папка «до» является нашей игры до любого VR возможности добавляются, и в папку «после» завершении игры с поддержкой VR.

До и после папки содержат следующие файлы:
-   **текстур или** — папку, содержащую всех изображений, используемых в игре.
-   **css /** — папку, содержащую CSS для игры.
-   **js /** — папку, содержащую файлы JavaScript. Файл main.js — наша игра, а остальные файлы — использовать библиотеки.
-   **моделей и** — папка С трехмерными моделями. В данной игре у нас есть только одна модель для динозавра.
-   **index.html** — веб-страницы, на котором размещен обработчик игры. Открыв эту страницу в Microsoft Edge запуска игры.

Вы можете протестировать обе версии игры, открыв их соответствующие index.html файлы в Microsoft Edge.



## <a name="the-mixed-reality-portal"></a>Портала смешанной реальности

Если вы знакомы со смешанной реальности Windows и Windows 10 Creators Update, установленных на компьютере с совместимой графической карты, попробуйте открыть приложение **Портала смешанной реальности** из меню "Пуск" в Windows 10.

![Смешанной реальности портала поиска](images/mixed-reality-portal.png)

Если выполнены все требования, можно включить функции разработчика и имитировать гарнитуры смешанной реальности, питание от сети на компьютере. Если вы являетесь повезло достаточно фактические гарнитуры соседние, подключите его и запустите программу установки.

> [!IMPORTANT]
> Портала смешанной реальности должен быть открыт в любое время с помощью этого учебника.

Теперь вы готовы взаимодействуют с WebVR с Microsoft Edge.

## <a name="2d-ui-in-a-virtual-world"></a>Двумерного пользовательского интерфейса в виртуальном мире

>[!NOTE]
> Привлекайте папку [**перед**](https://github.com/Microsoft/Windows-tutorials-web/tree/master/BabylonJS-game-with-WebVR/before) для получения образце начальные.

[Babylon.GUI](https://doc.babylonjs.com/how_to/gui) — это библиотека, поддержкой VR, позволяет создавать простые, интерактивных пользовательских интерфейсов, хорошо подходят для VR и отображает не VR.
Стандартному расширение Babylon.js `GUI` библиотека — использовать throuhout пример для создания 2D элементов.


Двухмерная текст `GUI` элемент можно создать с помощью нескольких строк в зависимости от того, сколько атрибуты, которые вы хотите изменить.
В следующем фрагменте кода уже находится в нашем примере [**перед**](https://github.com/Microsoft/Windows-tutorials-web/tree/master/BabylonJS-game-with-WebVR/before) , но давайте Пошаговое руководство, что происходит.
Сначала мы делаем [`AdvancedDynamicTexture`](https://doc.babylonjs.com/how_to/gui#advanceddynamictexture) объекта, чтобы установить графического интерфейса пользователя, которые будут рассмотрены. Пример задает это `CreateFullScreenUI()`, то есть пользовательском Интерфейсе будут рассмотрены весь экран. С помощью `AdvancedDynamicTexture` создан, затем внесения 2D текстовое поле, которое отображается при запуске игры с помощью `GUI.Rectanlge()` и `GUI.TextBlock()`.


Этот код добавляется в [**файле main.js**](https://github.com/Microsoft/Windows-tutorials-web/blob/master/BabylonJS-game-with-WebVR/before/js/main.js#L157-L168).
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


Этот пользовательский Интерфейс отображается после создания, но может быть в одном или отключить с помощью `isVisible` в зависимости от того, что происходит в игре.
```javascript
startUI.isVisible = false;
```



## <a name="detecting-headsets"></a>Обнаружение гарнитур

Рекомендуется для приложений VR иметь два типа камеры, таким образом, чтобы несколько сценариев могут поддерживаться. Для этой игры мы будем поддерживает один камеру, которая требует гарнитура подключать работе и другой, использующий не гарнитуры. Чтобы определить, какой из них игра будет использовать, нам потребуется установить флажок для см. в разделе ли гарнитура была обнаружена. Для этого мы будем использовать [`navigator.getVRDisplays()`](https://developer.mozilla.org/en-US/docs/Web/API/Navigator/getVRDisplays).


Добавьте этот код выше `window.addEventListener('DOMContentLoaded')` в **файле main.js**.
```javascript
var headset;
// If a VR headset is connected, get its info
navigator.getVRDisplays().then(function (displays) {
    if (displays[0]) {
        headset = displays[0];
    }
});
```

Данными, хранящиеся в `headset` переменной, мы теперь сможете выбрать камеры, которая подходит для пользователя.


## <a name="creating-and-selecting-the-initial-camera"></a>Создание и выбрав начальной камеры

С помощью Babylon.js, WebVR можно добавить быстро с помощью [`WebVRFreeCamera`](http://doc.babylonjs.com/classes/3.1/webvrfreecamera). Этой камеры может занять ввода с клавиатуры и позволяет использовать гарнитура VR для управления вращением ваше «head».


### <a name="step-1-checking-for-headsets"></a>Шаг 1: Проверка для гарнитур

Для наших резервный камеры, мы будем использовать [`UniversalCamera`](https://doc.babylonjs.com/classes/3.1/universalcamera) , в настоящее время используется в исходном игры.

Мы проверяем наши `headset` переменную, чтобы определить, является ли мы можем использовать `WebVRFreeCamera` камеры.

Замените `camera = new BABYLON.UniversalCamera("Camera", new BABYLON.Vector3(0, 18, -45), scene);` с помощью следующего кода.
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


### <a name="step-2-activating-the-webvrfreecamera"></a>Шаг 2: Активация WebVRFreeCamera
Чтобы активировать этой камеры в большинстве браузеров, пользователю необходимо выполнить некоторые взаимодействия, который запрашивает работу виртуальной.
Мы будем подключить этой функции зависит от щелчка мышью.


Вставьте код в пределах `createScene()` функционировать после `camera.applyGravity = true;` .
```javascript
        scene.onPointerDown = function () {
            scene.onPointerDown = undefined
            camera.attachControl(canvas, true);
        }
```

Нажмите кнопку в игре теперь создаст запрос следующим образом или отображает игры в гарнитуры сразу же, если пользователь принял запрос перед.

![Современные методы строке](images/immersiveview.png)

Кроме того, мы можем добавить фрагмент кода, который будет отображать `UniversalCamera` просмотреть перед мы переход на наших `WebVRFreeCamera`, позволяя пользователю рассмотрим игры вместо синее окно. 

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

### <a name="step-3-adding-gamepad-support"></a>Шаг 3: Добавление поддержки геймпада

Так как `WebVRFreeCamera` не поддерживает изначально геймпадов, мы будем сопоставить наших кнопки геймпада клавиши со стрелками. Это необходимо сделать, углубиться в `inputs` свойства камеры. Добавляя соответствующие коды для левый аналоговый джойстик вверх, вниз, влево и вправо, чтобы соответствовать клавиши со стрелками, наши геймпада — обратно в действии.


Добавьте этот код ниже `scene.onPointerDown = function() {...}` вызова.
``` javascript
    // Custom input, adding Xbox controller support for left analog stick to map to keyboard arrows
    camera.inputs.attached.keyboard.keysUp.push(211);    // Left analog up
    camera.inputs.attached.keyboard.keysDown.push(212);  // Left analog down
    camera.inputs.attached.keyboard.keysLeft.push(214);  // Left analog left
    camera.inputs.attached.keyboard.keysRight.push(213); // Left analog right
```


### <a name="step-4-give-it-a-try"></a>Шаг 4: Попробуйте сделать!

Если мы открыть **index.html** наших гарнитуры и питание игровое устройство управления, левой нажатия синий окно игры будет переключитесь в нашей игре VR режим! Опубликуйте и поместить на вашей гарнитуры для посмотрите на результат. 


<iframe height='300' scrolling='no' title='Игру Babylon.js динозавр, с помощью Babylon.GUI - WebVR' src='//codepen.io/MicrosoftEdgeDocumentation/embed/preview/RjgpJd/?height=300&theme-id=23761&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>См. в разделе пера <a href='https://codepen.io/MicrosoftEdgeDocumentation/pen/RjgpJd/'>игру Babylon.js динозавр, с помощью Babylon.GUI - WebVR</a> с Microsoft Edge Docs (<a href='https://codepen.io/MicrosoftEdgeDocumentation'>@MicrosoftEdgeDocumentation</a>) на <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="conclusion"></a>Заключение

Поздравляем! Теперь у вас есть полный игру Babylon.js с поддержкой WebVR. Здесь вы можете воспользоваться узнали еще лучше игры или выполнять сборку отключать данному.
