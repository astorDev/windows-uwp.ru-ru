---
author: payzer
title: "Отключение нерабочей области"
description: 
area: Xbox
translationtype: Human Translation
ms.sourcegitcommit: 32a875348debac9aec9f5a26bc4e7e0af2a0a5b4
ms.openlocfilehash: abd06e78364ff32cc10d733e33b153b854dbc467

---

# Рисование границ экрана пользовательского интерфейса   
По умолчанию вокруг области приложения по краям области просмотра есть границы. Это необходимо для учета безопасной для телевизора области. Подробнее: [Проектирование для Xbox и телевизора](http://go.microsoft.com/fwlink/?LinkID=760736#tv-safe-area).  Мы рекомендуем выключить эти границы и рисовать на границах экрана. Для этого добавьте следующий код, когда запускается приложение:
   
`Windows.UI.ViewManagement.ApplicationView.GetForCurrentView().SetDesiredBoundsMode(Windows.UI.ViewManagement.ApplicationViewBoundsMode.UseCoreWindow);`
   
Примечание. Для приложений на C++, использующих /DirectX, об этом беспокоиться не нужно. Система всегда будет отображать приложение вплоть до границ экрана.



<!--HONumber=Jun16_HO5-->


