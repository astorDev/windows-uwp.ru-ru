---
title: 'Руководство по началу работы: трехмерная игра UWP на JavaScript'
description: Игра UWP для Microsoft Store, написанная на JavaScript с использованием библиотеки three.js
author: abbycar
ms.author: abigailc
ms.date: 03/06/2017
ms.topic: article
keywords: Windows10, uwp
ms.assetid: fb4249b2-f93c-4993-9e4d-57a62c04be66
ms.localizationpriority: medium
ms.openlocfilehash: 0183e19135758f73dfea9b63535437ff9b66011a
ms.sourcegitcommit: 753e0a7160a88830d9908b446ef0907cc71c64e7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/29/2018
ms.locfileid: "5742653"
---
# <a name="creating-a-3d-javascript-game-using-threejs"></a>Создание трехмерной игры JavaScript с использованием библиотеки three.js

## <a name="introduction"></a>Введение

Для веб-разработчиков и любителей JavaScript разработка приложений UWP на JavaScript— удобный способ представления своих приложений остальным людям. Совсем не обязательно учить такие языки, как C# или C++!

В этом примере мы будем пользоваться библиотекой **three.js**. Эта библиотека основана на API WebGL, который используется для отрисовки двух- и трехмерной графики для веб-браузеров. Библиотека **three.js** упрощает этот сложный API и делает разработку с использованием трехмерной графики гораздо проще. 


Хотите немного узнать о приложении, которое нам предстоит создать, прежде чем читать дальше? Ознакомьтесь с ним на CodePen!

<iframe height='300' scrolling='no' title='Окончательный динозавр игры' src='//codepen.io/MicrosoftEdgeDocumentation/embed/preview/NpKejy/?height=300&theme-id=23761&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>См. запись <a href='https://codepen.io/MicrosoftEdgeDocumentation/pen/NpKejy/'>Dino game final</a> от Microsoft Edge Docs (<a href='https://codepen.io/MicrosoftEdgeDocumentation'>@MicrosoftEdgeDocumentation</a>) на <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!NOTE] 
> Этот пример не полный игрой. она предназначена для демонстрации с помощью JavaScript и сторонней библиотеки создать приложение, готовое к публикации в Microsoft Store.


## <a name="requirements"></a>Требования

