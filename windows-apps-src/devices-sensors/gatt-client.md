---
title: Клиент Bluetooth GATT
description: В этой статье представлен обзор клиента Bluetooth Generic Attribute Profile (GATT) для приложений универсальной платформы Windows (UWP), а также пример кода для распространенных сценариев.
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: e894b750ba2119e2cca6f316f9671c51386d800c
ms.sourcegitcommit: e51f9489d8c977c3498afb1a75c91f96ac3a642b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/26/2020
ms.locfileid: "83854680"
---
# <a name="bluetooth-gatt-client"></a>Клиент Bluetooth GATT

В этой статье описывается использование API-интерфейсов клиента Bluetooth Generic Attribute (GATT) для приложений универсальной платформы Windows (UWP) и приводится пример кода для основных задач клиента GATT.

- Отправка запросов для ближайших устройств
- Подключение к устройству
- Перечисление поддерживаемых служб и характеристик устройства
- Чтение и запись в характеристику
- Подписка на уведомления об изменении значения характеристики

> **Важные API**
>
> - [**Windows. Devices. Bluetooth**](https://docs.microsoft.com/uwp/api/Windows.Devices.Bluetooth)
> - [**Windows. Devices. Bluetooth. Женерикаттрибутепрофиле**](https://docs.microsoft.com/uwp/api/Windows.Devices.Bluetooth.GenericAttributeProfile)

## <a name="overview"></a>Обзор

С помощью API-интерфейсов в пространстве имен [**Windows.Devices.Bluetooth.GenericAttributeProfile**](https://docs.microsoft.com/uwp/api/Windows.Devices.Bluetooth.GenericAttributeProfile) разработчики получают доступ к устройствам Bluetooth с низким энергопотреблением. Функции устройств Bluetooth с низким энергопотреблением обеспечиваются коллекцией следующих элементов:

- Службы
- Характеристики
- Дескрипторы

Службы определяют функциональный контракт устройства с низким энергопотреблением и содержат набор характеристик, определяющих службу. Характеристики в свою очередь содержат описывающие их дескрипторы. Эти 3 элемента, как правило, называют атрибутами устройства.

API-интерфейсы Bluetooth GATT для устройств с низким энергопотреблением предоставляют доступ к объектам и функциям, а не собственно к механизмам передачи. Кроме того, API-интерфейсы Bluetooth GATT позволяют разработчикам работать с устройствами Bluetooth с низким энергопотреблением и выполнять следующие задачи:

- Обнаружение атрибутов
- Чтение и запись значений атрибутов
- Регистрация обратного вызова для события Characteristic ValueChanged

Для эффективной реализации разработчик должен иметь представление о службах и характеристиках GATT, которые будет обрабатывать приложение. Он должен уметь преобразовывать значения отдельных характеристик, например двоичных данных, предоставляемых API-интерфейсами, в полезные данные, отображаемые для пользователей. API Bluetooth GATT предоставляют только основные примитивы, необходимые для взаимодействия с устройством Bluetooth с низким энергопотреблением. Для интерпретации данных необходимо определить профиль приложения. Для этого используется либо стандартный профиль Bluetooth SIG, либо настраиваемый профиль, реализованный поставщиком устройства. Профиль создает контракт привязки между приложением и устройством в отношении содержимого передаваемых данных и их интерпретации.

Для удобства Bluetooth SIG ведет [список доступных общих профилей](https://www.bluetooth.com/specifications/adopted-specifications#gattspec).

## <a name="query-for-nearby-devices"></a>Отправка запросов для ближайших устройств

Существует два основных метода отправки запросов для ближайших устройств:

- DeviceWatcher в Windows.Devices.Enumeration
- AdvertisementWatcher в Windows.Devices.Bluetooth.Advertisement

Второй метод подробно рассматривается в документации по классу [Advertisement](ble-beacon.md), поэтому здесь упоминается только основной принцип — поиск адресов ближайших устройств Bluetooth, соответствующих критериям конкретного [фильтра объявления](https://docs.microsoft.com/uwp/api/windows.devices.bluetooth.advertisement.bluetoothleadvertisementwatcher.advertisementfilter). Когда адрес найден, можно вызвать [BluetoothLEDevice.FromBluetoothAddressAsync](https://docs.microsoft.com/uwp/api/windows.devices.bluetooth.bluetoothledevice.frombluetoothaddressasync) для получения ссылки на устройство.

Вернемся к методу DeviceWatcher. Устройство Bluetooth с низким энергопотреблением ничем не отличается от любых других устройств в Windows. Запрос к этому устройству можно отправить с помощью [API-интерфейсов перечисления](https://docs.microsoft.com/uwp/api/Windows.Devices.Enumeration). Используйте класс [DeviceWatcher](https://docs.microsoft.com/uwp/api/windows.devices.enumeration.devicewatcher), чтобы передать строку запроса с указанием искомых устройств:

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

После запуска DeviceWatcher вы получите [DeviceInformation](https://docs.microsoft.com/uwp/api/Windows.Devices.Enumeration.DeviceInformation) для каждого устройства, соответствующего критериям запроса, в обработчике события [Added](https://docs.microsoft.com/uwp/api/windows.devices.enumeration.devicewatcher.added) для соответствующих устройств. Чтобы получить более полное представление о методе DeviceWatcher, см. полный пример кода [на Github](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/DeviceEnumerationAndPairing).

## <a name="connecting-to-the-device"></a>Подключение к устройству

После обнаружения нужного устройства используйте [DeviceInformation.Id](https://docs.microsoft.com/uwp/api/windows.devices.enumeration.deviceinformation.id), чтобы получить объект BluetoothLEDevice для соответствующего устройства:

```csharp
async void ConnectDevice(DeviceInformation deviceInfo)
{
    // Note: BluetoothLEDevice.FromIdAsync must be called from a UI thread because it may prompt for consent.
    BluetoothLEDevice bluetoothLeDevice = await BluetoothLEDevice.FromIdAsync(deviceInfo.Id);
    // ...
}
```

С другой стороны, при удалении всех ссылок на объект BluetoothLEDevice инициируется автоматическое отключение от устройства после короткого периода ожидания (если на это устройство не ссылаются другие приложения в системе).

```csharp
bluetoothLeDevice.Dispose();
```

Если приложению снова потребуется доступ к устройству, создайте объект устройства заново и предоставьте доступ к характеристике (см. следующий раздел). После этого операционная система будет инициировать повторное подключение по требованию. Если устройство находится поблизости, вы получите доступ к нему. В противном случае будет получен ответ с ошибкой DeviceUnreachable.  

## <a name="enumerating-supported-services-and-characteristics"></a>Перечисление поддерживаемых служб и характеристик

Теперь, когда у вас есть объект BluetoothLEDevice, можно перейти к следующему шагу — обнаружению данных, предоставляемых устройством. Сначала необходимо запросить службы:

```csharp
GattDeviceServicesResult result = await bluetoothLeDevice.GetGattServicesAsync();

if (result.Status == GattCommunicationStatus.Success)
{
    var services = result.Services;
    // ...
}
```

После определения нужных служб необходимо запросить характеристики.

```csharp
GattCharacteristicsResult result = await service.GetCharacteristicsAsync();

if (result.Status == GattCommunicationStatus.Success)
{
    var characteristics = result.Characteristics;
    // ...
}
```

Операционная система возвращает список ReadOnly, где перечислены объекты GattCharacteristic, с которыми можно выполнять операции.

## <a name="perform-readwrite-operations-on-a-characteristic"></a>Выполнение операций чтения и записи с характеристикой

Характеристика — неотъемлемая часть обмена данными с использованием GATT. Она содержит значение, представляющее уникальный фрагмент данных на устройстве. Например, характеристика Battery Level содержит значение, представляющее уровень заряда аккумулятора устройства.

Изучите свойства характеристики, чтобы определить поддерживаемые операции:

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

Если поддерживается чтение, можно прочитать значение:

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

Запись в характеристику выполняется по аналогичному шаблону:

```csharp
var writer = new DataWriter();
// WriteByte used for simplicity. Other common functions - WriteInt16 and WriteSingle
writer.WriteByte(0x01);

GattCommunicationStatus result = await selectedCharacteristic.WriteValueAsync(writer.DetachBuffer());
if (result == GattCommunicationStatus.Success)
{
    // Successfully wrote to device
}
```

> **Совет**. [DataReader](https://docs.microsoft.com/uwp/api/windows.storage.streams.datareader) и незаменимым используются при работе с необработанными буферами [,](https://docs.microsoft.com/uwp/api/windows.storage.streams.datawriter) полученными из многих API-интерфейсов Bluetooth.

## <a name="subscribing-for-notifications"></a>Подписка на уведомления

Проверьте свойства характеристики и убедитесь, что характеристика поддерживает операцию Indicate или Notify.

> **Примечание**. Операция Indicate считается более надежной, поскольку каждое событие изменения значения подтверждается клиентским устройством. Операция Notify используется чаще, так как большинство транзакций GATT обеспечивает экономию энергии, а не высочайший уровень надежности. Так или иначе, все эти операции обрабатываются на уровне контроллера без участия приложения. В дальнейшем для этих операций будет использоваться собирательный термин "уведомления".

Перед получением уведомлений необходимо обеспечить выполнение двух условий:

- Запись в дескриптор конфигурации характеристик клиента (Client Characteristic Configuration, CCCD)
- Обработка события Characteristic.ValueChanged

Запись в CCCD сообщает серверу, что данный клиент должен получать оповещение каждый раз, когда меняется значение определенной характеристики. Для этого выполните следующие действия.

```csharp
GattCommunicationStatus status = await selectedCharacteristic.WriteClientCharacteristicConfigurationDescriptorAsync(
                        GattClientCharacteristicConfigurationDescriptorValue.Notify);
if(status == GattCommunicationStatus.Success)
{
    // Server has been informed of clients interest.
}
```

Теперь событие ValueChanged для GattCharacteristic будет вызываться при каждом изменении значения на удаленном устройстве. Осталось только реализовать обработчик:

```csharp
characteristic.ValueChanged += Characteristic_ValueChanged;

...

void Characteristic_ValueChanged(GattCharacteristic sender,
                                    GattValueChangedEventArgs args)
{
    // An Indicate or Notify reported that the value has changed.
    var reader = DataReader.FromBuffer(args.CharacteristicValue)
    // Parse the data however required.
}
```
