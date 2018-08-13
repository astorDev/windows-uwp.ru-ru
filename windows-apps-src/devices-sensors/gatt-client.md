---
author: msatranjr
title: Клиент GATT Bluetooth
description: В этой статье Обзор Bluetooth универсальный атрибут профиля (GATT) клиента для приложений, универсальные платформы Windows (UWP), а также пример кода для наиболее распространенных случаев использования.
ms.author: misatran
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 555fec6d534c07898acd911f9cd84a11ac66dcd8
ms.sourcegitcommit: 897a111e8fc5d38d483800288ad01c523e924ef4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/13/2018
ms.locfileid: "304997"
---
# <a name="bluetooth-gatt-client"></a>Клиент GATT Bluetooth


**Важные API**

-   [**Windows.Devices.Bluetooth**](https://msdn.microsoft.com/library/windows/apps/Dn263413)
-   [**Windows.Devices.Bluetooth.GenericAttributeProfile**](https://msdn.microsoft.com/library/windows/apps/Dn297685)

В этой статье демонстрируется использование клиентских интерфейсов API Bluetooth универсальный атрибут (GATT) для приложений, универсальные платформы Windows (UWP), а также примеры кода для распространенных задач клиентского GATT:
- Запрос для рядом устройств
- Подключение к устройству
- Перечисление поддерживаемых служб и характеристики устройства
- Чтение и запись характеристик
- Подписка для уведомлений, когда характеристик значений

## <a name="overview"></a>Обзор
Разработчики могут использовать интерфейсы API в пространстве имен [**Windows.Devices.Bluetooth.GenericAttributeProfile**](https://msdn.microsoft.com/library/windows/apps/Dn297685) для доступа к устройствам Bluetooth LE. Функции устройств Bluetooth с низким энергопотреблением доступны через набор:

-   Службы
-   характеристик;
-   дескрипторов.

Службы определение функциональных контракта LE устройства и содержит коллекцию характеристики, которые определяют службы. Эти характеристики, в свою очередь, содержат дескрипторы, которые описывают характеристики. Эти термины 3, известные как атрибуты устройства.

API-интерфейсы GATT LE Bluetooth предоставляют доступ к объектам и функции, а не доступ к необработанные транспорта. API-интерфейсы GATT также разработчики могут работать с устройствами Bluetooth LE возможность выполнять следующие задачи:

-   Выполнить обнаружение атрибута
-   Чтение и запись значения атрибутов
-   Регистрация обратного вызова для события ValueChanged характеристика

Для создания полезных реализации разработчик должен иметь знания служб GATT и характеристики, которые должен использовать приложения и к процессу определенных характеристик со значениями таким образом, что двоичные данные, предоставляемые интерфейсом API преобразуется в полезные данные перед используется в презентации для пользователя. API Bluetooth GATT предоставляют только основные примитивы, необходимые для взаимодействия с устройством Bluetooth с низким энергопотреблением. Для интерпретации данных необходимо определить профиль приложения. Для этого используется либо стандартный профиль Bluetooth SIG, либо настраиваемый профиль, реализованный поставщиком устройства. Профиль создает контракт привязки между приложением и устройством в отношении содержимого передаваемых данных и их интерпретации.

Для удобства Bluetooth SIG ведет [список доступных общих профилей](https://www.bluetooth.com/specifications/adopted-specifications#gattspec).

## <a name="query-for-nearby-devices"></a>Запрос для рядом устройств
Существует два основных методов для отправки запросов о рядом устройств.
- DeviceWatcher в Windows.Devices.Enumeration
- AdvertisementWatcher в Windows.Devices.Bluetooth.Advertisement

Метод 2-й описывается объяснял в документации по [объявления](ble-beacon.md) , оно не будут рассмотрены много здесь, но концепция — это найти адрес Bluetooth рядом устройств, которые удовлетворяют отдельного [Фильтра объявления](https://msdn.microsoft.com/en-us/library/windows/apps/windows.devices.bluetooth.advertisement.bluetoothleadvertisementwatcher.advertisementfilter.aspx). Если адрес, можно вызвать [BluetoothLEDevice.FromBluetoothAddressAsync](https://msdn.microsoft.com/en-us/library/windows/apps/mt608819.aspx) для получения ссылки на устройство. 

Теперь методу DeviceWatcher. Устройство Bluetooth LE так же, как другие устройства в Windows и можно выполнять запросы с помощью [Интерфейсов API перечисления](https://msdn.microsoft.com/library/windows/apps/BR225459). Используйте класс [DeviceWatcher](https://msdn.microsoft.com/en-us/library/windows/apps/windows.devices.enumeration.devicewatcher) и передайте строку запроса, указав устройств для поиска: 

```csharp
// Query for extra properties you want returned
string[] requestedProperties = { "System.Devices.Aep.DeviceAddress", "System.Devices.Aep.IsConnected" };

DeviceWatcher deviceWatcher =
            DeviceInformation.CreateWatcher(
                    BluetoothLEDevice.GetDeviceSelectorFromPairingState(false),
                    requestedProperties,
                    DeviceInformationKind.AssociationEndpoint);

// Register event handlers before starting the watcher.
// Added, Updated and Removed are required to get all nearby devices
deviceWatcher.Added += DeviceWatcher_Added;
deviceWatcher.Updated += DeviceWatcher_Updated;
deviceWatcher.Removed += DeviceWatcher_Removed;

// EnumerationCompleted and Stopped are optional to implement.
deviceWatcher.EnumerationCompleted += DeviceWatcher_EnumerationCompleted;
deviceWatcher.Stopped += DeviceWatcher_Stopped;

// Start the watcher.
deviceWatcher.Start();
```
После начала DeviceWatcher, вы получите [DeviceInformation](https://msdn.microsoft.com/library/windows/apps/br225393) для каждого устройства, которая должна удовлетворять запроса в обработчике для события [добавлены](https://msdn.microsoft.com/en-us/library/windows/apps/windows.devices.enumeration.devicewatcher.added) для устройства. Более подробное описание DeviceWatcher в разделе полный образец [на репозиториев](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/DeviceEnumerationAndPairing). 

## <a name="connecting-to-the-device"></a>Подключение к устройству
После обнаружения необходимые устройства используйте [DeviceInformation.Id](https://msdn.microsoft.com/en-us/library/windows/apps/windows.devices.enumeration.deviceinformation.id) для получения объекта Bluetooth LE устройств для устройства: 

```csharp
async void ConnectDevice(DeviceInformation deviceInfo)
{
    // Note: BluetoothLEDevice.FromIdAsync must be called from a UI thread because it may prompt for consent.
    BluetoothLEDevice bluetoothLeDevice = await BluetoothLEDevice.FromIdAsync(deviceInfo.Id);
    // ...
}
```
С другой стороны, удаление всех ссылок на BluetoothLEDevice объект для устройства (и если ни одно приложение в системе есть ссылка на устройство) вызовет автоматическое отключение после небольшой ожидания. 

```csharp
bluetoothLeDevice.Dispose();
```
Если приложение должно осуществлять доступ к устройства еще раз, просто повторное создание объекта устройства и доступ к характеристик (описывается в следующем разделе) будет активировано операционная система для повторного подключения при необходимости. Если устройство является рядом, вы получите доступ к устройству в противном случае будет возвращено с ошибкой DeviceUnreachable.  

## <a name="enumerating-supported-services-and-characteristics"></a>Перечисление поддерживаемых служб и характеристики
Теперь, когда у вас есть объект BluetoothLEDevice, следующим шагом является для обнаружения данных предоставляет устройства. Для этого сначала запросить службы: 

```csharp
GattDeviceServicesResult result = await bluetoothLeDevice.GetGattServicesAsync();
                
if (result.Status == GattCommunicationStatus.Success)
{
    var services = result.Services;
    // ...
}
```
После определения службы процентной следующим шагом является запрашивать характеристики. 

```csharp
GattCharacteristicsResult result = await service.GetCharacteristicsAsync();
                
if (result.Status == GattCommunicationStatus.Success)
{
    var characteristics = result.Characteristics;
    // ...
}
```  
Операционная система возвращает список только для чтения GattCharacteristic объектов, что можно выполнять операции на.

## <a name="perform-readwrite-operations-on-a-characteristic"></a>Выполнение операций чтения и записи в характеристик

Эти характеристики — связь, основанная на основную единицу GATT. Он содержит значение, которое представляет определенную часть данных на устройстве. К примеру эти характеристики уровня батарей имеет значение, представляющее уровень батарей устройства.

Чтение характеристик свойства, чтобы определить, какие операции поддерживаются:
```csharp
GattCharacteristicProperties properties = characteristic.CharacteristicProperties

if(properties.HasFlag(GattCharacteristicProperties.Read))
{
    // This characteristic supports reading from it.
}
if(properties.HasFlag(GattCharacteristicProperties.Write))
{
    // This characteristic supports writing to it.
}
if(properties.HasFlag(GattCharacteristicProperties.Notify))
{
    // This characteristic supports subscribing to notifications.
}
```

Если чтение поддерживается, могут читать значение: 
```csharp
GattReadResult result = await selectedCharacteristic.ReadValueAsync();
if (result.Status == GattCommunicationStatus.Success)
{
    var reader = DataReader.FromBuffer(result.Value);
    byte[] input = new byte[reader.UnconsumedBufferLength];
    reader.ReadBytes(input);
    // Utilize the data as needed
}
```
Запись характеристик соответствует шаблону, следующий: 
```csharp
var writer = new DataWriter();
// WriteByte used for simplicity. Other commmon functions - WriteInt16 and WriteSingle
writer.WriteByte(0x01);

GattReadResult result = await selectedCharacteristic.WriteValueAsync(writer.DetachBuffer());
if (result.Status == GattCommunicationStatus.Success)
{
    // Successfully wrote to device
}
```
> **Совет**: удобство использования с помощью [модуль чтения данных](https://msdn.microsoft.com/en-us/library/windows/apps/windows.storage.streams.datareader.aspx) и [DataWriter](https://msdn.microsoft.com/en-us/library/windows/apps/windows.storage.streams.datawriter.aspx). Их функциональные возможности будут необходимые при работе с необработанных буферов, которые вы получаете из множества API-интерфейсы Bluetooth. 
## <a name="subscribing-for-notifications"></a>Подписка на для уведомлений

Убедитесь, что эти характеристики поддерживает сообщения или уведомлять (проверьте характеристики свойства, которые нужно убедитесь, что). 

> **Отмена**: указывают считается более надежная, так как каждое событие изменилось сочетается с ответа от устройства клиента. Уведомить является более широкое распространение, поскольку большинство операций GATT будет вместо сохранения питания, а не быть очень надежным. В любом случае все это обрабатывается на уровне контроллера, поэтому приложения не примите участие. Мы будем Собирательно называют просто «уведомления», но теперь вы знаете. 

Существует две вещи взяла на себя до получения уведомления.
- Запись в дескриптор характеристик конфигурации клиента (CCCD)
- Обработка события Characteristic.ValueChanged

Запись в CCCD сообщает устройству сервера, что этот клиент хочет знать каждый раз для изменения определенного значения характеристик. Для этого выполните следующие действия. 

```csharp
GattCommunicationStatus status = await selectedCharacteristic.WriteClientCharacteristicConfigurationDescriptorAsync(
                        GattClientCharacteristicConfigurationDescriptorValue.Notify);
if(status == GattCommunicationStatus.Success)
{
    // Server has been informed of clients interest.
}
```
Теперь событие ValueChanged GattCharacteristic будет вызван каждый раз, когда значение получает изменено на удаленном устройстве. Осталось является реализация обработчика: 

```csharp
characteristic.ValueChanged += Characteristic_ValueChanged;
// ... 

void Characteristic_ValueChanged(GattCharacteristic sender, 
                                    GattValueChangedEventArgs args)
{
    // An Indicate or Notify reported that the value has changed.
    var reader = DataReader.FromBuffer(args.CharacteristicValue)
    // Parse the data however required.
}
```


