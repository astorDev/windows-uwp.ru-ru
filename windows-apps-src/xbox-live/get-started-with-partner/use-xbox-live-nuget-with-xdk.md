---
title: Использовать Xbox Live NuGet с XDK
description: Узнайте, как использовать пакет NuGet для API Xbox Live для разработки заголовки XDK.
ms.assetid: 2c5ae514-393d-48bb-afd8-a897d35f7938
ms.date: 04/04/2017
ms.topic: article
keywords: Xbox live, xbox, игры, универсальной платформы Windows, windows 10 для настольных ПК, xbox, один NuGet
ms.localizationpriority: medium
ms.openlocfilehash: c955ca42c09075e5125683588c335cfa47443f00
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57655039"
---
# <a name="use-the-xbox-live-api-nuget-package-to-develop-xdk-titles"></a>Используйте пакет NuGet для API Xbox Live для разработки XDK заголовки

### <a name="1--ensure-you-have-the-latest-nuget-package-manager-installed"></a>1.  Убедитесь, что у вас установлена последняя версия диспетчера пакетов NuGet
1.  Проверка текущей версии:
    - В меню, выберите Инструменты "->" расширения и обновления.
    - В списке вкладку "установлено" найдите `NuGet Package Manager`
![](../images/nuget/nuget_uwp_install_1.png)
2.  Обновление текущей версии:
    - В меню, выберите Инструменты "->" расширения и обновления.
    - В группе обновлений -> вкладка коллекции Visual Studio, выберите `Update`
![](../images/nuget/nuget_uwp_install_2.png)

### <a name="2--add-reference-to-the-project"></a>2.  Добавьте ссылку на проект
1.  Добавьте ссылку на проект
    1.  Щелкните правой кнопкой решение проект и выберите пункт «Управление пакетами NuGet»
<br/>
![](../images/nuget/nuget_xbox_install_4.png)
1.  Поиск `Xbox Live` выберите соответствующий пакет и нажмите кнопку `Install`.
  - API службы Xbox имеет вариации для универсальной платформы Windows и XDK, а также для C++ и WinRT.  
  - Выбор между `Microsoft.Xbox.Live.SDK.*.UWP` и `Microsoft.Xbox.Live.SDK.*.XboxOneXDK`.  `XboxOneXDK` для ID@Xbox и управляемый разработчики, использующие один XDK Xbox.  `UWP` — для универсальной платформы Windows игр, которые могут выполняться на ПК, Xbox One или Windows Phone.  Дополнительные сведения об универсальной платформы Windows на Xbox One в [https://docs.microsoft.com/en-us/windows/uwp/xbox-apps/getting-started](https://docs.microsoft.com/en-us/windows/uwp/xbox-apps/getting-started)
  - Выбор между `Microsoft.Xbox.Live.SDK.Cpp.*` и `Microsoft.Xbox.Live.SDK.WinRT.*`. `Cpp` — для игровых движков C++ с помощью API-интерфейсы Xbox Live.  `WinRT` для игровых движков, написанный на языке C++, C#, или Javascript с помощью API-интерфейсы Xbox Live.  При использовании WinRT с обработчиком C++, используется C + +/ CX, использующего шляпы (^).  `Cpp` Рекомендуется использовать для игровых движков C++.    
![](../images/nuget/nuget_xbox_install_5.png)
![](../images/nuget/nuget_uwp_install_7.png)
1. После принятия TOS лицензии, подождите, пока пакет успешно добавлен.  Вы должны увидеть этот журнал, в окне вывода диспетчера пакетов:

```
========== Finished ==========
```

### <a name="3--optionally-include-header"></a>3.  При необходимости включите заголовок
* Для `Microsoft.Xbox.Live.SDK.Cpp.*` проектов на основе `#include <xsapi\services.h>` в исходном проекте.
* Для `Microsoft.Xbox.Live.SDK.WinRT.*` на основе проектов, нет необходимости включать какие-либо заголовки.   
