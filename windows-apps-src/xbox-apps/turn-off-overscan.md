---
author: payzer
title: "Рисование пользовательского интерфейса до края экрана"
description: 
ms.author: wdg-dev-content
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp
ms.assetid: 1adb221f-6f70-4255-9329-2046a486ca45
translationtype: Human Translation
ms.sourcegitcommit: 5645eee3dc2ef67b5263b08800b0f96eb8a0a7da
ms.openlocfilehash: 9a221672391dfbfb4af664438448307800020c6f
ms.lasthandoff: 02/08/2017

---

# <a name="how-to-draw-ui-to-the-edge-of-the-screen"></a>Рисование пользовательского интерфейса до края экрана   
По умолчанию у приложений есть границы по краям окна просмотра, необходимые для учета безопасной для телевизора области (дополнительные сведения см. в разделе [Проектирование для Xbox и телевизора](../input-and-devices/designing-for-tv.md#tv-safe-area)). 

Мы рекомендуем выключить эти границы и рисовать до края экрана. Для этого добавьте следующий код, когда запускается приложение:
   
```
Windows.UI.ViewManagement.ApplicationView.GetForCurrentView().SetDesiredBoundsMode(Windows.UI.ViewManagement.ApplicationViewBoundsMode.UseCoreWindow);
```
   
> [!NOTE]
> Для приложений на C++, использующих /DirectX, об этом беспокоиться не нужно. Система всегда будет отрисовывать ваше приложение до самого края экрана.

## <a name="see-also"></a>См. также
- [Рекомендации для Xbox](tailoring-for-xbox.md)
- [Приложения UWP для Xbox One](index.md)