Для этого проекта понадобятся:
-   компьютер (или виртуальная машина) под управлением текущей версии Windows10;
-   копия Visual Studio. Бесплатный выпуск Visual Studio Community Edition можно скачать на [домашней странице Visual Studio](http://visualstudio.com/).
В этом проекте используется библиотека JavaScript **three.js**. Библиотека **three.js** выпускается по лицензии MIT. Эта библиотека уже есть в проекте (ищите `js/libs` в представлении "Обозреватель решений"). Подробнее об этой библиотеке можно узнать на домашней странице [**three.js**](https://threejs.org/).

## <a name="getting-started"></a>Начало работы

Полный исходный код для приложения хранится в репозитории [GitHub](https://github.com/Microsoft/Windows-appsample-get-started-js3d).

Самый простой способ начать с ним работу— перейти в этот репозиторий, нажать зеленую кнопку "Клонировать или скачать", а затем "Открыть в Visual Studio". 

![Кнопка "Клонировать или скачать"](images/3dclone.png)

Если вы не хотите клонировать проект, его можно скачать в виде ZIP-файла.
Загрузив проект в Visual Studio, вы увидите несколько файлов, в том числе:
-   Images/— папка с различными значками для приложений UWP.
- css/— папка с используемыми таблицами стилей CSS.
-   js/— папку, содержащую файлы JavaScript, где файл main.js— это сама игра, а другие файлы— сторонние библиотеки.
-   models/— папка с трехмерными моделями. Для этой игры используется лишь одна модель—модель динозавра.
-   index.html— веб-страница, на которой размещен обработчик игры.

Теперь можно запустить игру!

Чтобы запустить приложение, нажмите клавишу F5. Должно открыться окно с предложением щелкнуть на экране. На фоне вы также увидите двигающегося динозавра. Закройте игру, и мы начнем изучать приложение и его основные компоненты.

> [!NOTE] 
> Что-то пошло не так? Убедитесь, что вы установили Visual Studio с поддержкой веб-страниц. Это можно проверить, создав новый проект. Если JavaScript не поддерживается, нужно будет переустановить Visual Studio, установив флажок "Инструменты разработчика Microsoft Web".

## <a name="walkthrough"></a>Пошаговое руководство

При запуске этой игры отобразится окно с предложением щелкнуть на экране. [API блокировки указателя](https://developer.mozilla.org/docs/Web/API/Pointer_Lock_API) используется, чтобы вы могли осматриваться с помощью мыши. Для перемещения используются клавиши W, A, S и D или клавиши со стрелками.
Цель этой игры— убегать от динозавра. Как только динозавр приблизится к вам на достаточное расстояние, он начнет вас преследовать, пока вы не убежите достаточно далеко или не подойдете слишком близко и проиграете.

### <a name="1-setting-up-your-initial-html-file"></a>1. Настройка исходного файла HTML

Для начала работы необходимо добавить небольшой код HTML в файл **index.html**. Этот файл представляет собой веб-страницу по умолчанию, содержащую приложение.

Сейчас мы настроим его с помощью библиотек, которые будем использовать, и элемента `div` (с именем `container`), который мы будем использовать для отрисовки графики. Мы также настроим его так, чтобы он указывал на файл **main.js** (код нашей игры).


```html
<!DOCTYPE html>
<html lang='en'>

<head>
    <link rel="stylesheet" type="text/css" href="css/stylesheet.css" />
</head>

    <body>
        <div id='container'></div>
        <script src='js/libs/three.js'></script>
        <script src="js/controls/PointerLockControls.js"></script>
        <script src="js/main.js"></script>
    </body>

</html>
```


Исходный код HTML готов. Теперь можно перейти к файлу **main.js** и создать графическое наполнение!

### <a name="2-creating-your-scene"></a>2. Создание сцены

В этом разделе руководства мы добавим основание игры.

Для начала сформируем элемент `scene`. Элемент `scene` в библиотеке **three.js** служит для добавления камеры, объектов и источников света. Вам также потребуется обработчик, который отобразит изображение с камеры.

В файле **main.js** мы создадим функцию для всех этих действий с именем `init()`, которая вызывает некоторые дополнительные функции.

```javascript
var UNITWIDTH = 90; // Width of a cubes in the maze
var UNITHEIGHT = 45; // Height of the cubes in the maze

var camera, scene, renderer;

init();
animate();

function init() {
    // Create the scene where everything will go
    scene = new THREE.Scene();

    // Add some fog for effects
    scene.fog = new THREE.FogExp2(0xcccccc, 0.0015);

    // Set render settings
    renderer = new THREE.WebGLRenderer();
    renderer.setClearColor(scene.fog.color);
    renderer.setPixelRatio(window.devicePixelRatio);
    renderer.setSize(window.innerWidth, window.innerHeight);

    // Get the HTML container and connect renderer to it
    var container = document.getElementById('container');
    container.appendChild(renderer.domElement);

    // Set camera position and view details
    camera = new THREE.PerspectiveCamera(60, window.innerWidth / window.innerHeight, 1, 2000);
    camera.position.y = 20; // Height the camera will be looking from
    camera.position.x = 0;
    camera.position.z = 0;

    // Add the camera
    scene.add(camera);

    // Add the walls(cubes) of the maze
    createMazeCubes();

    // Add lights to the scene
    addLights();

    // Listen for if the window changes sizes and adjust
    window.addEventListener('resize', onWindowResize, false);
}

```

Нам также потребуется создать следующие функции:
- `createMazeCubes()`
- `addLights()`
- `onWindowResize()`
- `animate()` / `render()`
- Функции преобразования единиц

#### <a name="createmazecubes"></a>createMazeCubes()

Функция `createMazeCubes()` добавит в сцену простой куб. В дальнейшем мы используем эту функцию для добавления множества кубов в наш лабиринт.

```javascript
function createMazeCubes() {

  // Make the shape of the cube that is UNITWIDTH wide/deep, and UNITHEIGHT tall
  var cubeGeo = new THREE.BoxGeometry(UNITWIDTH, UNITHEIGHT, UNITWIDTH);
  // Make the material of the cube and set it to blue
  var cubeMat = new THREE.MeshPhongMaterial({
    color: 0x81cfe0,
  });
  
  // Combine the geometry and material to make the cube
  var cube = new THREE.Mesh(cubeGeo, cubeMat);

  // Add the cube to the scene
  scene.add(cube);

  // Update the cube's position
  cube.position.y = UNITHEIGHT / 2;
  cube.position.x = 0;
  cube.position.z = -100;
  // rotate the cube by 30 degrees
  cube.rotation.y = degreesToRadians(30);
}

```

#### <a name="addlights"></a>addLights()

`addLights()`— это простая функция, которая группирует создаваемые источники света и добавляет их в сцену.

```javascript
function addLights() {
  var lightOne = new THREE.DirectionalLight(0xffffff);
  lightOne.position.set(1, 1, 1);
  scene.add(lightOne);

  // Add a second light with half the intensity
  var lightTwo = new THREE.DirectionalLight(0xffffff, .5);
  lightTwo.position.set(1, -1, -1);
  scene.add(lightTwo);
}
```

#### <a name="onwindowresize"></a>onWindowResize()

Функция `onWindowResize` вызывается каждый раз, когда прослушиватель событий узнает, что произошло событие `resize`. Оно происходит, когда пользователь изменяет размер окна. В этом случае необходимо убедиться, что изображение сохраняет пропорции и выводится на все окно.

```javascript
function onWindowResize() {

  camera.aspect = window.innerWidth / window.innerHeight;
  camera.updateProjectionMatrix();

  renderer.setSize(window.innerWidth, window.innerHeight);
}
```

#### <a name="animate"></a>animate()

Наконец, нам потребуется функция `animate()`, которая также будет вызывать функцию `render()`. Функция [`requestAnimationFrame()`](https://developer.mozilla.org/docs/Web/API/window/requestAnimationFrame) используется для постоянного обновления обработчика. В дальнейшем мы будем использовать эти функции для добавления эффектных анимаций, таких как передвижение по лабиринту, в обработчик.

```javascript
function animate() {
    render();
    // Keep updating the renderer
    requestAnimationFrame(animate);
}

function render() {
    renderer.render(scene, camera);
}
```

#### <a name="unit-conversion-functions"></a>Функции преобразования единиц

В **three.js** повороты измеряются в радианах. Чтобы упростить работу, мы добавим некоторые функции для удобного преобразования градусов в радианы. 


```javascript
function degreesToRadians(degrees) {
  return degrees * Math.PI / 180;
}

function radiansToDegrees(radians) {
  return radians * 180 / Math.PI;
}
```

Нелегко запомнить, что 30 градусов— это 0,523радиана. Гораздо проще выполнить операцию `degreesToRadians(30)`, чтобы получить значение поворота, которое используется в функции `createMazeCubes()`.

___

Объем кода немалый, но у нас уже есть прекрасный куб, отрисованный в `container`! Посмотрите на результат в CodePen.

Вы можете скопировать и вставить весь код JavaScript в эту запись CodePen, чтобы не отстать, если вы столкнулись с проблемами, или отредактировать ее, чтобы настроить источники света и изменить цвета. 

<iframe height='300' scrolling='no' title='Источники света, камера, кубов!' src='//codepen.io/MicrosoftEdgeDocumentation/embed/YZWygZ/?height=300&theme-id=23761&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>См. запись <a href='https://codepen.io/MicrosoftEdgeDocumentation/pen/YZWygZ/'>источников света, камеру, кубов!</a> от Microsoft Edge Docs (<a href='https://codepen.io/MicrosoftEdgeDocumentation'>@MicrosoftEdgeDocumentation</a>) на <a href='https://codepen.io'>CodePen</a>.
</iframe>


### <a name="3-making-the-maze"></a>3. Создание лабиринта

Куб, безусловно, завораживает, но еще лучше целый лабиринт из кубов! В игровом сообществе хорошо известно, что один из самых быстрых способов создать уровеньзаключается в размещении кубов в двухмерном массиве.
 
![лабиринт на основе двухмерного массива](images/dinomap.png)

Простой ручной способ создания и изменения лабиринта— размещение единиц там, где должны быть кубы, и нолей, где их быть не должно.

Для этого заменим прежнюю функцию `createMazeCubes()` на ту, в которой используется вложенный цикл для создания и размещения нескольких кубов. Мы также создадим имя массива `collidableObjects` и добавим в него кубы для обнаружения столкновений далее в этом учебнике.

```javascript
var totalCubesWide; // How many cubes wide the maze will be
var collidableObjects = []; // An array of collidable objects used later

function createMazeCubes() {
  // Maze wall mapping, assuming even square
  // 1's are cubes, 0's are empty space
  var map = [
    [0, 0, 1, 0, 1, 0, 1, 0, 0, 0, 1, 0, 0, 0, 0, 0, 0, 1, 0, 0, ],
    [0, 1, 0, 0, 0, 0, 1, 0, 0, 0, 1, 0, 0, 0, 0, 0, 0, 1, 0, 0, ],
    [0, 1, 0, 0, 0, 0, 1, 0, 0, 0, 1, 1, 1, 1, 1, 0, 1, 1, 0, 0, ],
    [0, 1, 0, 0, 0, 0, 1, 0, 0, 0, 1, 0, 1, 0, 0, 0, 0, 0, 0, 0, ],
    [0, 1, 1, 0, 0, 0, 0, 0, 0, 0, 0, 0, 1, 0, 0, 0, 0, 0, 0, 0, ],
    [0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 1, 0, 0, 0, 0, 0, 0, 0, ],
    [1, 1, 1, 0, 1, 0, 0, 0, 0, 0, 0, 0, 1, 0, 0, 0, 0, 0, 0, 0, ],
    [0, 0, 0, 0, 1, 0, 0, 0, 0, 0, 0, 0, 1, 0, 0, 0, 0, 0, 0, 0, ],
    [0, 0, 0, 0, 1, 1, 1, 1, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, ],
    [0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, ],
    [0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 1, 0, 1, 0, 0, 1, 0, 1, ],
    [0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 1, 0, 1, 0, 0, 1, 0, 0, ],
    [0, 0, 0, 0, 0, 0, 0, 0, 0, 1, 1, 0, 1, 0, 1, 0, 0, 1, 1, 1, ],
    [0, 0, 0, 0, 0, 0, 1, 0, 0, 0, 1, 0, 0, 0, 1, 0, 0, 0, 0, 0, ],
    [0, 0, 0, 0, 0, 0, 1, 0, 0, 1, 1, 1, 1, 0, 1, 0, 0, 0, 0, 0, ],
    [1, 1, 1, 0, 1, 1, 1, 1, 0, 1, 0, 1, 1, 0, 1, 0, 0, 0, 0, 0, ],
    [0, 0, 0, 0, 0, 0, 0, 0, 0, 1, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, ],
    [1, 1, 1, 0, 0, 0, 0, 0, 0, 1, 0, 1, 0, 1, 0, 0, 0, 0, 0, 0, ],
    [0, 0, 1, 0, 1, 1, 1, 1, 0, 0, 0, 1, 0, 1, 0, 0, 0, 0, 0, 0, ],
    [0, 0, 1, 1, 1, 0, 0, 0, 0, 1, 0, 1, 0, 1, 0, 0, 0, 0, 0, 0, ]
  ];

  // wall details
  var cubeGeo = new THREE.BoxGeometry(UNITWIDTH, UNITHEIGHT, UNITWIDTH);
  var cubeMat = new THREE.MeshPhongMaterial({
    color: 0x81cfe0,
  });

  // Keep cubes within boundry walls
  var widthOffset = UNITWIDTH / 2;
  // Put the bottom of the cube at y = 0
  var heightOffset = UNITHEIGHT / 2;
  
  // See how wide the map is by seeing how long the first array is
  totalCubesWide = map[0].length;

  // Place walls where 1`s are
  for (var i = 0; i < totalCubesWide; i++) {
    for (var j = 0; j < map[i].length; j++) {
      // If a 1 is found, add a cube at the corresponding position
      if (map[i][j]) {
        // Make the cube
        var cube = new THREE.Mesh(cubeGeo, cubeMat);
        // Set the cube position
        cube.position.z = (i - totalCubesWide / 2) * UNITWIDTH + widthOffset;
        cube.position.y = heightOffset;
        cube.position.x = (j - totalCubesWide / 2) * UNITWIDTH + widthOffset;
        // Add the cube
        scene.add(cube);
        // Used later for collision detection
        collidableObjects.push(cube);
      }
    }
  }
    // The size of the maze will be how many cubes wide the array is * the width of a cube
    mapSize = totalCubesWide * UNITWIDTH;
}

```

Теперь, зная количество используемых кубов (и их размер), мы можем использовать расчетную переменную `mapSize` для указания размеров плоскости основания.

```javascript
var mapSize;    // The width/depth of the maze

function createGround() {
    // Create ground geometry and material
    var groundGeo = new THREE.PlaneGeometry(mapSize, mapSize);
    var groundMat = new THREE.MeshPhongMaterial({ color: 0xA0522D, side: THREE.DoubleSide});

    var ground = new THREE.Mesh(groundGeo, groundMat);
    ground.position.set(0, 1, 0);
    // Rotate the place to ground level
    ground.rotation.x = degreesToRadians(90);
    scene.add(ground);
}
```

В качестве последнего элемента добавим стены по периметру, чтобы оградить лабиринт. Мы используем цикл для одновременного создания двух плоскостей (стен) при помощи переменной `mapSize`, рассчитанной в `createGround()`, чтобы определить их ширину. Новые стены также будут добавлены в массив `collidableObjects` для обнаружения столкновений в дальнейшем.

```javascript
function createPerimWalls() {
    var halfMap = mapSize / 2;  // Half the size of the map
    var sign = 1;               // Used to make an amount positive or negative

    // Loop through twice, making two perimeter walls at a time
    for (var i = 0; i < 2; i++) {
        var perimGeo = new THREE.PlaneGeometry(mapSize, UNITHEIGHT);
        // Make the material double sided
        var perimMat = new THREE.MeshPhongMaterial({ color: 0x464646, side: THREE.DoubleSide });
        // Make two walls
        var perimWallLR = new THREE.Mesh(perimGeo, perimMat);
        var perimWallFB = new THREE.Mesh(perimGeo, perimMat);

        // Create left/right wall
        perimWallLR.position.set(halfMap * sign, UNITHEIGHT / 2, 0);
        perimWallLR.rotation.y = degreesToRadians(90);
        scene.add(perimWallLR);
        // Used later for collision detection
        collidableObjects.push(perimWallLR);
        // Create front/back wall
        perimWallFB.position.set(0, UNITHEIGHT / 2, halfMap * sign);
        scene.add(perimWallFB);

        // Used later for collision detection
        collidableObjects.push(perimWallFB);

        sign = -1; // Swap to negative value
    }
}
```


Не забудьте добавить вызов `createGround()` и `createPerimWalls` после `createMazeCubes()` в функции `init()`, чтобы они были скомпилированы.
___

Мы создали превосходный лабиринт, но не можем вполне оценить его, так как наша камера стоит на месте. Пора сделать игру интереснее и добавить элементы управления камерой.

Поэкспериментируйте в CodePen, например измените цвет кубов или удалите основание, снабдив комментарием `createGround()` в функции `init()`.


<iframe height='300' scrolling='no' title='Создание лабиринта' src='//codepen.io/MicrosoftEdgeDocumentation/embed/JWKYzG/?height=300&theme-id=23761&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>См. запись <a href='https://codepen.io/MicrosoftEdgeDocumentation/pen/JWKYzG/'>Maze building</a> от Microsoft Edge Docs (<a href='https://codepen.io/MicrosoftEdgeDocumentation'>@MicrosoftEdgeDocumentation</a>) на <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="4-allowing-the-player-to-look-around"></a>4. Предоставление игроку возможности осматриваться

Самое время отправиться в лабиринт и осмотреться. Для этого мы воспользуемся библиотекой **PointerLockControls.js** и камерой.

Библиотека **PoinerLockControls.js** использует мышь для поворота камеры в направлении движения мыши, позволяя игроку осматриваться. 

Для начала добавим несколько новых элементов в файл **index.html**.

```html
<div id="blocker">
    <div id="instructions">
    <strong>Click to look!</strong>
    </div>
</div>

<script src="main.js"></script>
```

Вам также потребуется весь код CSS на CodePen внизу этого раздела. Его необходимо вставить в файл **stylesheet.css**.

Вернемся к файлу **main.js** и добавим несколько глобальных переменных: `controls` для хранения контроллера, `controlsEnabled` для отслеживания состояния контроллера и `blocker` для захвата элемента `blocker` в **index.html**:

```javascript
var controls;
var controlsEnabled = false;

// HTML elements to be changed
var blocker = document.getElementById('blocker');
```


Теперь мы можем создать новый объект `PoinerLockControls` в функции `init()`, передать ему элемент `camera` и добавить `camera` (доступ к объекту осуществляется с помощью функции `controls.getObject()`).

```javascript
controls = new THREE.PointerLockControls(camera);
scene.add(controls.getObject());
```

Камера подключена, но нужно обеспечить взаимодействие между мышью и контроллером, чтобы игрок мог осматриваться. 

В этой ситуации поможет [API блокировки указателя](https://docs.microsoft.com/microsoft-edge/dev-guide/dom/pointer-lock), с помощью которого можно привязать движения мыши к камере. API блокировки указателя также скрывает указатель мыши для большей реалистичности. Нажатием клавиши ESC мы прерываем связь мыши и камеры, а указатель вновь появляется. Чтобы это осуществить, добавим функции `getPointerLock()` и `lockChange()`.

Функция `getPointerLock()` ожидает щелчка мышью. После щелчка обработанная игра (в элементе `container`) пытается получить контроль над мышью. Мы также добавим прослушиватель событий, чтобы определить, когда игрок включает или отключает блокировку, после чего вызывается функция `lockChange()`. 

```javascript
function getPointerLock() {
  document.onclick = function () {
    container.requestPointerLock();
  }
  document.addEventListener('pointerlockchange', lockChange, false); 
}

```

Функции `lockChange()` необходимо отключить или включить элементы управления и элемент `blocker`. Чтобы определить состояние блокировки указателя, нужно проверить, указан ли элемент `container` в качестве целевого значения свойства [`pointerLockElement`](https://developer.mozilla.org/docs/Web/API/Document/pointerLockElement) для событий мыши.

```javascript
function lockChange() {
    // Turn on controls
    if (document.pointerLockElement === container) {
        // Hide blocker and instructions
        blocker.style.display = "none";
        controls.enabled = true;
    // Turn off the controls
    } else {
      // Display the blocker and instruction
        blocker.style.display = "";
        controls.enabled = false;
    }
}
```

Теперь мы можем добавить вызов функции `getPointerLock()` непосредственно перед функцией `init()`.
```javascript
// Get the pointer lock state
getPointerLock();
init();
animate();
```

---

Теперь у нас есть возможность **осматриваться**, но интереснее всего **передвигаться**. Дальше придется прибегнуть к математическим вычислениям и векторам, но что за трехмерная графика без вычислений?

<iframe height='300' scrolling='no' title='Осматриваться' src='//codepen.io/MicrosoftEdgeDocumentation/embed/gmwbMo/?height=300&theme-id=23761&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>См. запись <a href='https://codepen.io/MicrosoftEdgeDocumentation/pen/gmwbMo/'>Look around</a> от Microsoft Edge Docs (<a href='https://codepen.io/MicrosoftEdgeDocumentation'>@MicrosoftEdgeDocumentation</a>) на <a href='https://codepen.io'>CodePen</a>.
</iframe>


### <a name="5-adding-player-movement"></a>5. Добавление перемещения игрока

Чтобы разобраться в том, как заставить игрока двигаться, придется углубиться в вычисления. Нам нужно придать скорость (движение) элементу `camera` вдоль определенного вектора (направление).

Добавим еще несколько глобальных переменных, чтобы отслеживать направление движения игрока, и зададим начальный вектор скорости.

```javascript
// Flags to determine which direction the player is moving
var moveForward = false;
var moveBackward = false;
var moveLeft = false;
var moveRight = false;

// Velocity vector for the player
var playerVelocity = new THREE.Vector3();

// How fast the player will move
var PLAYERSPEED = 800.0;

var clock;
```

В начале функции `init()` установим в качестве значения `clock` новый объект `Clock`. Мы будем использовать его для отслеживания изменений во времени (дельта) для отрисовки новых кадров. Вам также потребуется добавить вызов функции `listenForPlayerMovement()`, собирающей вводимые пользователем данные. 

```
clock = new THREE.Clock();
listenForPlayerMovement();
```

Функция `listenForPlayerMovement()` будет переключать состояния направления. Внизу функции расположены два прослушивателя событий, ожидающие, когда клавиша будет нажата и отпущена. Как только произойдет одно из этих событий, мы проверим, должна ли эта клавиша начинать или останавливать движение.

Мы настроили эту игру таким образом, что игрок может передвигаться с помощью клавиш W, A, S и D или клавиш со стрелками.

```javascript
function listenForPlayerMovement() {
    
    // A key has been pressed
    var onKeyDown = function(event) {

    switch (event.keyCode) {

      case 38: // up
      case 87: // w
        moveForward = true;
        break;

      case 37: // left
      case 65: // a
        moveLeft = true;
        break;

      case 40: // down
      case 83: // s
        moveBackward = true;
        break;

      case 39: // right
      case 68: // d
        moveRight = true;
        break;
    }
  };

  // A key has been released
    var onKeyUp = function(event) {

    switch (event.keyCode) {

      case 38: // up
      case 87: // w
        moveForward = false;
        break;

      case 37: // left
      case 65: // a
        moveLeft = false;
        break;

      case 40: // down
      case 83: // s
        moveBackward = false;
        break;

      case 39: // right
      case 68: // d
        moveRight = false;
        break;
    }
  };

  // Add event listeners for when movement keys are pressed and released
  document.addEventListener('keydown', onKeyDown, false);
  document.addEventListener('keyup', onKeyUp, false);
}
```

Теперь вы можем определить, куда пользователь хочет направиться (направление хранится в виде значения `true` в одном из глобальных флагов направления), и пора действовать. Действие выражается в виде функции `animatePlayer()`.

Эта функция будет вызываться из `animate()` и передавать `delta`, чтобы получить изменение во времени между кадрами, что при изменениях частоты кадров движение оставалось синхронным.

```javascript
function animate() {
  render();
  requestAnimationFrame(animate);
  // Get the change in time between frames
  var delta = clock.getDelta();
  animatePlayer(delta);
}
```

А теперь самое интересное! Вектор импульса (`playerVeloctiy`) определяется тремя параметрами, `(x, y, z)`, среди которых `y` выражает вертикальный импульс. Так как в этой игре не будет прыжков, мы будем работать только с параметрами `x` и `z`. Изначально для этого вектора установлены значения (0, 0, 0).

Как видно из кода ниже, выполняется серия проверок, чтобы узнать, значение какого флага направления переключается на `true`. Узнав направление, мы увеличиваем или уменьшаем значения `x` и `y`, чтобы задать импульс в этом направлении. Если ни одна из клавиш движения не нажата, параметры вектора вновь возвращаются к значениям `(0, 0, 0)`.


```javascript

function animatePlayer(delta) {
  // Gradual slowdown
  playerVelocity.x -= playerVelocity.x * 10.0 * delta;
  playerVelocity.z -= playerVelocity.z * 10.0 * delta;

  if (moveForward) {
    playerVelocity.z -= PLAYERSPEED * delta;
  } 
  if (moveBackward) {
    playerVelocity.z += PLAYERSPEED * delta;
  } 
  if (moveLeft) {
    playerVelocity.x -= PLAYERSPEED * delta;
  } 
  if (moveRight) {
    playerVelocity.x += PLAYERSPEED * delta;
  }
  if( !( moveForward || moveBackward || moveLeft ||moveRight)) {
    // No movement key being pressed. Stop movememnt
    playerVelocity.x = 0;
    playerVelocity.z = 0;
  }
  controls.getObject().translateX(playerVelocity.x * delta);
  controls.getObject().translateZ(playerVelocity.z * delta);
}
```

В конце мы применяем обновленные значения `x` и `y` к камере в виде преобразований, чтобы игрок двигался.

---

Поздравляем! Вы создали управляемую игроком камеру, которая может двигаться и осматриваться. Пока что мы по-прежнему проходим сквозь стены, но об этом позаботимся позже. Далее мы добавим динозавра.

<iframe height='300' scrolling='no' title='Перемещения' src='//codepen.io/MicrosoftEdgeDocumentation/embed/qrbKZg/?height=300&theme-id=23761&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>См. в разделе пера <a href='https://codepen.io/MicrosoftEdgeDocumentation/pen/qrbKZg/'>перемещения</a> от Microsoft Edge Docs (<a href='https://codepen.io/MicrosoftEdgeDocumentation'>@MicrosoftEdgeDocumentation</a>) на <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!NOTE]
> Использование этих элементов управления в приложении UWP может привести к задержкам при движении и незарегистрированным событиям `keyUp`. Мы изучаем эту проблему и надеемся вскоре исправить эту часть примера.

### <a name="6-load-that-dino"></a>6. Загружаем динозавра!

Если вы клонировали или скачали репозиторий этого проекта, внутри вы найдете папку `models` с файлом `dino.json`. Этот файл JSON представляет собой трехмерную модель динозавра сделанная в Blender и экспортированная из него.


Чтобы загрузить этого динозавра, мы добавим еще несколько глобальных переменных.

```javascript
var DINOSCALE = 20;  // How big our dino is scaled to

var clock;
var dino;
var loader = new THREE.JSONLoader();

var instructions = document.getElementById('instructions');
```

Создав `JSONLoader`, передадим путь к файлу **dino.json** и обратный вызов с данными геометрии и материалами из файла.
Загрузка динозавра— асинхронная задача, поэтому ничто не будет отрисовано до полной загрузки динозавра. В файле **index.html** мы изменили строку в элементе `instructions` на `"Loading..."`, чтобы игрок знал о процессе загрузки.

После загрузки динозавра добавим в элемент `instructions` фактические игровые инструкции и переместим функцию `animate()` с конца функции `init()` в конец обратного вызова функции, как показано ниже.

```javascript
   // load the dino JSON model and start animating once complete
    loader.load('./models/dino.json', function (geometry, materials) {


        // Get the geometry and materials from the JSON
        var dinoObject = new THREE.Mesh(geometry, new THREE.MultiMaterial(materials));

        // Scale the size of the dino
        dinoObject.scale.set(DINOSCALE, DINOSCALE, DINOSCALE);
        dinoObject.rotation.y = degreesToRadians(-90);
        dinoObject.position.set(30, 0, -400);
        dinoObject.name = "dino";
        scene.add(dinoObject);
        
        // Store the dino
        dino = scene.getObjectByName("dino"); 

        // Model is loaded, switch from "Loading..." to instruction text
        instructions.innerHTML = "<strong>Click to Play!</strong> </br></br> W,A,S,D or arrow keys = move </br>Mouse = look around";

        // Call the animate function so that animation begins after the model is loaded
        animate();
    });
```

---

Модель динозавра загружена. Посмотрите!

<iframe height='300' scrolling='no' title='Добавление динозавр' src='//codepen.io/MicrosoftEdgeDocumentation/embed/xqOwBw/?height=300&theme-id=23761&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>См. запись <a href='https://codepen.io/MicrosoftEdgeDocumentation/pen/xqOwBw/'>Adding the dino</a> от Microsoft Edge Docs (<a href='https://codepen.io/MicrosoftEdgeDocumentation'>@MicrosoftEdgeDocumentation</a>) на <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="7-move-that-dino"></a>7. Динозавр должен передвигаться!

Создание ИИ для игры может стать крайне сложной задачей, поэтому в данном примере мы сделаем поведение динозавра при перемещении простым. Динозавр будет двигаться прямо, проходить сквозь стены и исчезать в тумане вдалеке.

Для этого сначала добавим глобальную переменную `dinoVelocity`.

```javascript
var DINOSPEED = 400.0;

var dinoVelocity = new THREE.Vector3();
```
 Затем вызовем функцию `animateDino()` из функции `animation()` и добавим следующий код.

```javascript
function animateDino(delta) {
    // Gradual slowdown
    dinoVelocity.x -= dinoVelocity.x * 10.0 * delta;
    dinoVelocity.z -= dinoVelocity.z * 10.0 * delta;

    dinoVelocity.z += DINOSPEED * delta;
    // Move the dino
    dino.translateZ(dinoVelocity.z * delta);
}
```
---

Смотреть, как динозавр "уплывает", не очень интересно, но как только мы реализуем обнаружение столкновений, игра оживет.

<iframe height='300' scrolling='no' title='Перемещение dino - столкновения нет' src='//codepen.io/MicrosoftEdgeDocumentation/embed/preview/jBMbbL/?height=300&theme-id=23761&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>См. запись <a href='https://codepen.io/MicrosoftEdgeDocumentation/pen/jBMbbL/'>Moving the dino - no collision</a> от Microsoft Edge Docs (<a href='https://codepen.io/MicrosoftEdgeDocumentation'>@MicrosoftEdgeDocumentation</a>) на <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="8-collision-detection-for-the-player"></a>8. Обнаружение столкновений для игрока

Игрок и динозавр передвигаются, но они по-прежнему могут проходить сквозь стены. Когда в самом начале учебника мы начали добавлять кубы и стены, мы поместили их в массив `collidableObjects`. Именно этот массив мы и будем использовать, чтобы определять, что игрок слишком близко к какому-либо объекту и не может пройти через него.

Мы будем использовать трассировщики лучей, чтобы определять предстоящее пересечение. Можно представить трассировщик лучей как лазерный луч, исходящий из камеры в определенном направлении и сообщающий о столкновении с объектом и расстояние до этого объекта.

```javascript
var PLAYERCOLLISIONDISTANCE = 20;
```

Мы создадим новую функцию `detectPlayerCollision()`, которая будет возвращать значение `true`, если игрок находится слишком близко к объекту, с которым возможно столкновение.
Мы применим к игроку один трассировщик лучей, изменяющий свое направление в зависимости от направления движения игрока.

Для этого создадим неопределенную матрицу `rotationMatrix`. При проверке направления движения мы придем к определенной матрице `rotationMatrix` или неопределенной при движении вперед.
Если матрица `rotationMatrix` определена, она будет применяться к направлению элементов управления. 

Затем от камеры будет пущен трассировщик лучей в направлении `cameraDirection`.


```javascript
function detectPlayerCollision() {
    // The rotation matrix to apply to our direction vector
    // Undefined by default to indicate ray should coming from front
    var rotationMatrix;
    // Get direction of camera
    var cameraDirection = controls.getDirection(new THREE.Vector3(0, 0, 0)).clone();

    // Check which direction we're moving (not looking)
    // Flip matrix to that direction so that we can reposition the ray
    if (moveBackward) {
        rotationMatrix = new THREE.Matrix4();
        rotationMatrix.makeRotationY(degreesToRadians(180));
    }
    else if (moveLeft) {
        rotationMatrix = new THREE.Matrix4();
        rotationMatrix.makeRotationY(degreesToRadians(90));
    }
    else if (moveRight) {
        rotationMatrix = new THREE.Matrix4();
        rotationMatrix.makeRotationY(degreesToRadians(270));
    }

    // Player is not moving forward, apply rotation matrix needed
    if (rotationMatrix !== undefined) {
        cameraDirection.applyMatrix4(rotationMatrix);
    }

    // Apply ray to player camera
    var rayCaster = new THREE.Raycaster(controls.getObject().position, cameraDirection);

    // If our ray hit a collidable object, return true
    if (rayIntersect(rayCaster, PLAYERCOLLISIONDISTANCE)) {
        return true;
    } else {
        return false;
    }
}
```

Функция `detectPlayerCollision()` зависит от вспомогательной функции `rayIntersect()`.
Она использует трассировщик лучей и значение, представляющее, насколько близко мы можем подойти к объекту в массиве `collidableObjects`, прежде чем определить, что произошло столкновение.

```javascript
function rayIntersect(ray, distance) {
    var intersects = ray.intersectObjects(collidableObjects);
    for (var i = 0; i < intersects.length; i++) {
        // Check if there's a collision
        if (intersects[i].distance < distance) {
            return true;
        }
    }
    return false;
}
```

Получив возможность определять предстоящее столкновение, мы можем привести в порядок функцию `animatePlayer()`.

```javascript
function animatePlayer(delta) {
    // Gradual slowdown
    playerVelocity.x -= playerVelocity.x * 10.0 * delta;
    playerVelocity.z -= playerVelocity.z * 10.0 * delta;

    // If no collision and a movement key is being pressed, apply movement velocity
    if (detectPlayerCollision() == false) {
        if (moveForward) {
            playerVelocity.z -= PLAYERSPEED * delta;
        }
        if (moveBackward) {
            playerVelocity.z += PLAYERSPEED * delta;
        } 
        if (moveLeft) {
            playerVelocity.x -= PLAYERSPEED * delta;
        }
        if (moveRight) {
            playerVelocity.x += PLAYERSPEED * delta;
        }

        controls.getObject().translateX(playerVelocity.x * delta);
        controls.getObject().translateZ(playerVelocity.z * delta);
    } else {
        // Collision or no movement key being pressed. Stop movememnt
        playerVelocity.x = 0;
        playerVelocity.z = 0;
    }
}
```

---

Мы реализовали обнаружение столкновений игрока. Попробуйте столкнуться со стеной!

<iframe height='300' scrolling='no' title='Переместить игрока - столкновения' src='//codepen.io/MicrosoftEdgeDocumentation/embed/preview/qraOeO/?height=300&theme-id=23761&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>См. запись <a href='https://codepen.io/MicrosoftEdgeDocumentation/pen/qraOeO/'>Moving the player - collision</a> от Microsoft Edge Docs (<a href='https://codepen.io/MicrosoftEdgeDocumentation'>@MicrosoftEdgeDocumentation</a>) на <a href='https://codepen.io'>CodePen</a>.
</iframe>


### <a name="9-collision-detection-and-animation-for-dino"></a>9. Обнаружение столкновений и анимация для динозавра

Пора лишить динозавра возможности проходить сквозь стены и заставить его двигаться в случайном направлении, если он окажется слишком близко к объекту, с которым возможно столкновение.

Для начала определим, когда динозавр может столкнуться с объектом. 

Нам потребуется назначить еще одну переменную для расстояния столкновения.

```javascript
var DINOCOLLISIONDISTANCE = 55;     
```

Мы указали расстояние столкновения для динозавра, а теперь добавим функцию, аналогичную функции `detectPlayerCollision()`, но немного проще.
Функция `detectDinoCollision` проста тем, что от передней части динозавра по прямой всегда исходит один трассировщик лучей. Поворачивать его, как в случае со столкновениями игрока, не нужно.

```javascript
function detectDinoCollision() {
    // Get the rotation matrix from dino
    var matrix = new THREE.Matrix4();
    matrix.extractRotation(dino.matrix);
    // Create direction vector
    var directionFront = new THREE.Vector3(0, 0, 1);

    // Get the vectors coming from the front of the dino
    directionFront.applyMatrix4(matrix);

    // Create raycaster
    var rayCasterF = new THREE.Raycaster(dino.position, directionFront);
    // If we have a front collision, we have to adjust our direction so return true
    if (rayIntersect(rayCasterF, DINOCOLLISIONDISTANCE))
        return true;
    else
        return false;
}
```

Взглянем на окончательный вид функции `animateDino()` после добавления обнаружения столкновений.


```javascript
function animateDino(delta) {
    // Gradual slowdown
    dinoVelocity.x -= dinoVelocity.x * 10.0 * delta;
    dinoVelocity.z -= dinoVelocity.z * 10.0 * delta;


    // If no collision, apply movement velocity
    if (detectDinoCollision() == false) {
        dinoVelocity.z += DINOSPEED * delta;
        // Move the dino
        dino.translateZ(dinoVelocity.z * delta);

    } else {
        // Collision. Adjust direction
        var directionMultiples = [-1, 1, 2];
        var randomIndex = getRandomInt(0, 2);
        var randomDirection = degreesToRadians(90 * directionMultiples[randomIndex]);

        dinoVelocity.z += DINOSPEED * delta;
        dino.rotation.y += randomDirection;
    }
}
```

Нам нужно, чтобы динозавр всегда поворачивался на -90, 90 или 180 градусов. Чтобы упростить этот процесс, выше мы создали массив `directionMultiples`, который будет выдавать эти числа при умножении на 90.
Чтобы сделать выбор градуса угла поворота случайным, мы добавили вспомогательную функцию `getRandomInt()` для захвата значения 0, 1 или 2, представляющего случайный индекс массива.

```javascript
function getRandomInt(min, max) {
    min = Math.ceil(min);
    max = Math.floor(max);
    return Math.floor(Math.random() * (max - min)) + min;
}
```

После выполнения всех этих действий мы умножим случайный индекс массива на 90, чтобы получить градус (преобразованный в радианы) угла поворота.
После добавления значения параметру поворота `y` динозавра с помощью `dino.rotation.y += randomDirection;` динозавр поворачивается в случайном направлении при столкновении.


---

Получилось! Теперь у нас есть динозавр с ИИ, способным перемещаться по лабиринту.

<iframe height='300' scrolling='no' title='Перемещение dino - collision' src='//codepen.io/MicrosoftEdgeDocumentation/embed/preview/bqwMXZ/?height=300&theme-id=23761&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>См. в разделе пера <a href='https://codepen.io/MicrosoftEdgeDocumentation/pen/bqwMXZ/'>перемещения dino - collision</a> от Microsoft Edge Docs (<a href='https://codepen.io/MicrosoftEdgeDocumentation'>@MicrosoftEdgeDocumentation</a>) на <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="10-starting-the-chase"></a>10. Начало погони

Оказавшись на определенном расстоянии от игрока, динозавр должен начать преследовать его. Так как это только пример, у динозавра нет расширенных алгоритмов отслеживания игрока. Динозавр будет просто смотреть на игрока и двигаться к нему. Это работает на открытых участках лабиринта, но столкнувшись со стеной, динозавр путается.

В функции `animate()` мы добавим логическую переменную, определяемую значением, возвращаемым функцией `triggerChase()`.

```javascript
function animate() {
    render();
    requestAnimationFrame(animate);

    // Get the change in time between frames
    var delta = clock.getDelta();

    // If the player is in dino's range, trigger the chase
    var isBeingChased = triggerChase();

    animateDino(delta);
    animatePlayer(delta);
}
```

Функция `triggerChase` проверяет наличие игрока в зоне преследования динозавра, после чего будет постоянно направлять динозавра к игроку, что он мог двигаться в направлении игрока. 

```javascript
function triggerChase() {
    // Check if in dino detection range of the player
    if (dino.position.distanceTo(controls.getObject().position) < 300) {
        // Set the dino target's y value to the current y value. We only care about x and z for movement.
        var lookTarget = new THREE.Vector3();
        lookTarget.copy(controls.getObject().position);
        lookTarget.y = dino.position.y;

        // Make dino face camera
        dino.lookAt(lookTarget);

        // Get distance between dino and camera with a unit offset
        // Game over when dino is the value of CATCHOFFSET units away from camera
        var distanceFrom = Math.round(dino.position.distanceTo(controls.getObject().position)) - CATCHOFFSET;
        // Alert and display distance between camera and dino
        dinoAlert.innerHTML = "Dino has spotted you! Distance from you: " + distanceFrom;
        dinoAlert.style.display = '';
        return true;
        // Not in agro range, don't start distance countdown
    } else {
        dinoAlert.style.display = 'none';
        return false;
    }
}
```

Вторая половина функции `triggerChase` отвечает за отображение текста, из которого игрок будет знать расстояние до динозавра. Мы также добавим `CATCHOFFSET`, чтобы указать необходимо расстояние до `0`. Без смещения `0` был бы прямо над игроком, и конец игры выглядел бы не очень красиво.



```javascript
var dinoAlert = document.getElementById('dino-alert');
dinoAlert.style.display = 'none';
```

---

У нас уже есть разъяренный динозавр, начинающий преследовать игрока, если тот подойдет слишком близко. Динозавр не остановится, пока не окажется над игроком.
В конце нам нужно добавить условия окончания игры, как только динозавр окажется рядом с игроком со смещением `CATCHOFFSET`.

<iframe height='300' scrolling='no' title='Chase' src='//codepen.io/MicrosoftEdgeDocumentation/embed/preview/NpRBqR/?height=300&theme-id=23761&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>См. запись <a href='https://codepen.io/MicrosoftEdgeDocumentation/pen/NpRBqR/'>The chase</a> от Microsoft Edge Docs (<a href='https://codepen.io/MicrosoftEdgeDocumentation'>@MicrosoftEdgeDocumentation</a>) на <a href='https://codepen.io'>CodePen</a>.
</iframe>


### <a name="11-ending-the-game"></a>11. Окончание игры


Мы прошли долгий путь от простого куба, и теперь пора завершать работу.

Сначала установим переменную, чтобы контролировать, завершена игра или нет.

```javascript
var gameOver = false;
```

Теперь в последний раз обновим функцию `animate()`, добавив в нее возможность проверять, достаточно ли близко динозавр к игроку.
Если динозавр находится слишком близко к игроку, запускается новая функция `caught()`, останавливающая движение игрока и динозавра, а если нет, игра продолжится и игрок с динозавром будут перемещаться дальше.

```javascript
function animate() {
    render();
    requestAnimationFrame(animate);

    // Get the change in time between frames
    var delta = clock.getDelta();
    // Update our frames per second monitor

    // If the player is in dino's range, trigger the chase
    var isBeingChased = triggerChase();
    // If the player is too close, trigger the end of the game
    if (dino.position.distanceTo(controls.getObject().position) < CATCHOFFSET) {
        caught();
    // Player is at an undetected distance
    // Keep the dino moving and let the player keep moving too
    } else {
        animateDino(delta);
        animatePlayer(delta);
    }
}
```

Если динозавра поймает игрока, функция `caught()` отобразит элемент `blocker` и обновит текст, чтобы тот указывал, что игрок проиграл.
Переменной `gameOver` также задается значение `true`, и теперь она сообщает нам об окончании игры.  


```javascript
function caught() {
    blocker.style.display = '';
    instructions.innerHTML = "GAME OVER </br></br></br> Press ESC to restart";
    gameOver = true;
    instructions.style.display = '';
    dinoAlert.style.display = 'none';
}
```


Зная, окончена игра или нет, мы можем добавить в функцию `lockChange()` возможность проверки окончания игры.
Теперь мы можем добавить `location.reload` для перезапуска игры, когда пользователь нажимает клавишу ESC после окончания игры.

```javascript
function lockChange() {
    if (document.pointerLockElement === container) {
        blocker.style.display = "none";
        controls.enabled = true;
    } else {
        if (gameOver) {
            location.reload();
        }
        blocker.style.display = "";
        controls.enabled = false;
    }
}
```

---

Вот и все! Путь был непростым, но нам удалось создать игру с помощью библиотеки **three.js**.

Вернитесь в начало страницы, чтобы просмотреть запись [final CodePen](#introduction)!


## <a name="publishing-to-the-microsoft-store"></a>Публикации в Microsoft Store
Теперь у вас есть приложение UWP, его можно опубликовать в Microsoft Store (при условии, что вы сначала Усовершенствуйте его!) Существует несколько шагов в процесс.

1.  [Зарегистрируйтесь](https://developer.microsoft.com/store/register) в качестве разработчика приложений для Windows.
2.  Заполните [контрольный список](https://msdn.microsoft.com/windows/uwp/publish/app-submissions) для отправки приложения.
3.  Отправьте приложение на [сертификацию](https://msdn.microsoft.com/windows/uwp/publish/the-app-certification-process).
Дополнительные сведения см. в разделе [Публикация вашего приложения UWP](https://developer.microsoft.com/store/publish-apps).

