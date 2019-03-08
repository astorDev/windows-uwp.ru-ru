---
title: Сервер Bluetooth GATT
description: В этой статье представлен обзор сервера Bluetooth Generic Attribute Profile (GATT) для приложений универсальной платформы Windows (UWP), а также пример кода для распространенных сценариев.
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 551f8b925ffd56950ba893da7b81fefb4579f558
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57635139"
---
# <a name="bluetooth-gatt-server"></a>Сервер Bluetooth GATT


**Важные API**
- [**Windows.Devices.Bluetooth**](https://msdn.microsoft.com/library/windows/apps/Dn263413)
- [**Windows.Devices.Bluetooth.GenericAttributeProfile**](https://msdn.microsoft.com/library/windows/apps/Dn297685)


В этой статье описываются API-интерфейсы сервера Bluetooth Generic Attribute (GATT) для приложений универсальной платформы Windows (UWP) и приводится пример кода для основных задач сервера GATT. 
- Определение поддерживаемых служб
- Публикация сервера (необходима для того, чтобы удаленные клиенты могли обнаружить этот сервер)
- Объявление поддержки для службы
- Реагирование на запросы чтения и записи
- Отправка уведомлений подписанным клиентам

## <a name="overview"></a>Обзор
Как правило, Windows функционирует в роли клиента. Однако существует множество сценариев, когда операционная система Windows должна выполнять функции сервера Bluetooth GATT с низким энергопотреблением. Сюда относятся почти все сценарии для устройств интернета вещей, а также большинство сценариев обмена данными с использованием BLE. Все эти сценарии требуют, чтобы операционная система Windows функционировала как сервер GATT. Кроме того, эта технология обязательна для такого популярного сценария, как отправка уведомлений на ближайшие носимые устройства.  
> Прежде чем продолжить, убедитесь, что вам знакомы все основные понятия, описанные в [документации клиента GATT](gatt-client.md).  

Операции сервера связаны с классами Service Provider и GattLocalCharacteristic. Эти два класса предоставит функциональность, необходимую для объявления, реализации и предоставления иерархия данных к удаленному устройству.

## <a name="define-the-supported-services"></a>Определение поддерживаемых служб
Приложение может объявить одну или несколько служб, которые будут опубликованы в Windows. Каждая служба имеет уникальный идентификатор UUID. 

### <a name="attributes-and-uuids"></a>Атрибуты и идентификаторы UUID
Каждая служба, характеристика и дескриптор имеет свой уникальный 128-битный идентификатор UUID.
> Ко всем API-интерфейсам Windows применяется термин GUID, но в стандарте Bluetooth этот идентификатор определяется как UUID. В нашем случае эти два термина взаимозаменяемы, поэтому мы будем использовать термин UUID. 

Если атрибут является стандартным и определяется профилем Bluetooth SIG, у него также будет короткий 16-битный идентификатор (например, атрибут Battery Level имеет UUID 0000**2A19**-0000-1000-8000-00805F9B34FB, а его короткий идентификатор выглядит как 0x2A19). Эти стандартные идентификаторы UUID описаны в статьях [GattServiceUuids](https://msdn.microsoft.com/en-us/library/windows/apps/windows.devices.bluetooth.genericattributeprofile.gattserviceuuids.aspx) и [GattCharacteristicUuids](https://msdn.microsoft.com/en-us/library/windows/apps/windows.devices.bluetooth.genericattributeprofile.gattcharacteristicuuids.aspx).

Если ваше приложение реализует собственную службу, необходимо создать пользовательский UUID. Это легко сделать в Visual Studio с помощью команды Tools (Инструменты) -> CreateGuid (используйте вариант 5, чтобы получить идентификатор в формате "xxxxxxxx-xxxx-... xxxx"). Этот UUID можно использовать для объявления новых локальных служб, характеристик или дескрипторов.

#### <a name="restricted-services"></a>Службы с ограниченным доступом
Перечисленные ниже службы зарезервированы системой и в настоящее время недоступны для публикации:
1. Device Information Service (DIS)
2. Generic Attribute Profile Service (GATT)
3. Generic Access Profile Service (GAP)
4. Human Interface Device Service (HOGP)
5. Scan Parameters Service (SCP)

> При попытке создать заблокированную службу вызов к CreateAsync вернет ошибку BluetoothError.DisabledByPolicy.

#### <a name="generated-attributes"></a>Генерируемые атрибуты
Перечисленные ниже дескрипторы автоматически генерируются системой в соответствии с классом GattLocalCharacteristicParameters, предоставленным в процессе создания характеристики:
1. Client Characteristic Configuration (если характеристика имеет маркер indicatable или notifiable)
2. Characteristic User Description (если задано свойство UserDescription) Для получения дополнительных сведений см. свойство GattLocalCharacteristicParameters.UserDescription.
3. Characteristic Format (по одному дескриптору на каждый указанный формат представления)  Для получения дополнительных сведений см. свойство GattLocalCharacteristicParameters.PresentationFormats.
4. Characteristic Aggregate Format (если указано несколько форматов представления)  Для получения дополнительных сведений см. свойство GattLocalCharacteristicParameters.PresentationFormats.
5. Characteristic Extended Properties (если характеристика помечена битом расширенных свойств)

> Значение дескриптора Extended Properties определяется свойствами характеристики ReliableWrites и WritableAuxiliaries.

> Попытка создать зарезервированный дескриптор вызывает исключение.

> Примечание: широковещательная рассылка в настоящее время не поддерживается.  Если задать свойство Broadcast GattCharacteristicProperty, будет создано исключение.

### <a name="build-up-the-hierarchy-of-services-and-characteristics"></a>Построение вверх по иерархии, служб и характеристики
Для создания и объявления определения основной корневой службы используется класс GattServiceProvider.  Каждой службе требуется собственный объект ServiceProvider, в который передается GUID: 

```csharp
GattServiceProviderResult result = await GattServiceProvider.CreateAsync(uuid);

if (result.Error == BluetoothError.Success)
{
    serviceProvider = result.ServiceProvider;
    // 
}
```
> Основные службы находятся на верхнем уровне дерева GATT. Основные службы содержат характеристики и другие службы ("включенные" или дополнительные). 

Задайте обязательные характеристики и дескрипторы для службы:

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
Здесь также удобно объявлять обработчики событий для операций, поддерживаемых каждой характеристикой (см. рис. выше).  Чтобы приложение правильно реагировало на запросы, необходимо определить и настроить обработчик событий для каждого типа запроса, поддерживаемого атрибутом.  Если обработчик не зарегистрирован, система мгновенно выполняет запрос с ошибкой *UnlikelyError*.

### <a name="constant-characteristics"></a>Постоянные характеристики
Некоторые значения характеристик не меняются в течение всего жизненного цикла приложения. В этом случае рекомендуется объявить постоянную характеристику во избежание нежелательной активации приложения: 

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
После определения всех атрибутов службы необходимо опубликовать поддержку для этой службы. Публикация информирует операционную систему о том, что эта служба должна возвращаться, когда удаленные устройства выполняют поиск служб.  Необходимо настроить два свойства: IsDiscoverable и IsConnectable.  

```csharp
GattServiceProviderAdvertisingParameters advParameters = new GattServiceProviderAdvertisingParameters
{
    IsDiscoverable = true,
    IsConnectable = true
};
serviceProvider.StartAdvertising(advParameters);
```
- **IsDiscoverable**: Объявляет понятное имя для удаленных устройств в объявлении, за которой устройство обнаружения.
- **IsConnectable**:  Объявляет объявление, доступный для соединения для использования в периферийных роли.

> Когда для службы заданы оба свойства, Discoverable и Connectable, система добавляет UUID этой службы в пакет объявления.  Размер пакета объявления составляет всего 31 байт, и 16 из них занимает 128-битный UUID.

> Примечание: когда служба публикуется на переднем плане и работа приложения приостанавливается, приложение должно вызвать функцию StopAdvertising.

## <a name="respond-to-read-and-write-requests"></a>Реагирование на запросы чтения и записи
Как упоминалось в разделе об объявлении требуемых характеристик, атрибут GattLocalCharacteristics поддерживает 3 типа событий: ReadRequested, WriteRequested и SubscribedClientsChanged.

### <a name="read"></a>Read
Когда удаленное устройство пытается прочитать значение характеристики и это значение не является постоянным, вызывается событие ReadRequested. Характеристика, для которой была вызвана операция чтения, а также аргументы, содержащие сведения об удаленном устройстве, передаются делегату: 

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
Когда удаленное устройство пытается записать значение в характеристику, вызывается событие WriteRequested с подробными сведениями об удаленном устройстве, соответствующей характеристике и самом значении: 

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
Существует 2 типа операций записи: с ответом и без ответа. Чтобы определить, какую операцию записи выполняет удаленное устройство, используйте свойство GattWriteOption в объекте GattWriteRequest. 

## <a name="send-notifications-to-subscribed-clients"></a>Отправка уведомлений подписанным клиентам
Отправка уведомлений — наиболее часто выполняемая операция сервера GATT. Уведомления выполняют важную функцию публикации данных на удаленных устройствах. В зависимости от конкретной ситуации можно отправлять уведомления всем подписанным клиентам или самостоятельно выбирать устройства для отправки нового значения: 

```csharp
async void NotifyValue()
{
    var writer = new DataWriter();
    // Populate writer with data
    // ...
    
    await notifyCharacteristic.NotifyValueAsync(writer.DetachBuffer());
}
```

Когда новое устройство подписывается на уведомления, вызывается событие SubscribedClientsChanged: 

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
> Примечание: приложение может определять максимальный размер уведомления для конкретных клиентов с помощью свойства MaxNotificationSize.  Система применяет усечение к любым данным, размер которых превышает максимальное значение.
