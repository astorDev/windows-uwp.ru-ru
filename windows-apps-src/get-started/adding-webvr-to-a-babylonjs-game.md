---
title: Добавление поддержки WebVR 3D игру Babylon.js
description: Узнайте, как добавить поддержку WebVR для существующего трехмерную Babylon.js игру.
author: abbycar
ms.author: abigailc
ms.date: 11/29/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: webvr, пограничного сервера, веб-разработке, babylon, babylonjs, babylon.js, javascript
ms.localizationpriority: medium
ms.openlocfilehash: 41665e8719493bb658f9926947061b1b5f81a139
ms.sourcegitcommit: 897a111e8fc5d38d483800288ad01c523e924ef4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/13/2018
ms.locfileid: "1018654"
---
# <a name="adding-webvr-support-to-a-3d-babylonjs-game"></a>Добавление поддержки WebVR 3D игру Babylon.js

Если вы создали трехмерную игру с Babylon.js и представить, что он может выглядеть хорошие в виртуальной реальности (VR), выполните действия в этом руководстве, чтобы убедиться, что в реальность.

Мы добавим поддержка WebVR игру, показано ниже. Пойти дальше и подключите контроллером Xbox пришло!


<iframe height='300' scrolling='no' title='Игра Дино Babylon.js с помощью Babylon.GUI' src='//codepen.io/MicrosoftEdgeDocumentation/embed/preview/wrOvoj/?height=300&theme-id=23761&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Просмотреть перо <a href='https://codepen.io/MicrosoftEdgeDocumentation/pen/wrOvoj/'>игру Дино Babylon.js с помощью Babylon.GUI</a> с пограничного сервера: документы Microsoft (<a href='https://codepen.io/MicrosoftEdgeDocumentation'>@MicrosoftEdgeDocumentation</a>) на <a href='https://codepen.io'>CodePen</a>.
</iframe>

