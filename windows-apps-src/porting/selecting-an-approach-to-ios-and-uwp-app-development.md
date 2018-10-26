---
author: stevewhims
description: Какие варианты доступны при разработке кроссплатформенных приложений?
title: Выбор подхода к разработке приложений iOS и UWP
ms.assetid: 5CDAB313-07B7-4A32-A49B-026361DCC853
ms.author: stwhi
ms.date: 02/08/2017
ms.topic: article
keywords: Windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: c62624e83d1e8334cce711869088817e2f9dc5e0
ms.sourcegitcommit: 6cc275f2151f78db40c11ace381ee2d35f0155f9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/26/2018
ms.locfileid: "5566265"
---
# <a name="selecting-an-approach-to-ios-and-uwp-app-development"></a>Выбор подхода к разработке приложений iOS и UWP


Какие варианты доступны при разработке кроссплатформенных приложений?

## <a name="whats-the-best-way-to-support-both-ios-and-windows"></a>Какой вариант лучше всего поддерживает и iOS, и Windows?

Может показаться, что Windows и iOS очень сильно отличаются, но множество инструментов и методов, число которых растет, могут существенно упростить создание приложений, поддерживающих обе платформы (а также Android). Лучшее решение зависит от типа создаваемого приложения и от того, начинаете ли вы работу с нуля или портируете существующий проект.

## <a name="writing-a-new-app"></a>Создание нового приложения

Если вы начинаете с чистого листа, в вашем распоряжении множество возможностей, в том числе:

-   [Xamarin](http://go.microsoft.com/fwlink/p/?LinkID=320484)

    С помощью Xamarin можно написать приложение на C#, работающее в Windows, а также создать собственные приложения для iOS. Поддержка Xamarin встроена в Visual Studio, поэтому просто выберите соответствующий тип проекта.

-   [Apache Cordova](http://go.microsoft.com/fwlink/p/?LinkID=400439)

    Если вам больше по душе JavaScript и HTML, Apache Cordova (другое название— PhoneGap) поможет вам создать кроссплатформенные приложения для iOS, Windows и Android. Этот тип проекта также встроен в Visual Studio.

-   Игровые модули

    С помощью таких средств, как [Unity3D](http://go.microsoft.com/fwlink/p/?LinkID=394062) и [Unreal Engine](http://go.microsoft.com/fwlink/p/?LinkID=320479) вы можете разрабатывать игры высочайшего качества для Windows и многих других платформ, включая iOS. Unity поддерживает скрипты C#, Unreal использует C++.

-   [MonoGame](http://go.microsoft.com/fwlink/p/?LinkID=320483)

    Это духовный правопреемник XNA. Теперь это платформа с открытым кодом для создания кроссплатформенных приложений, т. е. вы можете создавать приложения на C# для многочисленных платформ с поддержкой физического модуля, а также двухмерной и трехмерной графики.

## <a name="adapting-an-existing-app"></a>Адаптация существующего приложения

Если у вас есть существующее приложение для iOS, число вариантов немного меньше. Однако все далеко не потеряно.

-   [Мост Windows для iOS](https://go.microsoft.com/fwlink/p/?LinkId=619014)

    Это средство также известно как Project Islandwood. Оно все еще находится в стадии разработки и может импортировать проекты Xcode непосредственно в Visual Studio. Код Objective-C можно скомпилировать и отладить в Visual Studio. Если ваш проект использует библиотеки, например Cocos для графики, этот способ позволит быстро перенести ваше приложение.

-   Измените назначение своего кода C++.

    Если ваша основная бизнес-логика написана на C++, а не Objective-C или Swift, зачастую можно использовать этот код, внеся в проект незначительные изменения. Затем можно использовать XAML для определения пользовательского интерфейса, как и в других приложениях для Windows, и при необходимости вызывать код C++.

-   [Запуск OpenGLES в Windows с помощью ANGLE](http://go.microsoft.com/fwlink/p/?linkid=618387)

    В качестве промежуточного действия при портировании проекта OpenGLES2.0 можно использовать ANGLE. ANGLE позволяет запускать содержимое OpenGLES в Windows путем преобразования вызовов API OpenGLES в вызовы API DirectX11.

## <a name="other-cross-platform-authoring-tools"></a>Другие средства разработки кроссплатформенных приложений

-   [GameSalad](http://go.microsoft.com/fwlink/p/?LinkID=320480)

    Среда для разработки игр.

-   [Construct 2]( http://go.microsoft.com/fwlink/p/?LinkID=320481)

    Среда для разработки игр.

-   [Titanium Studio](http://go.microsoft.com/fwlink/p/?LinkID=320482)

    Кроссплатформенная среда для разработки.

-   [Cocos2D-x](http://go.microsoft.com/fwlink/p/?LinkID=320485)

    Кроссплатформенная библиотека кода для обработки спрайтовой графики и физического моделирования.

-   [Impact.js](http://go.microsoft.com/fwlink/p/?LinkID=320486)

    Библиотека игр на базе HTML.

-   [Marmalade](http://go.microsoft.com/fwlink/p/?LinkID=320487)

    Кроссплатформенный пакет SDK.

-   [OpenFL](http://go.microsoft.com/fwlink/p/?LinkID=320488)

    Кроссплатформенное средство разработки.

-   [GameMaker](http://go.microsoft.com/fwlink/p/?LinkID=320490)

    Среда разработки, специально созданная для игр.

-   [PlayCanvas](http://go.microsoft.com/fwlink/p/?LinkID=394061)

    Средство разработки игр на базе HTML.

