---
author: TerryWarwick
title: Перечисление устройств PointOfService
description: Узнайте, как перечислять устройства PointOfService
ms.author: jken
ms.date: 06/8/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: Windows 10, UWP, точка обслуживания, POS
ms.localizationpriority: medium
ms.openlocfilehash: be1fdc42295fc03ff86a69e287a4089abe547689
ms.sourcegitcommit: ee77826642fe8fd9cfd9858d61bc05a96ff1bad7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/11/2018
ms.locfileid: "2018442"
---
# <a name="enumerating-point-of-service-devices"></a>Перечисление POS-устройств
В этом разделе вы узнайте, как [**определить средство выбора устройств**](https://docs.microsoft.com/windows/uwp/devices-sensors/build-a-device-selector), использующееся для запроса устройств, доступных системе, а также, как использовать это средство выбора для перечисления POS-устройств с помощью одного из следующих способов:

**Способ 1.** [**Получение первого доступного устройства**](#Method-1:-get-first-available-device)<br />В этом разделе вы узнаете, как использовать **GetDefaultAsync** для доступа к первому доступному устройству в определенном классе устройств PointOfService.

**Способ 2.** [**Снимок устройств**](#Method-2:-Snapshot-of-devices)<br />В этом разделе вы узнаете, как выполнить перечисление снимка устройств PointOfService, имеющихся в системе в определенный момент времени. Это удобно при создании собственного пользовательского интерфейса или перечислении устройств без отображения интерфейса для пользователя. Метод FindAllAsync будет удерживать результаты до полного завершения перечисления.

**Способ 3.** [**Перечисление и отслеживание**](#Method-3:-Enumerate-and-watch)<br />В этом разделе приведены сведения о более мощной и гибкой модели перечисления, которая позволяет перечислять имеющиеся в настоящий момент устройства, а также получать уведомления при добавлении устройств в систему или их удалении из нее.  Это полезно в том случае, если вы хотите сохранить текущий список устройств в фоновом режиме для его отображения в пользовательском интерфейсе, не дожидаясь создания снимка.
 

---
## <a name="define-a-device-selector"></a>Определение средства выбора устройств
Средство выбора устройств позволяет ограничить количество устройств, по которым выполняется поиск при перечислении.  Это позволяет получать только актуальные результаты и сократить время, необходимое для перечисления нужных устройств.  

Метод [**GetDeviceSelector**](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.posprinter.getdeviceselector#Windows_Devices_PointOfService_PosPrinter_GetDeviceSelector) предоставляет средство выбора для перечисления всех устройств POSPrinters, подключенных к системе, включая USB-устройства, сетевые устройства и Bluetooth-принтеры POSPrinters.

```Csharp
using Windows.Devices.PointOfService;

string selector = POSPrinter.GetDeviceSelector();   

```

С помощью метода [**GetDeviceSelector**](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.posprinter.getdeviceselector#Windows_Devices_PointOfService_PosPrinter_GetDeviceSelector_Windows_Devices_PointOfService_PosConnectionTypes_), который принимает [**PosConnectionTypes**](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.posconnectiontypes) в качестве параметра, можно ограничить диапазон средства выбора перечислением локальных, сетевых или Bluetooth-устройств POSPrinters, подключенных к системе, при этом сократив время, необходимое для завершения запроса.  В примере ниже демонстрируется использование этого метода для определения средства выбора, поддерживающего только локально подключенные устройства POSPrinters.

 ```Csharp
using Windows.Devices.PointOfService;

string selector = POSPrinter.GetDeviceSelector(PosConnectionTypes.Local);   

```
> [!TIP]
> Инструкции по созданию более сложных строк средства выбора см. в разделе [**Создание средства выбора устройств**](https://docs.microsoft.com/windows/uwp/devices-sensors/build-a-device-selector).

---

## <a name="method-1-get-first-available-device"></a>Способ 1. Получение первого доступного устройства

Для получения устройства PointOfService проще всего использовать метод **GetDefaultAsync**, чтобы получить первое доступное устройство в рамках класса устройств PointOfService. 

Следующий пример иллюстрирует использование метода [**GetDefaultAsync**](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodescanner.getdefaultasync#Windows_Devices_PointOfService_BarcodeScanner_GetDefaultAsync) для BarcodeScanner (сканер штрихкодов). Шаблон кодирования является одинаковым для всех классов устройств PointOfService.

```Csharp

using Windows.Devices.PointOfService;

BarcodeScanner barcodeScanner = await BarcodeScanner.GetDefaultAsync();

```

> [!CAUTION]
> Метод GetDefaultAsync необходимо использовать с осторожностью, так как он может возвращать разные устройства при переходе от сеанса к сеансу. Многие события могут влиять на это перечисление, приводя к тому, что первое доступное устройство будет разным, включая: 
> - Изменение камер, подключенных к компьютеру 
> - Изменение устройств PointOfService, подключенных к компьютеру
> - Изменение подключенных сетевых устройств PointOfService, доступных в сети
> - Изменение Bluetooth-устройств PointOfService в пределах диапазона обнаружения компьютера 
> - Изменения конфигурации PointOfService 
> - Установка драйверов или служебных объектов OPOS
> - Установка расширений PointOfService
> - Обновление операционной системы Windows

---

## <a name="method-2-snapshot-of-devices"></a>Способ 2. Снимок устройств

В некоторых сценариях вам может потребоваться создать собственный пользовательский интерфейс или перечислить устройства без отображения интерфейса для пользователя.  В таких случаях можно выполнить перечисление снимка устройств, которые в настоящее время подключены к системе или связаны с ней с помощью метода [**DeviceInformation.FindAllAsync**](https://docs.microsoft.com/uwp/api/windows.devices.enumeration.deviceinformation.findallasync).  Этот метод будет удерживать все результаты до полного завершения перечисления.

> [!TIP]
> При использовании метода FindAllAsync рекомендуется использовать метод GetDeviceSelector с параметром PosConnectionTypes, чтобы ограничить запрос требуемым типом подключения.  Сетевые и Bluetooth-подключения могут задерживать получение результатов, так как их перечисления должны завершиться до возврата результатов FindAllAsync.

>[!CAUTION] 
>FindAllAsync возвращает массив устройств.  Порядок этого массива может изменяться в разных сеансах, поэтому не рекомендуется полагаться на определенный порядок путем использования жестко закодированного индекса в массиве.  Свойства DeviceInformation можно использовать для фильтрации результатов или предоставления пользовательского интерфейса пользователю.

В этом примере используется средство выбора, описанное выше, для создания моментального снимка устройств с помощью метода FindAllAsync, который перечисляет каждый элемент, возвращенный коллекцией, и записывает имя устройства и его идентификатор в выходные данные отладки. 

```Csharp
using Windows.Devices.Enumeration;

DeviceInformationCollection deviceCollection = await DeviceInformation.FindAllAsync(selector);

foreach (DeviceInformation devInfo in deviceCollection)
{
    Debug.WriteLine("{0} {1}", devInfo.Name, devInfo.Id);
}
```

> [!TIP] 
> При работе с API-интерфейсами [**Windows.Devices.Enumeration**](https://docs.microsoft.com/uwp/api/Windows.Devices.Enumeration) будет необходимо часто использовать объекты [**DeviceInformation**](https://docs.microsoft.com/uwp/api/windows.devices.enumeration.deviceinformation) для получения сведений о конкретном устройстве. Например, свойство [**DeviceInformation.ID**](https://docs.microsoft.com/uwp/api/windows.devices.enumeration.deviceinformation.id) можно использовать для восстановления и повторного использования одного и того же устройства, если оно будет доступно в будущих сеансах, а свойство [**DeviceInformation.Name**](https://docs.microsoft.com/uwp/api/windows.devices.enumeration.deviceinformation.name) — для отображения сведений об устройстве в вашем приложении.  Сведения о дополнительных доступных свойствах см. на странице справки [**DeviceInformation**](https://docs.microsoft.com/uwp/api/windows.devices.enumeration.deviceinformation).

---

## <a name="method-3-enumerate-and-watch"></a>Способ 3. Перечисление и отслеживание

Более мощным и гибким способом перечисления устройств является создание объекта [**DeviceWatcher**](https://docs.microsoft.com/uwp/api/Windows.Devices.Enumeration.DeviceWatcher).  Наблюдатель устройств динамически перечисляет устройства, чтобы приложение получало уведомления при добавлении, удалении или изменении устройств после завершения первоначального перечисления.  Объект DeviceWatcher позволяет определять, когда подключенное к сети устройство выходит в сеть, Bluetooth-устройство находится в радиусе обнаружения, а также, когда происходит отсоединение локально подключенного устройства. Это позволяет предпринять соответствующие действия в приложении.

В этом примере используется средство выбора, описанное выше, для создания объекта DeviceWatcher, а также определяются обработчики событий для уведомлений "Добавлено", "Удалено" и "Обновлено". Чтобы применить действие к любому уведомлению, необходимо указать сведения об этом действии.

```Csharp

using Windows.Devices.Enumeration;

DeviceWatcher deviceWatcher = DeviceInformation.CreateWatcher(selector);
deviceWatcher.Added += DeviceWatcher_Added;
deviceWatcher.Removed += DeviceWatcher_Removed;
deviceWatcher.Updated += DeviceWatcher_Updated;

void DeviceWatcher_Added(DeviceWatcher sender, DeviceInformation args)
{
    // TODO: Add the DeviceInformation object to your collection
}

void DeviceWatcher_Removed(DeviceWatcher sender, DeviceInformationUpdate args)
{
    // TODO: Remove the item in your collection associated with DeviceInformationUpdate
}

void DeviceWatcher_Updated(DeviceWatcher sender, DeviceInformationUpdate args)
{
    // TODO: Update your collection with information from DeviceInformationUpdate
}
```

> [!TIP]
> Дополнительные сведения об использовании DeviceWatcher см. в разделе [**Перечисление и отслеживание устройств**]( https://docs.microsoft.com/windows/uwp/devices-sensors/enumerate-devices#enumerate-and-watch-devices).
