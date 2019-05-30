---
ms.assetid: D06AA3F5-CED6-446E-94E8-713D98B13CAA
title: Создание средства выбора устройств
description: Создание средства выбора устройства позволяет ограничить количество устройств, по которым выполняется поиск при перечислении.
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: a9d31eeefffca1320f3222ce25cfb7678178d4ab
ms.sourcegitcommit: ac7f3422f8d83618f9b6b5615a37f8e5c115b3c4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66370394"
---
# <a name="build-a-device-selector"></a>Создание средства выбора устройств



**Важные API**

- [**Windows.Devices.Enumeration**](https://docs.microsoft.com/en-us/uwp/api/Windows.Devices.Enumeration)

Создание средства выбора устройства позволяет ограничить количество устройств, по которым выполняется поиск при перечислении. Благодаря этому вы не только будете получать подходящие результаты, но и улучшите производительность системы. В большинстве случаев вы получаете средство выбора устройств из его стека. Например, для устройств, которые обнаруживаются по USB, можно использовать команду [**GetDeviceSelector**](https://docs.microsoft.com/uwp/api/windows.devices.usb.usbdevice.getdeviceselector). Эти средства выбора устройств возвращают строку расширенного синтаксиса запросов (AQS). Если вы не знакомы с форматом AQS, подробнее о нем можно узнать в статье [Программное использование дополнительного синтаксиса запроса](https://docs.microsoft.com/windows/desktop/search/-search-3x-advancedquerysyntax).

## <a name="building-the-filter-string"></a>Формирование строки фильтра

В ряде случаев предоставленное средство выбора устройств недоступно для сценария, когда требуется перечислить устройства. Средство выбора устройств — это строка фильтра AQS, которая содержит следующие сведения. Прежде чем создать строку фильтрации, необходимо знать некоторые ключевые сведения об устройствах, которые вы хотите перечислить.

-   [  **DeviceInformationKind**](https://docs.microsoft.com/uwp/api/Windows.Devices.Enumeration.DeviceInformationKind) устройств, которые вам необходимы. Дополнительные сведения о влиянии **DeviceInformationKind** на перечисление устройств см. в разделе [Перечисление устройств](enumerate-devices.md).
-   Построение строки фильтра AQS, описанное в этом разделе.
-   Необходимые свойства. Доступные свойства будут зависеть от [**DeviceInformationKind**](https://docs.microsoft.com/uwp/api/Windows.Devices.Enumeration.DeviceInformationKind). Дополнительные сведения см. в статье [Свойства сведений об устройстве](device-information-properties.md).
-   Протоколы, которые вы используете для запроса. Это требуется, только если вы выполняете поиск устройств по беспроводной или проводной сети. Подробнее: [Перечисление устройств по сети](enumerate-devices-over-a-network.md).

При использовании API [**Windows.Devices.Enumeration**](https://docs.microsoft.com/uwp/api/Windows.Devices.Enumeration) средство выбора устройств часто объединяется с нужным типом устройства. Доступный список типов устройств определяется перечислением [**DeviceInformationKind**](https://docs.microsoft.com/uwp/api/Windows.Devices.Enumeration.DeviceInformationKind). Такое сочетание факторов позволяет ограничить доступные устройства нужными вам устройствами. Если перечисление **DeviceInformationKind** не указано или используемый вами метод не предоставляет параметр **DeviceInformationKind**, по умолчанию используется тип **DeviceInterface**.

API [**Windows.Devices.Enumeration**](https://docs.microsoft.com/uwp/api/Windows.Devices.Enumeration) используют канонический синтаксис AQS, но не все операторы поддерживаются. Список доступных при построении строки фильтра свойств см. в разделе [Свойства сведений об устройстве](device-information-properties.md).

**Внимание**  пользовательские свойства, которые определяются с помощью `{GUID} PID` формат не может использоваться при построении строки фильтра AQS. Это вызвано тем, что данный тип свойства образован на основе имени известного свойства.

 

В таблице ниже перечислены операторы AQS и типы параметров, которые они поддерживают.

| Оператор                       | Поддерживаемые типы                                                             |
|--------------------------------|-----------------------------------------------------------------------------|
| **КОП\_РАВНО**                 | Строковый, логический, GUID, UInt16, UInt32                                       |
| **КОП\_NOTEQUAL**              | Строковый, логический, GUID, UInt16, UInt32                                       |
| **КОП\_LESSTHAN**              | UInt16, UInt32                                                              |
| **КОП\_GREATERTHAN**           | UInt16, UInt32                                                              |
| **КОП\_LESSTHANOREQUAL**       | UInt16, UInt32                                                              |
| **КОП\_GREATERTHANOREQUAL**    | UInt16, UInt32                                                              |
| **КОП\_ЗНАЧЕНИЕ\_CONTAINS**       | Строковый, строковый массив, логический массив, массив GUID, массив UInt16, массив UInt32 |
| **КОП\_ЗНАЧЕНИЕ\_NOTCONTAINS**    | Строковый, строковый массив, логический массив, массив GUID, массив UInt16, массив UInt32 |
| **КОП\_ЗНАЧЕНИЕ\_STARTSWITH**     | Строка                                                                      |
| **КОП\_ЗНАЧЕНИЕ\_ENDSWITH**       | Строка                                                                      |
| **КОП\_DOSWILDCARDS**          | Не поддерживается.                                                               |
| **КОП\_WORD\_РАВНО**           | Не поддерживается.                                                               |
| **КОП\_WORD\_STARTSWITH**      | Не поддерживается.                                                               |
| **КОП\_ПРИЛОЖЕНИЯ\_КОНКРЕТНЫХ** | Не поддерживается.                                                               |


> **Совет**  можно указать **NULL** для **COP\_равно** или **COP\_NOTEQUAL**. Это означает свойство без значения или несуществующее значение. В AQS, укажите **NULL** с помощью пустыми скобками \[ \].

> **Важные**  при использовании **COP\_значение\_CONTAINS** и **COP\_значение\_NOTCONTAINS** операторы, с помощью строки и массивы строк ведут себя по-разному. В случае со строками система выполняет поиск без учета регистра, чтобы определить, содержит ли устройство указанную строку в качестве подстроки. В случае со строковыми массивами поиск по подстрокам не выполняется. В таком случае поиск выполняется по массиву, чтобы определить, содержит ли он указанную строку полностью. Выполнить поиск массива строк, чтобы определить, содержат ли элементы массива подстроку, невозможно.

Если вы не можете создать одну строку фильтра AQS, которая будет определять область результатов соответствующим образом, вы можете отфильтровать результаты после их получения. Но если вы выберете этот вариант, мы рекомендуем максимально ограничить результаты из начальной строки AQS фильтра при ее предоставлении интерфейсам API [**Windows.Devices.Enumeration**](https://docs.microsoft.com/uwp/api/Windows.Devices.Enumeration). Это повысит производительность вашего приложения.

## <a name="aqs-string-examples"></a>Примеры строк AQS

В следующих примерах показано, как можно использовать синтаксис AQS для ограничения количества устройств, которые нужно перечислить. Все эти строки фильтров используются вместе с [**DeviceInformationKind**](https://docs.microsoft.com/uwp/api/Windows.Devices.Enumeration.DeviceInformationKind), чтобы создать полный фильтр. Помните, что если тип не указан, по умолчанию используется тип **DeviceInterface**.

Когда этот фильтр используется с типом [**DeviceInformationKind**](https://docs.microsoft.com/uwp/api/Windows.Devices.Enumeration.DeviceInformationKind) объекта **DeviceInterface**, он перечисляет все объекты, которые сейчас включены и в которых содержится класс интерфейса Audio Capture. **=** выполняет преобразование в **COP\_равно**.

``` syntax
System.Devices.InterfaceClassGuid:="{2eef81be-33fa-4800-9670-1cd474972c3f}" AND
System.Devices.InterfaceEnabled:=System.StructuredQueryType.Boolean#True
```

Когда этот фильтр используется с типом [**DeviceInformationKind**](https://docs.microsoft.com/uwp/api/Windows.Devices.Enumeration.DeviceInformationKind) объекта **Device**, он перечисляет все объекты, в которых имеется не менее одного аппаратного идентификатора GenCdRom. **~~** выполняет преобразование в **COP\_значение\_CONTAINS**.

``` syntax
System.Devices.HardwareIds:~~"GenCdRom"
```

Когда этот фильтр используется с типом [**DeviceInformationKind**](https://docs.microsoft.com/uwp/api/Windows.Devices.Enumeration.DeviceInformationKind) объекта **DeviceContainer**, он перечисляет все объекты с названием модели с подстрокой Microsoft. **~~** выполняет преобразование в **COP\_значение\_CONTAINS**.

``` syntax
System.Devices.ModelName:~~"Microsoft"
```

Когда этот фильтр используется с типом [**DeviceInformationKind**](https://docs.microsoft.com/uwp/api/Windows.Devices.Enumeration.DeviceInformationKind) объекта **DeviceInterface**, он перечисляет все объекты с названием модели, которое начинается с подстроки Microsoft. **~&lt;** выполняет преобразование в **COP\_STARTSWITH**.

``` syntax
System.ItemNameDisplay:~<"Microsoft"
```

Когда этот фильтр используется с типом [**DeviceInformationKind**](https://docs.microsoft.com/uwp/api/Windows.Devices.Enumeration.DeviceInformationKind) объекта **Device**, он перечисляет все объекты, для которых задан параметр **System.Devices.IpAddress**. **&lt;&gt;\[\]** выполняет преобразование в **COP\_NOTEQUALS** в сочетании с **NULL** значение.

``` syntax
System.Devices.IpAddress:<>[]
```

Когда этот фильтр используется с типом [**DeviceInformationKind**](https://docs.microsoft.com/uwp/api/Windows.Devices.Enumeration.DeviceInformationKind) объекта **Device**, он перечисляет все объекты, для которых не задан параметр **System.Devices.IpAddress**. **=\[\]** выполняет преобразование в **COP\_равно** в сочетании с **NULL** значение.

``` syntax
System.Devices.IpAddress:=[]
```

 

 
