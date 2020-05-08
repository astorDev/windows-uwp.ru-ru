---
description: Какие варианты доступны при разработке кроссплатформенных приложений?
title: Выбор подхода к разработке приложений iOS и UWP
ms.assetid: 5CDAB313-07B7-4A32-A49B-026361DCC853
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 538fbc33d161f5f91033427af76438f49d3d3b68
ms.sourcegitcommit: 28bd367ab8acc64d4b6f3f73adca12100cbd359f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/24/2020
ms.locfileid: "82148585"
---
# <a name="selecting-an-approach-to-ios-and-uwp-app-development"></a>Выбор подхода к разработке приложений iOS и UWP


Какие варианты доступны при разработке кроссплатформенных приложений?

## <a name="whats-the-best-way-to-support-both-ios-and-windows"></a>Какой вариант лучше всего поддерживает и iOS, и Windows?

Может показаться, что Windows и iOS очень сильно отличаются, но множество инструментов и методов, число которых растет, могут существенно упростить создание приложений, поддерживающих обе платформы (а также Android). Лучшее решение зависит от типа создаваемого приложения и от того, начинаете ли вы работу с нуля или портируете существующий проект.

## <a name="writing-a-new-app"></a>Создание нового приложения

Если вы начинаете с чистого листа, в вашем распоряжении множество возможностей, в том числе:

-   [Xamarin](https://xamarin.com/)

    С помощью Xamarin можно написать приложение на C#, работающее в Windows, а также создать собственные приложения для iOS. Поддержка Xamarin встроена в Visual Studio, поэтому просто выберите соответствующий тип проекта.

-   [Apache Cordova](https://www.microsoft.com/?ref=go)

    Если вам больше по душе JavaScript и HTML, Apache Cordova (другое название — PhoneGap) поможет вам создать кроссплатформенные приложения для iOS, Windows и Android. Этот тип проекта также встроен в Visual Studio.

-   Игровые модули

    С помощью таких средств, как [Unity3D](https://www.unrealengine.com/en-US/) и [Unreal Engine](https://www.unity3d.com/) вы можете разрабатывать игры высочайшего качества для Windows и многих других платформ, включая iOS. Unity поддерживает скрипты C#, Unreal использует C++.

-   [MonoGame](http://www.monogame.net/)

    Это духовный правопреемник XNA. Теперь это платформа с открытым кодом для создания кроссплатформенных приложений, т. е. вы можете создавать приложения на C# для многочисленных платформ с поддержкой физического модуля, а также двухмерной и трехмерной графики.

## <a name="adapting-an-existing-app"></a>Адаптация существующего приложения

Если у вас есть существующее приложение для iOS, число вариантов немного меньше. Однако все далеко не потеряно.

-   [Мост Windows для iOS](https://github.com/Microsoft/WinObjC)

    Это средство также известно как Project Islandwood. Оно все еще находится в стадии разработки и может импортировать проекты Xcode непосредственно в Visual Studio. Код Objective-C можно скомпилировать и отладить в Visual Studio. Если ваш проект использует библиотеки, например Cocos для графики, этот способ позволит быстро перенести ваше приложение.

-   Измените назначение своего кода C++.

    Если ваша основная бизнес-логика написана на C++, а не Objective-C или Swift, зачастую можно использовать этот код, внеся в проект незначительные изменения. Затем можно использовать XAML для определения пользовательского интерфейса, как и в других приложениях для Windows, и при необходимости вызывать код C++.

-   [Запуск OpenGL ES в Windows с помощью ANGLE](https://github.com/microsoft/angle/wiki)

    В качестве промежуточного действия при портировании проекта OpenGL ES 2.0 можно использовать ANGLE. ANGLE позволяет запускать содержимое OpenGL ES в Windows путем преобразования вызовов API OpenGL ES в вызовы API DirectX 11.

## <a name="other-cross-platform-authoring-tools"></a>Другие средства разработки кроссплатформенных приложений

-   [GameSalad](https://gamesalad.com/)

    Среда для разработки игр.

-   [Construct 2]( https://www.scirra.com/)

    Среда для разработки игр.

-   [Titanium Studio](https://www.appcelerator.com/platform/titanium-studio/)

    Кроссплатформенная среда для разработки.

-   [Cocos2D-x](https://www.cocos2d-x.org/)

    Кроссплатформенная библиотека кода для обработки спрайтовой графики и физического моделирования.

-   [Impact.js](https://impactjs.com/)

    Библиотека игр на базе HTML.

-   [Marmalade](http://madewithmarmalade.com/)

    Кроссплатформенный пакет SDK.

-   [OpenFL](https://www.openfl.org/)

    Кроссплатформенное средство разработки.

-   [GameMaker](https://www.yoyogames.com/gamemaker/studio)

    Среда разработки, специально созданная для игр.

-   [PlayCanvas](https://playcanvas.com/)

    Средство разработки игр на базе HTML.

