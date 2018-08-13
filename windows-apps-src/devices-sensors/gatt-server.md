---
author: msatranjr
title: Сервер GATT Bluetooth
description: В этой статье Обзор сервера Bluetooth универсальный атрибут профиля (GATT) для приложений, универсальные платформы Windows (UWP), а также пример кода для наиболее распространенных случаев использования.
ms.author: misatran
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 27154fbb535b76995fba97702e65a9c0b2a8291c
ms.sourcegitcommit: 897a111e8fc5d38d483800288ad01c523e924ef4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/13/2018
ms.locfileid: "610767"
---
# <a name="bluetooth-gatt-server"></a>Сервер GATT Bluetooth


**Важные API**
- [**Windows.Devices.Bluetooth**](https://msdn.microsoft.com/library/windows/apps/Dn263413)
- [**Windows.Devices.Bluetooth.GenericAttributeProfile**](https://msdn.microsoft.com/library/windows/apps/Dn297685)


В этой статье демонстрируется Bluetooth универсальный атрибут (GATT) сервера API-интерфейсы для приложений, универсальные платформы Windows (UWP), а также примеры кода для распространенных задач сервера GATT: 
- Определение поддерживаемых служб
- Публикация сервера, поэтому обнаруживаться удаленных клиентов
- Advertise поддержки для службы
- Отвечать на запросы чтения и записи
- Отправлять уведомления на подписанном клиентов

## <a name="overview"></a>Обзор
Windows обычно находится в роли клиента. Тем не менее самые разные сценарии проблемах, требующими Windows для использования в качестве также сервера GATT LE Bluetooth. Почти все сценарии для IoT устройств, а также большинство связи BLE кроссплатформенный потребует Windows в качестве сервера GATT. Кроме того для отправки уведомлений рядом wearable устройств стала популярные сценарий, который требует эти технологии также.  
> Убедитесь, что все концепции [GATT клиента: документы](gatt-client.md) перед тем как очистить.  

Операций сервера будет связана с поставщиком услуг и GattLocalCharacteristic. Эти два класса будет предоставлять функциональные возможности, необходимые для объявления, реализации и предоставления иерархия данных для удаленных устройств.

## <a name="define-the-supported-services"></a>Определение поддерживаемых служб
Ваше приложение может сообщать одну или несколько служб, которые будут опубликованы в Windows. Каждой службы, которое уникально определяется UUID. 

### <a name="attributes-and-uuids"></a>Атрибуты и UUID
Определяется каждой службы, характеристики и дескриптор, это собственное уникальное UUID 128-бит.
> Windows API-интерфейсы все использовать терминов идентификатор GUID, но стандарт Bluetooth определяет их как UUID. В нашем случае эти два термины являются взаимозаменяемыми, вы сможете продолжить использование термина UUID. 

Если атрибут является стандартным и определенных пользователем подпись Bluetooth, также будет соответствующий короткий идентификатор 16-разрядной (например батарей уровень UUID является 0000**2A19**-0000-1000-8000-00805F9B34FB и короткий идентификатор является 0x2A19). Следующие стандартные UUID может отображаться в [GattServiceUuids](https://msdn.microsoft.com/en-us/library/windows/apps/windows.devices.bluetooth.genericattributeprofile.gattserviceuuids.aspx) и [GattCharacteristicUuids](https://msdn.microsoft.com/en-us/library/windows/apps/windows.devices.bluetooth.genericattributeprofile.gattcharacteristicuuids.aspx).

Если реализация приложения собственные настраиваемые службы, настраиваемые UUID будут иметь был создан. Это легко в Visual Studio через средства -> CreateGuid (вариант использования 5 для получения в формате «xxxxxxxx-xxxx-... xxxx»). В этом uuid теперь можно использовать для объявления нового локальным службам, характеристики или дескрипторов.

#### <a name="restricted-services"></a>Ограниченное служб
Следующие службы зарезервированы системой и не могут быть опубликованы в настоящее время:
1. Служба сведений о устройства (DIS)
2. Служба профилей универсальный атрибут (GATT)
3. Служба профилей универсального доступа (пробел)
4. Службы пользовательского интерфейса устройства (HOGP)
5. Проверка параметров службы (SCP)

> Попытка создать заблокированных службы приведет к BluetoothError.DisabledByPolicy, возвращенных вызов CreateAsync.

#### <a name="generated-attributes"></a>Созданный код атрибуты
Следующие дескрипторов формируются системой на основании GattLocalCharacteristicParameters, полученное во время создания эти характеристики:
1. Конфигурация характеристику клиента (если эти характеристики помечен как indicatable или notifiable).
2. Типичное пользовательское описание (если свойство UserDescription имеет значение). В разделе Свойства GattLocalCharacteristicParameters.UserDescription для получения дополнительных сведений.
3. Типичное формат (один дескриптор для каждого представления в формате указан).  В разделе Свойства GattLocalCharacteristicParameters.PresentationFormats для получения дополнительных сведений.
4. Типичное формат статистической обработки (Если указано более одного формата презентаций).  Свойство GattLocalCharacteristicParameters.See PresentationFormats для получения дополнительных сведений.
5. Типичное расширенные свойства (если эти характеристики помечено разрядная расширенные свойства).

> С помощью свойства ReliableWrites и WritableAuxiliaries характеристик определяется значение дескриптора расширенные свойства.

> Попытка создания зарезервированные дескриптор приведет к исключение.

> Обратите внимание, что вещания в настоящее время не поддерживается.  Указание GattCharacteristicProperty вещания вызовет исключение.

### <a name="build-up-the-heirarchy-of-services-and-characteristics"></a>Иерархия служб и характеристики построения
GattServiceProvider используется для создания и объявление Определение корневого основной службы.  Каждая служба требует что это собственный поставщик службы объекта, который принимает в GUID: 

```csharp
GattServiceProviderResult result = await GattServiceProvider.CreateAsync(uuid);

if (result.Error == BluetoothError.Success)
{
    serviceProvider = result.ServiceProvider;
    // 
}
```
> Основной службы, верхнего уровня в дереве GATT. Основные службы содержат характеристики, а также другие службы (называемого «Включено» или дополнительного служб). 

Теперь укажите службы с помощью требуемые характеристики и дескрипторов:

```csharp
GattLocalCharacteristicResult characteristicResult = await serviceProvider.Service.CreateCharacteristicAsync(uuid1, ReadParameters);
if (characteristicResult.Error != BluetoothError.Success)
{
    // An error occurred.
    return;
}
_readCharacteristic = characteristicResult.Characteristic;
_readCharacteristic.ReadRequested += ReadCharacteristic_ReadRequested;

characteristicResult = await serviceProvider.Service.CreateCharacteristicAsync(uuid2, WriteParameters);
if (characteristicResult.Error != BluetoothError.Success)
{
    // An error occurred.
    return;
}
_writeCharacteristic = characteristicResult.Characteristic;
_writeCharacteristic.WriteRequested += WriteCharacteristic_WriteRequested;

characteristicResult = await serviceProvider.Service.CreateCharacteristicAsync(uuid3, NotifyParameters);
if (characteristicResult.Error != BluetoothError.Success)
{
    // An error occurred.
    return;
}
_notifyCharacteristic = characteristicResult.Characteristic;
_notifyCharacteristic.SubscribedClientsChanged += SubscribedClientsChanged;
```
Как показано выше, это также отличная отправная точка для объявления обработчиков событий для каждой из которых поддерживает операций.  Отвечать на запросы правильно, необходимо приложения определены и задать обработчик событий для каждого типа запроса, атрибут поддерживает.  Восстановления после сбоя зарегистрировать обработчик приведет к запроса, выполняемой сразу же с *UnlikelyError* системы.

### <a name="constant-characteristics"></a>Константы характеристики
В некоторых случаях существует характеристик значения, которые не будет изменяться во время жизни приложения. В этом случае рекомендуется для объявления констант характеристик для предотвращения активации ненужные приложения: 

```csharp
byte[] value = new byte[] {0x21};
var constantParameters = new GattLocalCharacteristicParameters
{
    CharacteristicProperties = (GattCharacteristicProperties.Read),
    StaticValue = value.AsBuffer(),
    ReadProtectionLevel = GattProtectionLevel.Plain,
};

var characteristicResult = await serviceProvider.Service.CreateCharacteristicAsync(uuid4, constantParameters);
if (characteristicResult.Error != BluetoothError.Success)
{
    // An error occurred.
    return;
}
```
## <a name="publish-the-service"></a>Публикация службы
После полностью определенные службы следующим шагом является публикация поддержки для службы. Это информирует операционной системы, что службы должны быть возвращены при выполнении службы обнаружения удаленных устройств.  Необходимо установить два свойства - IsDiscoverable и IsConnectable:  

```csharp
GattServiceProviderAdvertisingParameters advParameters = new GattServiceProviderAdvertisingParameters
{
    IsDiscoverable = true,
    IsConnectable = true
};
serviceProvider.StartAdvertising(advParameters);
```
- **IsDiscoverable**: объявляет понятное имя для удаленных устройств в объявлении внесения обнаруживаемые устройства.
- **IsConnectable**: объявляет подключаемых объявления для использования в периферийный роли.

> Когда службы обнаруживаемый и Connectable, система добавит Uuid службы объявления пакета.  Существует только 31 байт в пакете объявления и использования 128-битового UUID занимает 16 из них!

> Обратите внимание, что после публикации службы на переднем плане приложения необходимо вызвать StopAdvertising при приостанавливается приложения.

## <a name="respond-to-read-and-write-requests"></a>Отвечать на запросы чтения и записи
Как было показано выше, а при объявлении требуемые характеристики, GattLocalCharacteristics имеют 3 типы событий - ReadRequested, WriteRequested и SubscribedClientsChanged.

### <a name="read"></a>Read
При удаленных устройств пытается прочитать значение из характеристик (и не является постоянным значением), вызывается событие ReadRequested. Эти характеристики, прочтите был вызван на, а также args (содержащие сведения об удаленных устройств) передается делегат: 

```csharp
characteristic.ReadRequested += Characteristic_ReadRequested;
// ... 

async void ReadCharacteristic_ReadRequested(GattLocalCharacteristic sender, GattReadRequestedEventArgs args)
{
    var deferral = args.GetDeferral();
    
    // Our familiar friend - DataWriter.
    var writer = new DataWriter();
    // populate writer w/ some data. 
    // ... 

    var request = await args.GetRequestAsync();
    request.RespondWithValue(writer.DetachBuffer());
    
    deferral.Complete();
}
``` 

### <a name="write"></a>Запись
При попытке записи значения характеристик удаленного устройства, событие WriteRequested вызывается с сведения об удаленных устройств, какие характеристики для записи и значение: 

```csharp
characteristic.ReadRequested += Characteristic_ReadRequested;
// ...

async void WriteCharacteristic_WriteRequested(GattLocalCharacteristic sender, GattWriteRequestedEventArgs args)
{
    var deferral = args.GetDeferral();
    
    var request = await args.GetRequestAsync();
    var reader = DataReader.FromBuffer(request.Value);
    // Parse data as necessary. 

    if (request.Option == GattWriteOption.WriteWithResponse)
    {
        request.Respond();
    }
    
    deferral.Complete();
}
```
Существует 2 типа записи - с и без ответа. Используйте GattWriteOption (свойство объекта GattWriteRequest), чтобы определить, какой тип записи, используемая для выполнения удаленных устройств. 

## <a name="send-notifications-to-subscribed-clients"></a>Отправлять уведомления на подписанном клиентов
Наиболее часто используемых операций сервера GATT уведомлений выполните критические функции отправлять данные на удаленных устройств. В некоторых случаях может потребоваться для уведомления всех подписанных клиентов, но othertimes, необходимо ответить на какие устройства для отправки новое значение для: 

```csharp
async void NotifyValue()
{
    var writer = new DataWriter();
    // Populate writer with data
    // ...
    
    await notifyCharacteristic.NotifyValueAsync(writer.DetachBuffer());
}
```

Когда подписывается новое устройство для уведомлений, вызывается событие SubscribedClientsChanged: 

```csharp
characteristic.SubscribedClientsChanged += SubscribedClientsChanged;
// ...

void _notifyCharacteristic_SubscribedClientsChanged(GattLocalCharacteristic sender, object args)
{
    List<GattSubscribedClient> clients = sender.SubscribedClients;
    // Diff the new list of clients from a previously saved one 
    // to get which device has subscribed for notifications. 

    // You can also just validate that the list of clients is expected for this app.  
}

```
> Обратите внимание, что приложение можно получить уведомлений максимальный размер для конкретного клиента с помощью свойства MaxNotificationSize.  Все данные, размер которых превышает максимальный размер усекаются системой.
