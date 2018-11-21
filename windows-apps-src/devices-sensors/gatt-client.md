---
author: msatranjr
title: Клиент GATT Bluetooth
description: В этой статье представлен обзор Bluetooth Generic Attribute Profile (GATT) клиента для приложений универсальной платформы Windows (UWP), а также пример кода для наиболее распространенных случаев использования.
ms.author: misatran
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 345e6f82ddf97c2595dad0029ca432f075a6190b
ms.sourcegitcommit: cbe7cf620622a5e4df7414f9e38dfecec1cfca99
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/21/2018
ms.locfileid: "7439545"
---
# <a name="bluetooth-gatt-client"></a>Клиент GATT Bluetooth


**Важные API**

-   [**Windows.Devices.Bluetooth**](https://msdn.microsoft.com/library/windows/apps/Dn263413)
-   [**Windows.Devices.Bluetooth.GenericAttributeProfile**](https://msdn.microsoft.com/library/windows/apps/Dn297685)

В этой статье показано использование API-интерфейсы клиента Bluetooth Generic Attribute (GATT) для приложений универсальной платформы Windows (UWP), а также пример кода для типичных задач клиент GATT:
- Запрос ближайшие устройства
- Подключите к устройству
- Перечисление поддерживаемых служб и характеристик устройства
- Чтение и запись в характеристикой
- Подписаться для уведомления при форматировать значение изменений

## <a name="overview"></a>Обзор
Разработчики могут использовать API-интерфейсы в пространстве имен [**Windows.Devices.Bluetooth.GenericAttributeProfile**](https://msdn.microsoft.com/library/windows/apps/Dn297685) для доступа к устройствам Bluetooth с низким ЭНЕРГОПОТРЕБЛЕНИЕМ. Функции устройств Bluetooth с низким энергопотреблением доступны через набор:

-   Службы
-   характеристик;
-   дескрипторов.

Службы определяют функциональный контракт устройства с низким ЭНЕРГОПОТРЕБЛЕНИЕМ и содержат коллекцию характеристик, определяющих службу. Эти характеристики, в свою очередь, содержат дескрипторы, которые описывают характеристики. Эти условия 3 сканером, известны как атрибуты устройства.

API-интерфейсы Bluetooth с низким ЭНЕРГОПОТРЕБЛЕНИЕМ GATT предоставляют доступ к объектам и функциям, вместо того, доступ к механизмам передачи. API GATT также позволяют разработчикам работать с устройствами Bluetooth с низким ЭНЕРГОПОТРЕБЛЕНИЕМ возможность выполнять следующие задачи:

-   Выполнять обнаружение атрибута
-   Чтение и запись значений атрибутов
-   Регистрация обратного вызова для события Characteristic.ValueChanged.

Для создания реализации разработчик должен иметь представление о службах и характеристиках GATT, приложения для эффективной и для процесса значения отдельных характеристик, например двоичных данных, предоставляемых API в полезные данные перед достаточны для пользователя. API Bluetooth GATT предоставляют только основные примитивы, необходимые для взаимодействия с устройством Bluetooth с низким энергопотреблением. Для интерпретации данных необходимо определить профиль приложения. Для этого используется либо стандартный профиль Bluetooth SIG, либо настраиваемый профиль, реализованный поставщиком устройства. Профиль создает контракт привязки между приложением и устройством в отношении содержимого передаваемых данных и их интерпретации.

Для удобства Bluetooth SIG ведет [список доступных общих профилей](https://www.bluetooth.com/specifications/adopted-specifications#gattspec).

## <a name="query-for-nearby-devices"></a>Запрос ближайшие устройства
Существует два основных способа запросить ближайшие устройства.
- DeviceWatcher в Windows.Devices.Enumeration
- AdvertisementWatcher в Windows.Devices.Bluetooth.Advertisement

Метод 2-го описывается объяснял в документации для [объявления](ble-beacon.md) поэтому он не будет рассказано гораздо здесь, но основная идея — это адрес Bluetooth ближайшие устройства, которые удовлетворяют определенным [Рекламный фильтр](https://msdn.microsoft.com/en-us/library/windows/apps/windows.devices.bluetooth.advertisement.bluetoothleadvertisementwatcher.advertisementfilter.aspx)поиска. Получив адрес, вы можете вызвать [BluetoothLEDevice.FromBluetoothAddressAsync](https://msdn.microsoft.com/en-us/library/windows/apps/mt608819.aspx) для получения ссылки на устройстве. 

Вернемся к методу DeviceWatcher. Bluetooth с НИЗКИМ энергопотреблением так же, как и любого другого устройства в Windows и можно запросить с помощью [Интерфейсов API перечисления](https://msdn.microsoft.com/library/windows/apps/BR225459). Используйте класс [DeviceWatcher](https://msdn.microsoft.com/en-us/library/windows/apps/windows.devices.enumeration.devicewatcher) и передайте строку запроса, указав устройств для поиска: 

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
После начала DeviceWatcher, вы будете получать [DeviceInformation](https://msdn.microsoft.com/library/windows/apps/br225393) для каждого устройства, который удовлетворяет запрос в обработчике для события [Added](https://msdn.microsoft.com/en-us/library/windows/apps/windows.devices.enumeration.devicewatcher.added) рассматриваемый устройств. Подробнее рассмотрим DeviceWatcher см. Полный пример [на Github](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/DeviceEnumerationAndPairing). 

## <a name="connecting-to-the-device"></a>Подключение к устройству
После обнаружения нужного устройства, используйте [DeviceInformation.Id](https://msdn.microsoft.com/en-us/library/windows/apps/windows.devices.enumeration.deviceinformation.id) для получения объекта устройства Bluetooth с низким ЭНЕРГОПОТРЕБЛЕНИЕМ для устройства: 

```csharp
async void ConnectDevice(DeviceInformation deviceInfo)
{
    // Note: BluetoothLEDevice.FromIdAsync must be called from a UI thread because it may prompt for consent.
    BluetoothLEDevice bluetoothLeDevice = await BluetoothLEDevice.FromIdAsync(deviceInfo.Id);
    // ...
}
```
С другой стороны, ликвидировать все ссылки на BluetoothLEDevice объект на устройстве (и если другое приложение в системе ссылку на устройстве) будет отправлено автоматически отключиться после небольшой ожидания. 

```csharp
bluetoothLeDevice.Dispose();
```
Если приложение должно снова доступ к устройству, просто повторно создать объект устройства и доступ к характеристикой (описано в следующем разделе) будет отправлено операционная система повторно подключиться при необходимости. Если устройство находится поблизости от него, вы получите доступ к устройству в противном случае будет возвращено с ошибкой DeviceUnreachable.  

## <a name="enumerating-supported-services-and-characteristics"></a>Перечисление поддерживаемых службах и характеристиках
Теперь у вас есть объект BluetoothLEDevice, следующий шаг — для обнаружения какие данные, предоставляет устройства. Первым шагом для этого является запрашивать службы: 

```csharp
GattDeviceServicesResult result = await bluetoothLeDevice.GetGattServicesAsync();
                
if (result.Status == GattCommunicationStatus.Success)
{
    var services = result.Services;
    // ...
}
```
После идентификации служба объектов следующим шагом является запрос характеристики. 

```csharp
GattCharacteristicsResult result = await service.GetCharacteristicsAsync();
                
if (result.Status == GattCommunicationStatus.Success)
{
    var characteristics = result.Characteristics;
    // ...
}
```  
Операционная система возвращает список ReadOnly GattCharacteristic объектов, затем можно выполнять операции с.

## <a name="perform-readwrite-operations-on-a-characteristic"></a>Выполнять операции чтения и записи на характеристикой

Эта характеристика — связь, основанная на неотъемлемой частью GATT. Он содержит значение, которое представляет определенную часть данных на устройстве. Например характеристик уровня заряда батареи имеет значение, представляющее уровень заряда аккумулятора устройства.

Прочтите характеристик свойства, чтобы определить, какие операции поддерживаются.
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

Если поддерживается чтения, вы можете прочитать значение: 
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
Запись характеристикой выполняется по аналогичной схеме: 
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
> **Совет**: получение знакомы с использованием [DataReader](https://msdn.microsoft.com/en-us/library/windows/apps/windows.storage.streams.datareader.aspx) и [DataWriter](https://msdn.microsoft.com/en-us/library/windows/apps/windows.storage.streams.datawriter.aspx). Их функциональные возможности будут необходимые при работе с необработанных буферов, которые вы получаете от многих интерфейсов API для Bluetooth. 
## <a name="subscribing-for-notifications"></a>Подписка для уведомлений

Убедитесь, что эта характеристика поддерживает указывать или уведомление (проверьте характеристик свойства, чтобы убедиться, что). 

> **Выделяет**: указать, считается более надежным, так как каждое событие изменения значения сочетается с подтверждение с клиентского устройства. Уведомлять является более наиболее распространенными вредоносными программами, поскольку большинство операций GATT бы вместо энергии вместо упором. В любом случае все из них обрабатывается на уровне контроллера, чтобы приложение не участвует. Мы будем визуально называть их как просто «notifications», но теперь вы знаете. 

Существует два аспекта, которые обеспечивают до получения уведомления.
- Запись в дескриптор характеристик конфигурации клиента (CCCD)
- Обработка события Characteristic.ValueChanged

Запись CCCD сообщает устройству сервера, что этот клиент хочет получить каждый раз, когда изменения этого конкретного значения характеристики термометра. Для этого выполните следующие действия. 

```csharp
GattCommunicationStatus status = await selectedCharacteristic.WriteClientCharacteristicConfigurationDescriptorAsync(
                        GattClientCharacteristicConfigurationDescriptorValue.Notify);
if(status == GattCommunicationStatus.Success)
{
    // Server has been informed of clients interest.
}
```
Теперь событие ValueChanged GattCharacteristic будет вызываться каждый раз, когда значение получает было изменено на удаленном устройстве. Остается является реализация обработчика: 

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


