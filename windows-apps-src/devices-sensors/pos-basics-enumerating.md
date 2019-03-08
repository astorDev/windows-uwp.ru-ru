---
title: Перечисление устройств PointOfService
description: Узнайте, как перечислять устройства PointOfService
ms.date: 10/08/2018
ms.topic: article
keywords: Windows 10, UWP, точка обслуживания, POS
ms.localizationpriority: medium
ms.openlocfilehash: 27d25864941b9d73c9b12e6329eab79fac1b15bf
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57620909"
---
# <a name="enumerating-point-of-service-devices"></a>Перечисление POS-устройств
В этом разделе вы узнаете, как [определить селектора устройства](https://docs.microsoft.com/windows/uwp/devices-sensors/build-a-device-selector) , используемый для запроса устройств, доступных в системе и использовать этот селектор для перечисления устройств Point of Service с помощью одного из следующих методов:

**Метод 1.** [Используйте средство выбора устройства](#method-1-use-a-device-picker)
<br/>
Отображать пользовательский Интерфейс для выбора устройства и выполнения пользователем команды на подключенное устройство. Этот метод обрабатывает обновление списка, при подключении и удаления устройств и проще и безопаснее, чем другие методы.

**Метод 2.** [Получить первому доступному устройству](#method-2-get-first-available-device)<br />Используйте [GetDefaultAsync](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodescanner.getdefaultasync) для доступа к первому доступному устройству в определенном классе устройства Point of Service.

**Метод 3:** [Моментальный снимок устройства](#method-3-snapshot-of-devices)<br />Перечислить моментальный снимок Point of Service устройств, которые присутствуют в системе в определенный момент времени. Это удобно при создании собственного пользовательского интерфейса или перечислении устройств без отображения интерфейса для пользователя. [FindAllAsync](https://docs.microsoft.com/uwp/api/windows.devices.enumeration.deviceinformation.findallasync) будет придерживать результаты до завершения перечисления.

**Способ 4:** [Перечисление и просмотр](#method-4-enumerate-and-watch)<br />[DeviceWatcher](https://docs.microsoft.com/uwp/api/Windows.Devices.Enumeration.DeviceWatcher) более мощную и гибкую модель перечисления, которая позволяет функции перечисления устройств, которые в настоящее время есть, а также получать уведомления, когда устройства добавляются или удаляются из системы.  Это полезно в том случае, если вы хотите сохранить текущий список устройств в фоновом режиме для его отображения в пользовательском интерфейсе, не дожидаясь создания снимка.

## <a name="define-a-device-selector"></a>Определение средства выбора устройств
Средство выбора устройств позволяет ограничить количество устройств, по которым выполняется поиск при перечислении.  Это позволит вам только получить нужные результаты и сократить время, необходимое для перечисления нужного устройства.

Можно использовать **GetDeviceSelector** метода для типа устройства, который вы ищете для получения селекторе устройства для этого типа. Например, с помощью [PosPrinter.GetDeviceSelector](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.posprinter.getdeviceselector#Windows_Devices_PointOfService_PosPrinter_GetDeviceSelector) будет предоставлен селектор для перечисления всех [PosPrinters](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.posprinter) подключен к системе, включая USB, сети и Bluetooth POS принтеры.

```Csharp
using Windows.Devices.PointOfService;

string selector = POSPrinter.GetDeviceSelector();
```

**GetDeviceSelector** методы для разных типов устройств:

* [BarcodeScanner.GetDeviceSelector](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodescanner.getdeviceselector)
* [CashDrawer.GetDeviceSelector](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.cashdrawer.getdeviceselector)
* [LineDisplay.GetDeviceSelector](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.linedisplay.getdeviceselector)
* [MagneticStripeReader.GetDeviceSelector](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.magneticstripereader.getdeviceselector)
* [PosPrinter.GetDeviceSelector](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.posprinter.getdeviceselector)

С помощью **GetDeviceSelector** метода, принимающего [PosConnectionTypes](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.posconnectiontypes) значение как параметр, можно ограничить выделения, чтобы перечислить локального, сети или устройств Bluetooth подключаемое POS, уменьшая время, необходимое для завершения запроса.  В примере ниже показано, что использование этого метода для определения селектора, которая поддерживает только локально подключенные POS принтеров.

 ```Csharp
using Windows.Devices.PointOfService;

string selector = POSPrinter.GetDeviceSelector(PosConnectionTypes.Local);
```

> [!TIP]
> См. в разделе [построения селектора устройства](https://docs.microsoft.com/windows/uwp/devices-sensors/build-a-device-selector) для создания более advanced селектор строк.

## <a name="method-1-use-a-device-picker"></a>Метод 1: Используйте средство выбора устройства

[DevicePicker](https://docs.microsoft.com/uwp/api/windows.devices.enumeration.devicepicker) класс позволяет отображать со списком устройств для пользователя выбрать из всплывающего меню выбора. Можно использовать [фильтра](https://docs.microsoft.com/uwp/api/windows.devices.enumeration.devicepicker.filter) свойство для выбора типов устройств, для отображения в средстве выбора. Это свойство имеет тип [DevicePickerFilter](https://docs.microsoft.com/uwp/api/windows.devices.enumeration.devicepickerfilter). Типы устройств можно добавить в фильтр с помощью [SupportedDeviceClasses](https://docs.microsoft.com/uwp/api/windows.devices.enumeration.devicepickerfilter.supporteddeviceclasses) или [SupportedDeviceSelectors](https://docs.microsoft.com/uwp/api/windows.devices.enumeration.devicepickerfilter.supporteddeviceselectors) свойство.

Когда будете готовы для отображения в средстве выбора устройств, можно вызвать [PickSingleDeviceAsync](https://docs.microsoft.com/uwp/api/windows.devices.enumeration.devicepicker.picksingledeviceasync) метод, который будет отображать пользовательский Интерфейс для выбора и возврата выбранного устройства. Вам потребуется указать [Rect](https://docs.microsoft.com/uwp/api/windows.foundation.rect) он определяет место отображения всплывающего меню. Этот метод будет возвращать [DeviceInformation](https://docs.microsoft.com/uwp/api/windows.devices.enumeration.deviceinformation) объекта, поэтому использовать его с точки для службы API-интерфейсы, потребуется использовать **FromIdAsync** метод для класса определенного устройства, который требуется. Вы передаете [DeviceInformation.Id](https://docs.microsoft.com/uwp/api/windows.devices.enumeration.deviceinformation.id) свойства в виде метода *deviceId* параметра и получите экземпляр класса устройства как возвращаемое значение.

В следующем фрагменте кода создается **DevicePicker**добавляет к нему фильтр сканера штрих-кода, активировал ли пользователь выбрать устройство, а затем создает **BarcodeScanner** объекта на основе идентификатора устройства:

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

## <a name="method-2-get-first-available-device"></a>Метод 2. Получить первому доступному устройству

Самый простой способ получить устройство Point of Service является использование **GetDefaultAsync** для получения первому доступному устройству в пределах класса Point of Service устройства. 

Следующий пример иллюстрирует использование [GetDefaultAsync](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodescanner.getdefaultasync#Windows_Devices_PointOfService_BarcodeScanner_GetDefaultAsync) для [BarcodeScanner](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodescanner). Шаблон кодирования одинакова для всех классов устройств Point of Service.

```Csharp
using Windows.Devices.PointOfService;

BarcodeScanner barcodeScanner = await BarcodeScanner.GetDefaultAsync();
```

> [!CAUTION]
> **GetDefaultAsync** должно использоваться с осторожностью, как он может возвращать другое устройство из одного сеанса к следующему. Многие события могут влиять на это перечисление, приводя к тому, что первое доступное устройство будет разным, включая: 
> - Изменение камер, подключенных к компьютеру 
> - Изменить точке начала служба устройства, подключенные к компьютеру
> - Изменения в подключенные к сети устройств Point of Service, доступных в сети
> - Изменение устройства Bluetooth Point of Service в пределах диапазона компьютера 
> - Изменения в конфигурацию Point of Service 
> - Установка драйверов или служебных объектов OPOS
> - Установка расширений Point of Service
> - Обновление операционной системы Windows

## <a name="method-3-snapshot-of-devices"></a>Метод 3: Моментальный снимок устройства

В некоторых сценариях вам может потребоваться создать собственный пользовательский интерфейс или перечислить устройства без отображения интерфейса для пользователя.  В таких ситуациях можно перечислить моментальный снимок устройства, которые в настоящее время подключены или связан с системой с помощью [DeviceInformation.FindAllAsync](https://docs.microsoft.com/uwp/api/windows.devices.enumeration.deviceinformation.findallasync).  Этот метод будет удерживать все результаты до полного завершения перечисления.

> [!TIP]
> Рекомендуется использовать **GetDeviceSelector** метод с **PosConnectionTypes** при использовании командлета **FindAllAsync** для ограничения запроса для типа подключения требуемого.  Подключения к сети и Bluetooth могут вызвать задержку результаты, как их перечисления необходимо выполнить перед **FindAllAsync** результаты.

> [!CAUTION] 
> **FindAllAsync** возвращает массив устройств.  Порядок этого массива может изменяться в разных сеансах, поэтому не рекомендуется полагаться на определенный порядок путем использования жестко закодированного индекса в массиве.  Используйте [DeviceInformation](https://docs.microsoft.com/uwp/api/windows.devices.enumeration.deviceinformation) свойства для фильтрации результатов или предоставление пользовательского интерфейса для пользователя на выбор.

В этом примере используется селектор, определенный выше устройств с помощью моментального снимка **FindAllAsync** затем выполняет перечисление всех элементов, возвращенных в коллекции и записывает имя устройства и идентификатор сообщения в вывод отладки. 

```Csharp
using Windows.Devices.Enumeration;

DeviceInformationCollection deviceCollection = await DeviceInformation.FindAllAsync(selector);

foreach (DeviceInformation devInfo in deviceCollection)
{
    Debug.WriteLine("{0} {1}", devInfo.Name, devInfo.Id);
}
```

> [!TIP] 
> При работе с [Windows.Devices.Enumeration](https://docs.microsoft.com/uwp/api/Windows.Devices.Enumeration) API-интерфейсы, часто нужно будет использовать [DeviceInformation](https://docs.microsoft.com/uwp/api/windows.devices.enumeration.deviceinformation) объектов для получения сведений о конкретном устройстве. Например [DeviceInformation.ID](https://docs.microsoft.com/uwp/api/windows.devices.enumeration.deviceinformation.id) свойство может использоваться для восстановления и повторно использовать то же устройство, если она доступна в будущих сеансов и [DeviceInformation.Name](https://docs.microsoft.com/uwp/api/windows.devices.enumeration.deviceinformation.name) свойство может использоваться для отображения в целях в приложении.  См. в разделе [DeviceInformation](https://docs.microsoft.com/uwp/api/windows.devices.enumeration.deviceinformation) справочника, посвященных сведения о доступных дополнительных свойств.

## <a name="method-4-enumerate-and-watch"></a>Метод 4. Перечисление и просмотр

Создание более мощный и гибкий метод перечисления устройств [DeviceWatcher](https://docs.microsoft.com/uwp/api/Windows.Devices.Enumeration.DeviceWatcher).  Наблюдатель устройств динамически перечисляет устройства, чтобы приложение получало уведомления при добавлении, удалении или изменении устройств после завершения первоначального перечисления.  Объект **DeviceWatcher** позволит обнаружить, когда подключенный к сети устройство подключается к сети, устройства Bluetooth находится в диапазоне, а также, как если локально подключенное устройство отключено, чтобы предпринять соответствующие действия в вашей приложение.

В этом примере используется средство выбора, определенные выше, чтобы создать **DeviceWatcher** также как и в случае определяет обработчики событий для [Added](https://docs.microsoft.com/uwp/api/windows.devices.enumeration.devicewatcher.added), [удалено](https://docs.microsoft.com/uwp/api/windows.devices.enumeration.devicewatcher.removed), и [обновлено ](https://docs.microsoft.com/uwp/api/windows.devices.enumeration.devicewatcher.updated) уведомления. Чтобы применить действие к любому уведомлению, необходимо указать сведения об этом действии.

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
> См. в разделе [Enumerate и наблюдать устройств]( https://docs.microsoft.com/windows/uwp/devices-sensors/enumerate-devices#enumerate-and-watch-devices) Дополнительные сведения об использовании **DeviceWatcher**.

## <a name="see-also"></a>См. также
* [Приступая к работе с Point of Service](pos-basics.md)
* [Класс DeviceInformation](https://docs.microsoft.com/uwp/api/windows.devices.enumeration.deviceinformation)
* [Класс PosPrinter](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.posprinter)
* [Перечисление PosConnectionTypes](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.posconnectiontypes)
* [Класс BarcodeScanner](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodescanner)
* [Класс DeviceWatcher](https://docs.microsoft.com/uwp/api/Windows.Devices.Enumeration.DeviceWatcher)

[!INCLUDE [feedback](./includes/pos-feedback.md)]