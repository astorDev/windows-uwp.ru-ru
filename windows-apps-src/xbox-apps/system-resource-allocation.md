---
author: Mtoepke
title: Системные ресурсы для приложений UWP и игр для Xbox One
description: UWP в системных ресурсах Xbox
ms.author: mstahl
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp
ms.assetid: 12e87019-4315-424e-b73c-426d565deef9
ms.localizationpriority: medium
ms.openlocfilehash: 8d6ebe8e3344f5276939471d7ac569ae83d48311
ms.sourcegitcommit: c6d6f8b54253e79354f8db14e5cf3b113a3e5014
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/24/2018
ms.locfileid: "2836559"
---
# <a name="system-resources-for-uwp-apps-and-games-on-xbox-one"></a>Системные ресурсы для приложений UWP и игр для Xbox One

Приложения UWP, запущенные в Xbox One, используют ресурсы совместно с системой и приложениями. Ресурсы, доступные приложению UWP на Xbox One, зависят от того, отправляете ли вы его как приложение или как игру по программе Xbox Live Creators Program.

* Максимальный доступный объем памяти для приложения, работающего на переднем плане:
    * Приложения: 1 ГБ
    * Игры: 5 ГБ

Максимальный доступный объем памяти для приложения, работающего в фоновом режиме — 128 МБ. Фоновый режим применяется только к одновременно выполняющимся приложениям, например музыкальным проигрывателям в фоновом режиме.  Работа игр приостанавливается и завершается в фоновом режиме.

Превышение этих ограничений вызовет сбой выделения памяти. Дополнительные сведения об отслеживании использования памяти см. описании [класса MemoryManager](https://msdn.microsoft.com/library/windows/apps/windows.system.memorymanager.aspx).
    
    > [!NOTE]
    > When running your app or game from the Visual Studio debugger, these memory constraints do not apply. This limit is only applicable when not running in debugging mode.

* ЦП
    * Приложения: общее использование 2–4 ядер процессора в зависимости от количества приложений и игр, запущенных в системе.
    * Игры: 4 эксклюзивных и 2 общих ядра ЦП.

* Графический процессор
    * Приложения: общее использование 45% процессора в зависимости от количества приложений и игр, запущенных в системе.
    * Игры: полный доступ к доступным циклам графического процессора.

* Поддержка DirectX
    * Приложения: DirectX 11, функциональный уровень 10.
    * Игры: DirectX 12 и DirectX 11, функциональный уровень 10.

* Все приложения и игры должны быть предназначены для 64-разрядной архитектуры, чтобы вести их разработку и отправлять в магазин для Xbox.  

Для **разработки приложений** ресурсы могут быть ограничены по сравнению со стандартным ПК и могут варьироваться в зависимости от числа приложений и игр, выполняющихся в системе.

При **разработке игр** Xbox One, как и другие игровые консоли, — это специализированное оборудование, для реализации полного потенциала которого требуется специальный комплект SDK. Если вы работаете над игрой, которая требует максимального использования возможностей оборудования Xbox One, можно зарегистрироваться в программе [ID@Xbox](http://www.xbox.com/Developers/id), чтобы получить доступ к комплекту разработки Xbox One.


Дополнительные сведения о системных ресурсах для приложений UWP на Xbox One см. в первой части этого видео.
</br>
</br>
<iframe src="https://mva.microsoft.com/en-US/training-courses-embed/developing-xbox-one-applications-16860/Video-What-s-Unique--vk0fOPf9C_2006218965" width="636" height="480" allowFullScreen frameBorder="0"></iframe>

## <a name="see-also"></a>См. также:
- [UWP на Xbox One](index.md)
- [Начало работы с программой Xbox Live Creators Program](../xbox-live/get-started-with-creators/get-started-with-xbox-live-creators.md)
- [DirectX и UWP на Xbox один](https://blogs.msdn.microsoft.com/chuckw/2017/12/15/directx-and-uwp-on-xbox-one/)

