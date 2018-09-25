---
author: TerryWarwick
title: Перечисление устройств PointOfService
description: Узнайте, как перечислять устройства PointOfService
ms.author: jken
ms.date: 08/30/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: Windows 10, UWP, точка обслуживания, POS
ms.localizationpriority: medium
ms.openlocfilehash: 4e42ebb2eba7b6465be271e6095100c03798826f
ms.sourcegitcommit: 232543fba1fb30bb1489b053310ed6bd4b8f15d5
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/25/2018
ms.locfileid: "4174671"
---
# <a name="enumerating-point-of-service-devices"></a>Перечисление POS-устройств
В этом разделе вы узнайте, как [определить средство выбора устройств](https://docs.microsoft.com/windows/uwp/devices-sensors/build-a-device-selector), использующееся для запроса устройств, доступных системе, а также, как использовать это средство выбора для перечисления POS-устройств с помощью одного из следующих способов:

**Метод 1:** [Использование средства выбора устройства](#method-1:-use-a-device-picker)
<br/>
Отображение средства выбора устройства пользовательского интерфейса и иметь пользователю выбрать на подключенном устройстве. Этот метод обрабатывает обновление списка, когда устройства подключенные и удаляются и проще и надежнее, чем другие методы.

**Метод 2:** [Получение первого доступного устройства](#Method-1:-get-first-available-device)<br />Использование [GetDefaultAsync](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodescanner.getdefaultasync) для доступа к первое доступное устройство в класс определенных POS-устройств.

**Способ 3:** [Снимок устройств](#Method-2:-Snapshot-of-devices)<br />Перечисление снимка POS-устройств, которые присутствуют в системе в определенный момент времени. Это удобно при создании собственного пользовательского интерфейса или перечислении устройств без отображения интерфейса для пользователя. [FindAllAsync](https://docs.microsoft.com/uwp/api/windows.devices.enumeration.deviceinformation.findallasync) будет удерживать результаты до полного завершения перечисления.

**Метод 4:** [Перечисление и отслеживание](#Method-3:-Enumerate-and-watch)<br />[DeviceWatcher](https://docs.microsoft.com/uwp/api/Windows.Devices.Enumeration.DeviceWatcher) является более мощной и гибкой модели перечисления, которая позволяет перечислять устройства, которые в настоящее время имеются и также получать уведомления при добавлении устройств или удален из системы.  Это полезно в том случае, если вы хотите сохранить текущий список устройств в фоновом режиме для его отображения в пользовательском интерфейсе, не дожидаясь создания снимка.

## <a name="define-a-device-selector"></a>Определение средства выбора устройств
Средство выбора устройств позволяет ограничить количество устройств, по которым выполняется поиск при перечислении.  Это позволит получать только актуальные результаты и сократить время, необходимое для перечисления нужных устройств.

Можно использовать метод **GetDeviceSelector** для типа устройства, который вы ищете для получения средство выбора устройств для этого типа. Например с помощью [PosPrinter.GetDeviceSelector](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.posprinter.getdeviceselector#Windows_Devices_PointOfService_PosPrinter_GetDeviceSelector) будет предоставлять вам со средством выбора для перечисления всех [PosPrinters](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.posprinter) подключенное к системе, включая USB, сети и принтеры Bluetooth POS.

```Csharp
using Windows.Devices.PointOfService;

string selector = POSPrinter.GetDeviceSelector();
```

Доступны следующие методы **GetDeviceSelector** для различных типов устройств:

* [BarcodeScanner.GetDeviceSelector](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodescanner.getdeviceselector)
* [CashDrawer.GetDeviceSelector](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.cashdrawer.getdeviceselector)
* [LineDisplay.GetDeviceSelector](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.linedisplay.getdeviceselector)
* [MagneticStripeReader.GetDeviceSelector](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.magneticstripereader.getdeviceselector)
* [PosPrinter.GetDeviceSelector](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.posprinter.getdeviceselector)

Используя метод **GetDeviceSelector** , который принимает значение [PosConnectionTypes](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.posconnectiontypes) как параметр, вы можете ограничить вашего выбора перечислением локальных, сетевых или Bluetooth присоединенные POS-устройствам, сокращает время, необходимое для завершения запроса.  В примере ниже демонстрируется использование этого метода для определения выбора, который поддерживает только локально подключенные принтеры POS.

 ```Csharp
using Windows.Devices.PointOfService;

string selector = POSPrinter.GetDeviceSelector(PosConnectionTypes.Local);
```

> [!TIP]
> Инструкции по созданию более сложных строк средства выбора см. в разделе [Создание средства выбора устройств](https://docs.microsoft.com/windows/uwp/devices-sensors/build-a-device-selector).

## <a name="method-1-use-a-device-picker"></a>Метод 1: Используйте элемент выбора устройств

> [!NOTE]
> Этот метод требует последней [Предварительной оценки Windows SDK](https://www.microsoft.com/software-download/windowsinsiderpreviewSDK).

Класс [DevicePicker](https://docs.microsoft.com/uwp/api/windows.devices.enumeration.devicepicker) позволяет отображать всплывающий элемент выбора, содержащий список устройств для пользователя на выбор. Чтобы выбрать типы устройств, чтобы показать в средстве выбора можно использовать свойство [фильтра](https://docs.microsoft.com/uwp/api/windows.devices.enumeration.devicepicker.filter) . Это свойство имеет тип [DevicePickerFilter](https://docs.microsoft.com/uwp/api/windows.devices.enumeration.devicepickerfilter). Типы устройств можно добавить в фильтр, используя свойство [SupportedDeviceClasses](https://docs.microsoft.com/uwp/api/windows.devices.enumeration.devicepickerfilter.supporteddeviceclasses) или [SupportedDeviceSelectors](https://docs.microsoft.com/uwp/api/windows.devices.enumeration.devicepickerfilter.supporteddeviceselectors) .

Когда будете готовы для отображения средства выбора устройств, можно вызвать метод [PickSingleDeviceAsync](https://docs.microsoft.com/uwp/api/windows.devices.enumeration.devicepicker.picksingledeviceasync) , который будет отображать Интерфейс средства выбора и возврата выбранного устройства. Вам потребуется указать [Rect](https://docs.microsoft.com/uwp/api/windows.foundation.rect) , которые определяют, где отображается всплывающий элемент. Этот метод возвращает объект [DeviceInformation](https://docs.microsoft.com/uwp/api/windows.devices.enumeration.deviceinformation) , таким образом, чтобы использовать его с точкой из службы API-интерфейсы, вам потребуется использовать метод **FromIdAsync** для конкретного устройства класса, который вы хотите. Передайте свойство [DeviceInformation.Id](https://docs.microsoft.com/uwp/api/windows.devices.enumeration.deviceinformation.id) в качестве параметра метода *deviceId* и получите экземпляр класса устройств как возвращаемое значение.

В следующем фрагменте кода создается **DevicePicker**, добавляет фильтр сканера штрихкодов, имеет пользователю выбрать устройства, а затем создает объект **BarcodeScanner** , основанный на идентификатор устройства:

```cs
private async Task<BarcodeScanner> GetBarcodeScanner()
{
    DevicePicker devicePicker = new DevicePicker();
    devicePicker.Filter.SupportedDeviceSelectors.Add(BarcodeScanner.GetDeviceSelector());
    Rect rect = new Rect();
    DeviceInformation deviceInformation = await devicePicker.PickSingleDeviceAsync(rect);
    BarcodeScanner barcodeScanner = await BarcodeScanner.FromIdAsync(deviceInformation.Id);
    return barcodeScanner;
}
```

## <a name="method-2-get-first-available-device"></a>Метод 2: Получение первого доступного устройства

Самый простой способ для получения POS-устройства — это использование **GetDefaultAsync** для получение первого доступного устройства в пределах класса устройств Point of Service. 

Следующий пример иллюстрирует использование [GetDefaultAsync](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodescanner.getdefaultasync#Windows_Devices_PointOfService_BarcodeScanner_GetDefaultAsync) для [BarcodeScanner](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodescanner). Шаблон кодирования является одинаковым для всех классов POS-устройств.

```Csharp
using Windows.Devices.PointOfService;

BarcodeScanner barcodeScanner = await BarcodeScanner.GetDefaultAsync();
```

> [!CAUTION]
> **GetDefaultAsync** необходимо использовать с осторожностью, так как он может возвращать разные устройства от сеанса к следующему. Многие события могут влиять на это перечисление, приводя к тому, что первое доступное устройство будет разным, включая: 
> - Изменение камер, подключенных к компьютеру 
> - Измените точке начала обновления устройств, подключенных к компьютеру
> - Изменение подключенных сетевых устройств Point of Service, доступных в сети
> - Изменение Bluetooth POS-устройств в радиусе действия компьютера 
> - Изменения конфигурации Point of Service 
> - Установка драйверов или служебных объектов OPOS
> - Установка расширений Point of Service
> - Обновление операционной системы Windows

## <a name="method-3-snapshot-of-devices"></a>Способ 3: Снимок устройств

В некоторых сценариях вам может потребоваться создать собственный пользовательский интерфейс или перечислить устройства без отображения интерфейса для пользователя.  В таких случаях можно выполнить перечисление снимка устройств, которые в настоящее время подключены к системе или связаны с ней с помощью метода [DeviceInformation.FindAllAsync](https://docs.microsoft.com/uwp/api/windows.devices.enumeration.deviceinformation.findallasync).  Этот метод будет удерживать все результаты до полного завершения перечисления.

> [!TIP]
> Рекомендуется использовать метод **GetDeviceSelector** с параметром **posconnectiontypes** , при использовании **метода FindAllAsync** , чтобы ограничить запрос требуемым типом подключения.  Сетевые и Bluetooth-подключения могут задерживать результатов, так как их перечисления должны завершиться до возврата результатов **FindAllAsync** .

> [!CAUTION] 
> **FindAllAsync** возвращает массив устройств.  Порядок этого массива может изменяться в разных сеансах, поэтому не рекомендуется полагаться на определенный порядок путем использования жестко закодированного индекса в массиве.  Свойства [DeviceInformation](https://docs.microsoft.com/uwp/api/windows.devices.enumeration.deviceinformation) можно используйте для фильтрации результатов или предоставления пользовательского интерфейса для пользователя на выбор.

В этом примере используется средство выбора, описанное выше, для создания моментального снимка устройств, с помощью **метода FindAllAsync** , а затем перечисляет каждый элемент, возвращенный коллекцией и записывает имя устройства и его идентификатор в отладочный вывод. 

```Csharp
using Windows.Devices.Enumeration;

DeviceInformationCollection deviceCollection = await DeviceInformation.FindAllAsync(selector);

foreach (DeviceInformation devInfo in deviceCollection)
{
    Debug.WriteLine("{0} {1}", devInfo.Name, devInfo.Id);
}
```

> [!TIP] 
> При работе с API-интерфейсами [Windows.Devices.Enumeration](https://docs.microsoft.com/uwp/api/Windows.Devices.Enumeration) будет необходимо часто использовать объекты [DeviceInformation](https://docs.microsoft.com/uwp/api/windows.devices.enumeration.deviceinformation) для получения сведений о конкретном устройстве. Например свойство [DeviceInformation.ID](https://docs.microsoft.com/uwp/api/windows.devices.enumeration.deviceinformation.id) может использоваться для восстановления и повторного использования одного устройства, если он доступен в будущих сеансах, а свойство [DeviceInformation.Name](https://docs.microsoft.com/uwp/api/windows.devices.enumeration.deviceinformation.name) можно использовать для отображения в приложении.  Сведения о дополнительных доступных свойствах см. на странице справки [DeviceInformation](https://docs.microsoft.com/uwp/api/windows.devices.enumeration.deviceinformation).

## <a name="method-4-enumerate-and-watch"></a>Способ 4: Перечисление и отслеживание

Более мощным и гибким способом перечисления устройств является создание объекта [DeviceWatcher](https://docs.microsoft.com/uwp/api/Windows.Devices.Enumeration.DeviceWatcher).  Наблюдатель устройств динамически перечисляет устройства, чтобы приложение получало уведомления при добавлении, удалении или изменении устройств после завершения первоначального перечисления.  Объект **DeviceWatcher** позволяет определять, когда устройства, подключенного к сети, Bluetooth-устройство находится в диапазоне, а также, как если происходит отсоединение локально подключенного устройства, таким образом, чтобы можно было предпринять соответствующие действия в приложении.

В этом примере используется средство выбора, описанное выше, для создания объекта **DeviceWatcher** , а также определяются обработчики событий для уведомления [Added](https://docs.microsoft.com/uwp/api/windows.devices.enumeration.devicewatcher.added)"," [удалено](https://docs.microsoft.com/uwp/api/windows.devices.enumeration.devicewatcher.removed)"и" [обновлено](https://docs.microsoft.com/uwp/api/windows.devices.enumeration.devicewatcher.updated) . Чтобы применить действие к любому уведомлению, необходимо указать сведения об этом действии.

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
> Дополнительные сведения об использовании **DeviceWatcher**см. [перечисление и отслеживание устройств]( https://docs.microsoft.com/windows/uwp/devices-sensors/enumerate-devices#enumerate-and-watch-devices) .

## <a name="see-also"></a>См. также
* [Начало работы с POS-устройствами](pos-basics.md)
* [Класс DeviceInformation](https://docs.microsoft.com/uwp/api/windows.devices.enumeration.deviceinformation)
* [Класс PosPrinter](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.posprinter)
* [Перечисление PosConnectionTypes](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.posconnectiontypes)
* [Класс BarcodeScanner](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodescanner)
* [Класс DeviceWatcher](https://docs.microsoft.com/uwp/api/Windows.Devices.Enumeration.DeviceWatcher)

[!INCLUDE [feedback](./includes/pos-feedback.md)]