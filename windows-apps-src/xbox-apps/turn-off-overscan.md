---
author: payzer
title: Рисование пользовательского интерфейса до края экрана
description: Как отключить автоматическое масштабирование для безопасной области заголовка.
ms.author: wdg-dev-content
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp
ms.assetid: 1adb221f-6f70-4255-9329-2046a486ca45
ms.openlocfilehash: 30fc3e357eaea0d36a5deba1b0ea85c2d9bc990e
ms.sourcegitcommit: 909d859a0f11981a8d1beac0da35f779786a6889
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.locfileid: "225100"
---
# <a name="how-to-draw-ui-to-the-edge-of-the-screen"></a>Рисование пользовательского интерфейса до края экрана   
По умолчанию вокруг области приложения по краям области просмотра есть границы, необходимые для учета безопасной для телевизора области (Дополнительные сведения см. в разделе [Проектирование для Xbox и Телевизора](../input-and-devices/designing-for-tv.md#tv-safe-area)). 

Мы рекомендуем выключить эти границы и рисовать на границах экрана. Для этого добавьте следующий код, когда запускается приложение:
   
```
Windows.UI.ViewManagement.ApplicationView.GetForCurrentView().SetDesiredBoundsMode(Windows.UI.ViewManagement.ApplicationViewBoundsMode.UseCoreWindow);
```
   
> [!NOTE]
> Для приложений на C++, использующих /DirectX, об этом беспокоиться не нужно. Система всегда будет отображать приложение вплоть до границ экрана.

## <a name="see-also"></a>См. также
- [Рекомендации для Xbox](tailoring-for-xbox.md)
- [Приложения UWP для Xbox One](index.md)
