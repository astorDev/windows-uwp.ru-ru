---
author: muhsinking
ms.assetid: D06AA3F5-CED6-446E-94E8-713D98B13CAA
title: Создание средства выбора устройств
description: Создание средства выбора устройства позволяет ограничить количество устройств, по которым выполняется поиск при перечислении.
ms.author: mukin
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 036ea8b7d9797112dca9b6594e9bc1e33e923588
ms.sourcegitcommit: ed0304b8a214c03b8aab74b8ef12c9f82b8e3c5f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/19/2018
ms.locfileid: "7291151"
---
# <a name="build-a-device-selector"></a>Создание средства выбора устройств



**Важные API**

- [**Windows.Devices.Enumeration**](https://docs.microsoft.com/en-us/uwp/api/Windows.Devices.Enumeration)

Создание средства выбора устройства позволяет ограничить количество устройств, по которым выполняется поиск при перечислении. Благодаря этому вы не только будете получать подходящие результаты, но и улучшите производительность системы. В большинстве случаев вы получаете средство выбора устройств из его стека. Например, для устройств, которые обнаруживаются по USB, можно использовать команду [**GetDeviceSelector**](https://msdn.microsoft.com/library/windows/apps/Dn264015). Эти средства выбора устройств возвращают строку расширенного синтаксиса запросов (AQS). Если вы не знакомы с форматом AQS, подробнее о нем можно узнать в статье [Программное использование дополнительного синтаксиса запроса](https://msdn.microsoft.com/library/windows/desktop/Bb266512).

## <a name="building-the-filter-string"></a>Формирование строки фильтра

В ряде случаев предоставленное средство выбора устройств недоступно для сценария, когда требуется перечислить устройства. Средство выбора устройств — это строка фильтра AQS, которая содержит следующие сведения. Прежде чем создать строку фильтрации, необходимо знать некоторые ключевые сведения об устройствах, которые вы хотите перечислить.

-   [**DeviceInformationKind**](https://msdn.microsoft.com/library/windows/apps/Dn948991) устройств, которые вам необходимы. Дополнительные сведения о влиянии **DeviceInformationKind** на перечисление устройств см. в разделе [Перечисление устройств](enumerate-devices.md).
-   Построение строки фильтра AQS, описанное в этом разделе.
-   Необходимые свойства. Доступные свойства будут зависеть от [**DeviceInformationKind**](https://msdn.microsoft.com/library/windows/apps/Dn948991). Дополнительные сведения см. в статье [Свойства сведений об устройстве](device-information-properties.md).
-   Протоколы, которые вы используете для запроса. Это требуется, только если вы выполняете поиск устройств по беспроводной или проводной сети. Подробнее: [Перечисление устройств по сети](enumerate-devices-over-a-network.md).

При использовании API [**Windows.Devices.Enumeration**](https://msdn.microsoft.com/library/windows/apps/BR225459) средство выбора устройств часто объединяется с нужным типом устройства. Доступный список типов устройств определяется перечислением [**DeviceInformationKind**](https://msdn.microsoft.com/library/windows/apps/Dn948991). Такое сочетание факторов позволяет ограничить доступные устройства нужными вам устройствами. Если перечисление **DeviceInformationKind** не указано или используемый вами метод не предоставляет параметр **DeviceInformationKind**, по умолчанию используется тип **DeviceInterface**.

API [**Windows.Devices.Enumeration**](https://msdn.microsoft.com/library/windows/apps/BR225459) используют канонический синтаксис AQS, но не все операторы поддерживаются. Список доступных при построении строки фильтра свойств см. в разделе [Свойства сведений об устройстве](device-information-properties.md).

**Внимание**пользовательские свойства, которые определяются с помощью `{GUID} PID` формат не могут использоваться при построении строки фильтра aqs. Это вызвано тем, что данный тип свойства образован на основе имени известного свойства.

 

В таблице ниже перечислены операторы AQS и типы параметров, которые они поддерживают.

| Оператор                       | Поддерживаемые типы                                                             |
|--------------------------------|-----------------------------------------------------------------------------|
| **COP\_EQUAL**                 | Строковый, логический, GUID, UInt16, UInt32                                       |
| **COP\_NOTEQUAL**              | Строковый, логический, GUID, UInt16, UInt32                                       |
| **COP\_LESSTHAN**              | UInt16, UInt32                                                              |
| **COP\_GREATERTHAN**           | UInt16, UInt32                                                              |
| **COP\_LESSTHANOREQUAL**       | UInt16, UInt32                                                              |
| **COP\_GREATERTHANOREQUAL**    | UInt16, UInt32                                                              |
| **COP\_VALUE\_CONTAINS**       | Строковый, строковый массив, логический массив, массив GUID, массив UInt16, массив UInt32 |
| **COP\_VALUE\_NOTCONTAINS**    | Строковый, строковый массив, логический массив, массив GUID, массив UInt16, массив UInt32 |
| **COP\_VALUE\_STARTSWITH**     | Строка                                                                      |
| **COP\_VALUE\_ENDSWITH**       | Строка                                                                      |
| **COP\_DOSWILDCARDS**          | Не поддерживается                                                               |
| **COP\_WORD\_EQUAL**           | Не поддерживается                                                               |
| **COP\_WORD\_STARTSWITH**      | Не поддерживается                                                               |
| **COP\_APPLICATION\_SPECIFIC** | Не поддерживается                                                               |


> **Совет**можно указать **значение NULL** для **COP\_EQUAL** или **COP\_NOTEQUAL**. Это означает свойство без значения или несуществующее значение. В AQS для указания **NULL** используются пустые скобки \[\].

> **Важные**при использовании **COP\_VALUE\_CONTAINS** и **COP\_VALUE\_NOTCONTAINS** операторы, ведут себя по-разному со строками и строковыми массивами. В случае со строками система выполняет поиск без учета регистра, чтобы определить, содержит ли устройство указанную строку в качестве подстроки. В случае со строковыми массивами поиск по подстрокам не выполняется. В таком случае поиск выполняется по массиву, чтобы определить, содержит ли он указанную строку полностью. Выполнить поиск массива строк, чтобы определить, содержат ли элементы массива подстроку, невозможно.

Если вы не можете создать одну строку фильтра AQS, которая будет определять область результатов соответствующим образом, вы можете отфильтровать результаты после их получения. Но если вы выберете этот вариант, мы рекомендуем максимально ограничить результаты из начальной строки AQS фильтра при ее предоставлении интерфейсам API [**Windows.Devices.Enumeration**](https://msdn.microsoft.com/library/windows/apps/BR225459). Это повысит производительность вашего приложения.

## <a name="aqs-string-examples"></a>Примеры строк AQS

В следующих примерах показано, как можно использовать синтаксис AQS для ограничения количества устройств, которые нужно перечислить. Все эти строки фильтров используются вместе с [**DeviceInformationKind**](https://msdn.microsoft.com/library/windows/apps/Dn948991), чтобы создать полный фильтр. Помните, что если тип не указан, по умолчанию используется тип **DeviceInterface**.

Когда этот фильтр используется с типом [**DeviceInformationKind**](https://msdn.microsoft.com/library/windows/apps/Dn948991) объекта **DeviceInterface**, он перечисляет все объекты, которые сейчас включены и в которых содержится класс интерфейса Audio Capture. **=** преобразуется в **COP\_EQUALS**.

``` syntax
System.Devices.InterfaceClassGuid:="{2eef81be-33fa-4800-9670-1cd474972c3f}" AND
System.Devices.InterfaceEnabled:=System.StructuredQueryType.Boolean#True
```

Когда этот фильтр используется с типом [**DeviceInformationKind**](https://msdn.microsoft.com/library/windows/apps/Dn948991) объекта **Device**, он перечисляет все объекты, в которых имеется не менее одного аппаратного идентификатора GenCdRom. **~~** преобразуется в **COP\_VALUE\_CONTAINS**.

``` syntax
System.Devices.HardwareIds:~~"GenCdRom"
```

Когда этот фильтр используется с типом [**DeviceInformationKind**](https://msdn.microsoft.com/library/windows/apps/Dn948991) объекта **DeviceContainer**, он перечисляет все объекты с названием модели с подстрокой Microsoft. **~~** преобразуется в **COP\_VALUE\_CONTAINS**.

``` syntax
System.Devices.ModelName:~~"Microsoft"
```

Когда этот фильтр используется с типом [**DeviceInformationKind**](https://msdn.microsoft.com/library/windows/apps/Dn948991) объекта **DeviceInterface**, он перечисляет все объекты с названием модели, которое начинается с подстроки Microsoft. **~&lt;** преобразуется в **COP\_STARTSWITH**.

``` syntax
System.ItemNameDisplay:~<"Microsoft"
```

Когда этот фильтр используется с типом [**DeviceInformationKind**](https://msdn.microsoft.com/library/windows/apps/Dn948991) объекта **Device**, он перечисляет все объекты, для которых задан параметр **System.Devices.IpAddress**. **&lt;&gt;\[\]** преобразуется в **COP\_NOTEQUALS** в сочетании со значением **NULL**.

``` syntax
System.Devices.IpAddress:<>[]
```

Когда этот фильтр используется с типом [**DeviceInformationKind**](https://msdn.microsoft.com/library/windows/apps/Dn948991) объекта **Device**, он перечисляет все объекты, для которых не задан параметр **System.Devices.IpAddress**. **=\[\]** преобразуется в **COP\_EQUALS** в сочетании со значением **NULL**.

``` syntax
System.Devices.IpAddress:=[]
```

 

 