Это трехмерную игру, которая работает на плоской экрана, но что о в VR?
В этом руководстве мы рассмотрим несколько действий его принимает для получения это и работать с WebVR. Мы будем использовать гарнитуры [Смешанные реальности Windows](https://developer.microsoft.com/en-us/windows/mixed-reality) , способные добавлена поддержка WebVR в Microsoft пограничного сервера. После игры мы применить эти изменения, то можно ожидать также для работы в другие сочетания браузера и гарнитуры, которые поддерживают WebVR.



## <a name="prerequisites"></a>Предварительные условия

- Текстовый редактор (например, [Кода Visual Studio](https://code.visualstudio.com/download))
- Контроллером Xbox, подключенный к компьютеру
- Windows 10 Creators Update
- На компьютере с [минимальные требуемые характеристики для запуска смешанные реальности Windows](https://developer.microsoft.com/en-us/windows/mixed-reality/immersive_headset_setup)
- Смешанные реальности Windows устройства (необязательно) 



## <a name="getting-started"></a>Начало работы

Чтобы приступить к работе проще всего посетите [веб Windows учебники репозиториев repo](https://github.com/Microsoft/Windows-tutorials-web), нажмите клавишу зеленой **клонированной или загрузить** и выберите пункт **Открыть в Visual Studio**.

![Кнопка "Клонировать или скачать"](images/3dclone.png)

Если вы не хотите клонировать проект, его можно скачать в виде ZIP-файла.
Затем необходимо две папки, [до](https://github.com/Microsoft/Windows-tutorials-web/tree/master/BabylonJS-game-with-WebVR/before) и [после](https://github.com/Microsoft/Windows-tutorials-web/tree/master/BabylonJS-game-with-WebVR/after). Папка «до» — это наш игру перед добавляются все функции VR и папку «после» — по завершении работы игра с поддержкой VR.

До и после папках содержатся следующие файлы:
-   **текстур /** — папка, содержащая любые изображения, используемые в нужную игру.
-   **css /** — папка, содержащая CSS для игры.
-   **js /** — папка, содержащая файлы JavaScript. Файл main.js является нашей игру, и другие файлы, библиотеки, используемые.
-   **моделей /** — папка, содержащая 3D-моделей. Для этой игры у нас есть только одна модель для динозавр.
-   **index.html** - веб-страницы, на котором размещается обработчик нужную игру. Открытие на этой странице в пограничного сервера Microsoft запускает нужную игру.

Вы можете проверить обеих версий игры, открыв их файлы соответствующих index.html в Microsoft пограничного сервера.



## <a name="the-mixed-reality-portal"></a>Смешанные реальности портала

Если вы знакомы с Windows смешанные реальности и обновление создателей 10 Windows, установленные на компьютере с совместимой графической карты, попробуйте открыть приложение **Смешанные реальности портала** в меню Пуск Windows 10.

![Смешанные реальности портала поиска](images/mixed-reality-portal.png)

Если выполнены все требования, можно включить компоненты для разработчиков и моделирования смешанные реальности Windows гарнитура, подключен к компьютеру. Если вы повезло достаточно фактический гарнитура рядом, подключения к сети и запустите программу установки.

> [!IMPORTANT]
> Смешанные реальности портала необходимо открыть в любое время во время в этом руководстве.

Теперь вы готовы к взаимодействия WebVR с пограничным сервером Microsoft.

## <a name="2d-ui-in-a-virtual-world"></a>2D пользовательского интерфейса в виртуальном мире

>[!NOTE]
> Скопировать папку [**перед**](https://github.com/Microsoft/Windows-tutorials-web/tree/master/BabylonJS-game-with-WebVR/before) для получения примера starter.

[Babylon.GUI](https://doc.babylonjs.com/how_to/gui) — это библиотека, VR понятное, позволяя создавать простые, интерактивные пользовательские интерфейсы, которые работают подходят для VR и отображает не VR.
Когда расширение Babylon.js `GUI` библиотеки — это используемые throuhout примера для создания 2D элементов.


2D текст `GUI` элемент может создаваться с использованием нескольких строк в зависимости от количества атрибутов, которые вы хотите изменить.
В следующем фрагменте кода уже в нашем примере [**перед**](https://github.com/Microsoft/Windows-tutorials-web/tree/master/BabylonJS-game-with-WebVR/before) , но давайте Пошаговое руководство, что происходит.
Сначала выделить [`AdvancedDynamicTexture`](https://doc.babylonjs.com/how_to/gui#advanceddynamictexture) объект, который требуется установить, что охватывает графический интерфейс пользователя. Пример устанавливает это `CreateFullScreenUI()`, то есть наш пользовательского интерфейса охватывает весь экран. С помощью `AdvancedDynamicTexture` создано, мы внесите 2D текстовое поле, которое отображается при запуске игры с помощью `GUI.Rectanlge()` и `GUI.TextBlock()`.


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


Этот пользовательский Интерфейс отображается после создания, но может быть включаются или выключаются с `isVisible` в зависимости от того, что происходит в нужную игру.
```javascript
startUI.isVisible = false;
```



## <a name="detecting-headsets"></a>Обнаружение мини-гарнитур

Рекомендуется для приложений VR иметь два вида камеры, чтобы несколько сценариев могут быть поддержаны. Для этой игры мы будем поддерживает один камеры, которому требуется гарнитура рабочее подключать, а другое, использующего гарнитура не. Чтобы определить, какой из них игра будет использоваться, сначала необходимо проверить видеть обнаружения гарнитуры. Для этого мы будем использовать [`navigator.getVRDisplays()`](https://developer.mozilla.org/en-US/docs/Web/API/Navigator/getVRDisplays).


Добавьте следующий код выше `window.addEventListener('DOMContentLoaded')` в **main.js**.
```javascript
var headset;
// If a VR headset is connected, get its info
navigator.getVRDisplays().then(function (displays) {
    if (displays[0]) {
        headset = displays[0];
    }
});
```

С помощью сведений, хранящихся в `headset` переменных, мы теперь сможет выбирать камеры, необходимой для пользователя.


## <a name="creating-and-selecting-the-initial-camera"></a>Создание и Выбор начальной камеры

С помощью Babylon.js, WebVR можно добавить быстро с помощью [`WebVRFreeCamera`](http://doc.babylonjs.com/classes/3.1/webvrfreecamera). В этом камеры может занять на клавиатуре и позволяет использовать гарнитуры VR для управления вашей цикл «заголовок».


### <a name="step-1-checking-for-headsets"></a>Шаг 1: Проверка наличия мини-гарнитур

Для наших резервный камеры, мы будем использовать [`UniversalCamera`](https://doc.babylonjs.com/classes/3.1/universalcamera) , используемого в настоящее время в исходной игру.

Мы проверим наших `headset` переменной, чтобы определить, является ли можно использовать `WebVRFreeCamera` камеры.

Замените `camera = new BABYLON.UniversalCamera("Camera", new BABYLON.Vector3(0, 18, -45), scene);` приведенный ниже код.
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
Чтобы активировать камеры в большинстве браузеров, пользователя необходимо выполнить некоторые, для которого запрашивается виртуального качества взаимодействия.
Мы будем подключать эту функцию до щелчка мыши.


Вставьте код в тегах `createScene()` работать после `camera.applyGravity = true;` .
```javascript
        scene.onPointerDown = function () {
            scene.onPointerDown = undefined
            camera.attachControl(canvas, true);
        }
```

Нажмите кнопку игры теперь создает строки следующим образом или отображает игры в мини-гарнитуры немедленно, если пользователь принял запрос перед.

![иммерсивном строки](images/immersiveview.png)

Также можно добавить фрагмент кода, который будет отображаться `UniversalCamera` просмотра перед мы переключитесь в нашем `WebVRFreeCamera`, разрешение пользователю откроете игру вместо синий окна. 

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

### <a name="step-3-adding-gamepad-support"></a>Шаг 3: Добавление игровой поддержки

Поскольку `WebVRFreeCamera` не поддерживает изначально игровые планшеты, мы будем сопоставить наших кнопок игровой клавиши со стрелками. Мы будем делать с углубиться в `inputs` свойство камеры. Добавление соответствующих кодов для левой аналоговых упрощенный вверх, вниз, влево и вправо в соответствии с с помощью клавиш со стрелками, наш игровой — обратно в действии.


Добавьте следующий код ниже `scene.onPointerDown = function() {...}` вызова.
``` javascript
    // Custom input, adding Xbox controller support for left analog stick to map to keyboard arrows
    camera.inputs.attached.keyboard.keysUp.push(211);    // Left analog up
    camera.inputs.attached.keyboard.keysDown.push(212);  // Left analog down
    camera.inputs.attached.keyboard.keysLeft.push(214);  // Left analog left
    camera.inputs.attached.keyboard.keysRight.push(213); // Left analog right
```


### <a name="step-4-give-it-a-try"></a>Шаг 4: Попробуйте!

Если мы откройте **index.html** с нашей гарнитуры и игровой контроллер подключен к сети, левой нажатой клавише окна синий игры будет переключитесь в нашем игру VR режим! Пойти дальше и поместить вашей мини-гарнитуры для извлечения результатов. 


<iframe height='300' scrolling='no' title='Игра Дино Babylon.js с помощью Babylon.GUI - WebVR' src='//codepen.io/MicrosoftEdgeDocumentation/embed/preview/RjgpJd/?height=300&theme-id=23761&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Просмотреть перо <a href='https://codepen.io/MicrosoftEdgeDocumentation/pen/RjgpJd/'>игру Дино Babylon.js с помощью Babylon.GUI - WebVR</a> с пограничного сервера: документы Microsoft (<a href='https://codepen.io/MicrosoftEdgeDocumentation'>@MicrosoftEdgeDocumentation</a>) на <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="conclusion"></a>Заключение

Поздравляем! Теперь у вас есть полный игру Babylon.js с поддержкой WebVR. Здесь вы можете воспользоваться узнали построить еще лучше игру или построения off следующий вид.
