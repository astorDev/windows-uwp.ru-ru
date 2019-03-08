---
title: Получение пользователя системы Windows на UWP
description: Узнайте, как получить пользователя системы Windows в игре универсальной платформы Windows (UWP).
ms.date: 06/07/2017
ms.topic: article
keywords: Xbox live, xbox, игры, универсальной платформы Windows, windows 10, Системный пользователь
ms.localizationpriority: medium
ms.openlocfilehash: c46f7e98c2dea3b23beb2cec80816067d4c4e341
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57632759"
---
# <a name="retrieving-the-windows-system-user-in-a-universal-windows-platform-uwp-title"></a>Получение пользователя системы Windows в заголовке универсальной платформы Windows (UWP)

## <a name="windowssystemuser"></a>Windows.System.User

Объект [Windows.System.User](https://docs.microsoft.com/en-us/uwp/api/windows.system.user) представляет локального пользователя Windows. На консоли Xbox он позволяет несколько пользователя windows, вход в систему в то же время один интерактивный сеанс, если приложение является приложением нескольких пользователей, то объект Windows.System.User потребовался бы для построения XboxLiveUser для доступа к службам live. На других платформах windows, например ПК или телефона он имеет либо разрешить только один пользователь windows на одном устройстве, или один интерактивный сеанс, передав Windows.System.User для создания XboxLiveUser не является обязательным.

## <a name="ways-to-retrieve-windows-system-user"></a>Способы получения пользователя системы Windows

* **С помощью статических методов из [Windows.System.User](https://docs.microsoft.com/en-us/uwp/api/windows.system.user) класса.**

  [Windows.System.User](https://docs.microsoft.com/en-us/uwp/api/windows.system.user) класс предоставляет набор статический метод для извлечения объектов Windows.System.User. Например можно назвать FindAllAsync для получения всех пользователей активные окна.

* **[UserPicker](https://docs.microsoft.com/en-us/uwp/api/windows.system.userpicker)**

  [Windows.System.UserPicker](https://docs.microsoft.com/en-us/uwp/api/windows.system.userpicker) класс предоставляет методы для запуска средства выбора пользователя и выберите пользователя системы windows в сценариях с несколькими пользователями.

* **[IGameController](https://docs.microsoft.com/en-us/uwp/api/windows.gaming.input.igamecontroller)**

  [Windows.Gaming.Input.IGameController](https://docs.microsoft.com/en-us/uwp/api/windows.gaming.input.igamecontroller) -это базовый интерфейс для всех устройств контроллера (игровой, гоночную колесика, Рукоятка и т.д.). Может появиться Windows.System.User объекта, связанного с игровой контроллер, путем вызова свойства пользователя.  

  Ниже приведены несколько игровой контроллер, реализован windows [ArcadeStick](https://docs.microsoft.com/en-us/uwp/api/windows.gaming.input.arcadestick), [FlightStick](https://docs.microsoft.com/en-us/uwp/api/windows.gaming.input.flightstick), [Gamepad](https://docs.microsoft.com/en-us/uwp/api/windows.gaming.input.gamepad), [RacingWheel](https://docs.microsoft.com/en-us/uwp/api/windows.gaming.input.racingwheel).

* **[UserDeviceAssociation](https://docs.microsoft.com/en-us/uwp/api/windows.system.userdeviceassociation)**

  Можно вызвать статический метод FindUserFromDeviceId для поиска соответствующего пользователя с идентификатором устройства. Обычно можно получить идентификатор устройства из входных событий windows, таких как [Windows.UI.Xaml.Input.KeyRoutedEventArgs](https://docs.microsoft.com/en-us/uwp/api/Windows.UI.Xaml.Input.KeyRoutedEventArgs), [Windows.UI.Core.KeyEventArgs](https://docs.microsoft.com/en-us/uwp/api/windows.ui.core.keyeventargs)
