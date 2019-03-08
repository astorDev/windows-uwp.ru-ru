---
description: Какие варианты доступны при разработке кроссплатформенных приложений?
title: Выбор подхода к разработке приложений iOS и UWP
ms.assetid: 5CDAB313-07B7-4A32-A49B-026361DCC853
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 9bf23926a1c17615db5ef838d21f9a46a8921c8c
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57655789"
---
# <a name="selecting-an-approach-to-ios-and-uwp-app-development"></a>Выбор подхода к разработке приложений iOS и UWP


Какие варианты доступны при разработке кроссплатформенных приложений?

## <a name="whats-the-best-way-to-support-both-ios-and-windows"></a>Какой вариант лучше всего поддерживает и iOS, и Windows?

Может показаться, что Windows и iOS очень сильно отличаются, но множество инструментов и методов, число которых растет, могут существенно упростить создание приложений, поддерживающих обе платформы (а также Android). Лучшее решение зависит от типа создаваемого приложения и от того, начинаете ли вы работу с нуля или портируете существующий проект.

## <a name="writing-a-new-app"></a>Создание нового приложения

Если вы начинаете с чистого листа, в вашем распоряжении множество возможностей, в том числе:

-   [Xamarin](https://go.microsoft.com/fwlink/p/?LinkID=320484)

    С помощью Xamarin можно написать приложение на C#, работающее в Windows, а также создать собственные приложения для iOS. Поддержка Xamarin встроена в Visual Studio, поэтому просто выберите соответствующий тип проекта.

-   [Apache Cordova](https://go.microsoft.com/fwlink/p/?LinkID=400439)

    Если вам больше по душе JavaScript и HTML, Apache Cordova (другое название — PhoneGap) поможет вам создать кроссплатформенные приложения для iOS, Windows и Android. Этот тип проекта также встроен в Visual Studio.

-   Игровые модули

    С помощью таких средств, как [Unity3D](https://go.microsoft.com/fwlink/p/?LinkID=394062) и [Unreal Engine](https://go.microsoft.com/fwlink/p/?LinkID=320479) вы можете разрабатывать игры высочайшего качества для Windows и многих других платформ, включая iOS. Unity поддерживает скрипты C#, Unreal использует C++.

-   [MonoGame](https://go.microsoft.com/fwlink/p/?LinkID=320483)

    Это духовный правопреемник XNA. Теперь это платформа с открытым кодом для создания кроссплатформенных приложений, т. е. вы можете создавать приложения на C# для многочисленных платформ с поддержкой физического модуля, а также двухмерной и трехмерной графики.

## <a name="adapting-an-existing-app"></a>Адаптация существующего приложения

Если у вас есть существующее приложение для iOS, число вариантов немного меньше. Однако все далеко не потеряно.

-   [Мост Windows для iOS](https://go.microsoft.com/fwlink/p/?LinkId=619014)

    Это средство также известно как Project Islandwood. Оно все еще находится в стадии разработки и может импортировать проекты Xcode непосредственно в Visual Studio. Код Objective-C можно скомпилировать и отладить в Visual Studio. Если ваш проект использует библиотеки, например Cocos для графики, этот способ позволит быстро перенести ваше приложение.

-   Измените назначение своего кода C++.

    Если ваша основная бизнес-логика написана на C++, а не Objective-C или Swift, зачастую можно использовать этот код, внеся в проект незначительные изменения. Затем можно использовать XAML для определения пользовательского интерфейса, как и в других приложениях для Windows, и при необходимости вызывать код C++.

-   [Использовать УГОЛ OpenGL ES под управлением Windows](https://go.microsoft.com/fwlink/p/?linkid=618387)

    В качестве промежуточного действия при портировании проекта OpenGL ES 2.0 можно использовать ANGLE. ANGLE позволяет запускать содержимое OpenGL ES в Windows путем преобразования вызовов API OpenGL ES в вызовы API DirectX 11.

## <a name="other-cross-platform-authoring-tools"></a>Другие средства разработки кроссплатформенных приложений

-   [GameSalad](https://go.microsoft.com/fwlink/p/?LinkID=320480)

    Среда для разработки игр.

-   [Construct 2]( https://go.microsoft.com/fwlink/p/?LinkID=320481)

    Среда для разработки игр.

-   [Titanium Studio](https://go.microsoft.com/fwlink/p/?LinkID=320482)

    Кроссплатформенная среда для разработки.

-   [Cocos2D-x](https://go.microsoft.com/fwlink/p/?LinkID=320485)

    Кроссплатформенная библиотека кода для обработки спрайтовой графики и физического моделирования.

-   [Impact.js](https://go.microsoft.com/fwlink/p/?LinkID=320486)

    Библиотека игр на базе HTML.

-   [Дольки](https://go.microsoft.com/fwlink/p/?LinkID=320487)

    Кроссплатформенный пакет SDK.

-   [OpenFL](https://go.microsoft.com/fwlink/p/?LinkID=320488)

    Кроссплатформенное средство разработки.

-   [GameMaker](https://go.microsoft.com/fwlink/p/?LinkID=320490)

    Среда разработки, специально созданная для игр.

-   [PlayCanvas](https://go.microsoft.com/fwlink/p/?LinkID=394061)

    Средство разработки игр на базе HTML.

