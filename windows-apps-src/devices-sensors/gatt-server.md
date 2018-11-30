---
title: Сервер GATT Bluetooth
description: В этой статье представлен обзор Bluetooth Generic Attribute Profile (GATT) сервера для приложений универсальной платформы Windows (UWP), а также пример кода для наиболее распространенных случаев использования.
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 551f8b925ffd56950ba893da7b81fefb4579f558
ms.sourcegitcommit: 89ff8ff88ef58f4fe6d3b1368fe94f62e59118ad
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/30/2018
ms.locfileid: "8214524"
---
# <a name="bluetooth-gatt-server"></a>Сервер GATT Bluetooth


**Важные API**
- [**Windows.Devices.Bluetooth**](https://msdn.microsoft.com/library/windows/apps/Dn263413)
- [**Windows.Devices.Bluetooth.GenericAttributeProfile**](https://msdn.microsoft.com/library/windows/apps/Dn297685)


В этой статье показаны API сервера Bluetooth Generic Attribute (GATT) для приложений универсальной платформы Windows (UWP), а также пример кода для типичных задач сервер GATT: 
- Определение поддерживаемых служб
- Опубликуйте сервер, поэтому могут быть обнаружены удаленными клиентами
- Объявление поддержки для службы
- Ответ на запросы чтения и записи
- Отправлять уведомления подписанный клиентов

## <a name="overview"></a>Обзор
Windows обычно выполняется в роли клиента. Тем не менее многие сценарии при одновременном которых требуется Windows в качестве Bluetooth с низким ЭНЕРГОПОТРЕБЛЕНИЕМ сервер GATT также. Почти все сценарии для устройств IoT, вместе с большинства обмен данными между различными платформами BLE потребуется для использования в сервер GATT. Кроме того отправку уведомления в ближайшие устройства носимое стала популярных сценария, требующего эту технологию также.  
> Убедитесь, что все основные понятия в [документации клиент GATT](gatt-client.md) отсутствуют перед продолжением работы.  

Операций сервера будет связана с поставщика услуг и GattLocalCharacteristic. Эти два класса предоставляет функции, необходимые для объявления, реализации и предоставления иерархии данных на удаленном устройстве.

## <a name="define-the-supported-services"></a>Определение поддерживаемых служб
Приложение может объявлять одного или нескольких служб, которые будут опубликованы операционной системой Windows. Каждая служба однозначно определяется UUID. 

### <a name="attributes-and-uuids"></a>Атрибуты и UUID
Каждый службы, характеристик и дескрипторов определяется по это собственные уникальные UUID 128-разрядные.
> API-интерфейсы Windows все используем термин GUID, но стандарта Bluetooth определяет, такие как UUID. В нашем случае эти два условия взаимозаменяемы, поэтому мы будем продолжать использовать термин UUID. 

Если атрибут является стандартным и по Bluetooth SIG определенные определению, он также будут иметь соответствующий короткий идентификатор 16-разрядные (например UUID уровня заряда батареи является 0000**2A19**-0000-1000-8000-00805F9B34FB и коротких идентификатор — это 0x2A19). Эти стандартные UUID можно увидеть в [GattServiceUuids](https://msdn.microsoft.com/en-us/library/windows/apps/windows.devices.bluetooth.genericattributeprofile.gattserviceuuids.aspx) и [GattCharacteristicUuids](https://msdn.microsoft.com/en-us/library/windows/apps/windows.devices.bluetooth.genericattributeprofile.gattcharacteristicuuids.aspx).

При реализации приложения его собственной пользовательской службы, пользовательские UUID имеет создается. Это легко в Visual Studio с помощью средства "->" CreateGuid (используйте вариант 5, чтобы получить его в формате «xxxxxxxx-xxxx … xxxx»). Это uuid теперь может использоваться для объявления новые локальные службы, характеристик и дескрипторов.

#### <a name="restricted-services"></a>Ограниченная служб
Следующие службы зарезервированный системой и не может быть опубликована в данный момент.
1. Сведения о службе устройства (DIS)
2. Службы универсальный атрибут Profile (GATT)
3. Служба профилей универсальный доступ (пробел)
4. Служба устройств пользовательского интерфейса (HOGP)
5. Проверка параметров службы (SCP)

> Попытка создать заблокированных службы приведет к BluetoothError.DisabledByPolicy, возвращаемого из вызова CreateAsync.

#### <a name="generated-attributes"></a>Созданный атрибуты
Следующие дескрипторы формируются системой, в зависимости от GattLocalCharacteristicParameters, предоставленных во время создания характеристика:
1. Настройка характеристик клиентской части (Если характеристика помечена как indicatable или notifiable).
2. Форматировать описание пользователя (если задается свойство UserDescription). Свойству GattLocalCharacteristicParameters.UserDescription Дополнительные сведения см.
3. Форматировать формат (один дескриптор для каждого указанного формата презентации).  Свойству GattLocalCharacteristicParameters.PresentationFormats Дополнительные сведения см.
4. Форматировать сводных формат (Если указано несколько формат представления).  Свойство GattLocalCharacteristicParameters.See PresentationFormats подробнее.
5. Форматировать расширенных свойств (Если характеристика помечен с битом расширенных свойств).

> Значение дескриптора расширенные свойства определяется через свойства ReliableWrites и WritableAuxiliaries характеристик.

> Попытка создать зарезервированные дескриптор приведет к вызову исключения.

> Обратите внимание, что трансляции в данный момент не поддерживается.  Указав GattCharacteristicProperty рассылка приведет к вызову исключения.

### <a name="build-up-the-hierarchy-of-services-and-characteristics"></a>Создания иерархии службах и характеристиках
GattServiceProvider используется для создания и объявление определение основного службы корневой.  Каждая служба требует, чтобы это собственный поставщик службы объекта, который принимает в GUID: 

```csharp
GattServiceProviderResult result = await GattServiceProvider.CreateAsync(uuid);

if (result.Error == BluetoothError.Success)
{
    serviceProvider = result.ServiceProvider;
    // 
}
```
> Основные службы — на верхнем уровне дерева GATT. Основные службы содержать характеристики, а также другие службы (под названием «Включено» или вспомогательных служб). 

Теперь заполните службу с обязательным характеристик и дескрипторов:

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
Как показано выше, это также удобно размещать объявляем обработчики событий для операций, которые каждой из которых поддерживает.  Правильно реагировать на запросы, приложение должно определяются и задать обработчик событий для каждого типа запроса, атрибут поддерживает.  Несовпадение зарегистрировать обработчик приведет к запрос немедленно завершить с *UnlikelyError* системой.

### <a name="constant-characteristics"></a>Характеристики констант
В некоторых случаях существует форматировать значения, которые не изменится в ходе выполнения времени существования приложения. В этом случае рекомендуется объявлять констант характеристик для предотвращения активацию ненужные приложения: 

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
После полной определения службы следующий шаг состоит в публикации поддержку для службы. Таким образом операционной системе, что служба должны быть возвращены при выполнении службы обнаружения удаленных устройств.  Вам придется установить два свойства - IsDiscoverable и IsConnectable:  

```csharp
GattServiceProviderAdvertisingParameters advParameters = new GattServiceProviderAdvertisingParameters
{
    IsDiscoverable = true,
    IsConnectable = true
};
serviceProvider.StartAdvertising(advParameters);
```
- **IsDiscoverable**: объявляет понятное имя к удаленным устройствам в рекламном блоке, что устройство было найти.
- **IsConnectable**: объявляет подключаемых объявления для использования в Периферийная роль.

> При обнаруживаемый и Connectable службы система добавит Uuid службы в пакет объявления.  Существует только 31 байт в пакете объявления и 128-разрядные UUID занимает 16 из них!

> Обратите внимание, что если служба публикуется на переднем плане, приложение должно вызвать StopAdvertising когда приостанавливает работу приложения.

## <a name="respond-to-read-and-write-requests"></a>Ответ на запросы чтения и записи
Как мы показали выше, а при объявлении необходимые характеристики, GattLocalCharacteristics имеются три типа события - ReadRequested, WriteRequested и SubscribedClientsChanged.

### <a name="read"></a>Read
Когда удаленное устройство пытается прочитать значение из характеристикой (и не постоянное значение), вызывается событие ReadRequested. Характеристика чтения был вызван на, а также аргументы (содержащего сведения об удаленном устройстве) передается делегат: 

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
При попытке записать значение характеристикой удаленного устройства, WriteRequested событие вызывается с информацией об удаленном устройстве, какие характеристик для записи и само значение: 

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
Существует два типа операций записи - с и без ответа. Используйте GattWriteOption (свойство объекта GattWriteRequest), чтобы выяснить, какой тип записи выполняет удаленного устройства. 

## <a name="send-notifications-to-subscribed-clients"></a>Отправлять уведомления подписанный клиентов
Наиболее распространенные операции сервер GATT уведомления выполнять важные функции передача данных к удаленным устройствам. В некоторых случаях необходимо уведомить все подписанный клиенты, но othertimes, может потребоваться выбрать устройств, которые должны отправить новое значение: 

```csharp
async void NotifyValue()
{
    var writer = new DataWriter();
    // Populate writer with data
    // ...
    
    await notifyCharacteristic.NotifyValueAsync(writer.DetachBuffer());
}
```

Когда новое устройство подписывается для уведомлений, вызывается событие SubscribedClientsChanged: 

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
> Обратите внимание, что приложение можно получить размер максимальное уведомлений для конкретного клиента с помощью свойства MaxNotificationSize.  Любые данные, размер которых превышает максимальный размер будут усечены системой.
