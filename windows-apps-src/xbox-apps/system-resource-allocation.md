---
title: Системные ресурсы для приложений UWP и игр для Xbox One
description: UWP в системных ресурсах Xbox
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.assetid: 12e87019-4315-424e-b73c-426d565deef9
ms.localizationpriority: medium
ms.openlocfilehash: 0869f5cfc2499a00577f0196cd9f9f84987c0321
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57647329"
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
    * Игры: 4 эксклюзивное и 2 общих ядер ЦП.

* Графический процессор
    * Приложения: общее использование 45 % процессора в зависимости от количества приложений и игр, запущенных в системе.
    * Игры: полный доступ к доступным циклам графического процессора.

* Поддержка DirectX
    * Приложения: Уровень компонентов DirectX 11 10.
    * Игры: DirectX 12, а уровень возможностей DirectX 11 10.

* Все приложения и игры должны быть предназначены для 64-разрядной архитектуры, чтобы вести их разработку и отправлять в магазин для Xbox.  

Для **разработки приложений** ресурсы могут быть ограничены по сравнению со стандартным ПК и могут варьироваться в зависимости от числа приложений и игр, выполняющихся в системе.

При **разработке игр** Xbox One, как и другие игровые консоли, — это специализированное оборудование, для реализации полного потенциала которого требуется специальный комплект SDK. Если вы работаете над игрой, которая требует максимального использования возможностей оборудования Xbox One, можно зарегистрироваться в программе [ID@Xbox](https://www.xbox.com/Developers/id), чтобы получить доступ к комплекту разработки Xbox One.


Дополнительные сведения о системных ресурсах для приложений UWP на Xbox One см. в первой части этого видео.
</br>
</br>
<iframe src="https://mva.microsoft.com/en-US/training-courses-embed/developing-xbox-one-applications-16860/Video-What-s-Unique--vk0fOPf9C_2006218965" width="636" height="480" allowFullScreen frameBorder="0"></iframe>

## <a name="see-also"></a>См. также
- [Приложения UWP для Xbox One](index.md)
- [Начало работы с Xbox Live Creators Program](../xbox-live/get-started-with-creators/get-started-with-xbox-live-creators.md)
- [DirectX и универсальной платформы Windows на Xbox One](https://blogs.msdn.microsoft.com/chuckw/2017/12/15/directx-and-uwp-on-xbox-one/)

