---
title: Рисование пользовательского интерфейса до края экрана
description: Как отключить автоматическое масштабирование для безопасной области заголовка.
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.assetid: 1adb221f-6f70-4255-9329-2046a486ca45
ms.localizationpriority: medium
ms.openlocfilehash: 1ac49d80f1d99a56eff565a0daa8f2f3e9289636
ms.sourcegitcommit: d2517e522cacc5240f7dffd5bc1eaa278e3f7768
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/30/2018
ms.locfileid: "8328625"
---
# <a name="how-to-draw-ui-to-the-edge-of-the-screen"></a>Рисование пользовательского интерфейса до края экрана   
По умолчанию вокруг области приложения по краям области просмотра есть границы, необходимые для учета безопасной для телевизора области (Дополнительные сведения см. в разделе [Проектирование для Xbox и Телевизора](../design/devices/designing-for-tv.md#tv-safe-area)). 

Мы рекомендуем выключить эти границы и рисовать на границах экрана. Для этого добавьте следующий код, когда запускается приложение:
   
```
Windows.UI.ViewManagement.ApplicationView.GetForCurrentView().SetDesiredBoundsMode(Windows.UI.ViewManagement.ApplicationViewBoundsMode.UseCoreWindow);
```
   
> [!NOTE]
> Для приложений на C++, использующих /DirectX, об этом беспокоиться не нужно. Система всегда будет отображать приложение вплоть до границ экрана.

## <a name="see-also"></a>См. также
- [Рекомендации для Xbox](tailoring-for-xbox.md)
- [Приложения UWP для Xbox One](index.md)
