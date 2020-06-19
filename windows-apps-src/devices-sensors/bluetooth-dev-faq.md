---
title: Вопросы и ответы для Bluetooth-разработчиков
description: Эта статья содержит ответы на часто задаваемые вопросы, связанные с API-интерфейсами UWP для работы с Bluetooth.
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.assetid: e7dee32d-3756-430d-a026-32c1ee288a85
ms.localizationpriority: medium
ms.openlocfilehash: 7ff826be0f5b0b8e9a6723fbb1593663f1748c3d
ms.sourcegitcommit: d708ac4ec4fac0135dafc0d8c5161ef9fd945ce7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/18/2020
ms.locfileid: "85069476"
---
# <a name="bluetooth-developer-faq"></a>Вопросы и ответы для Bluetooth-разработчиков

Эта статья содержит ответы на часто задаваемые вопросы об API-интерфейсах UWP для работы с Bluetooth.

## <a name="what-apis-do-i-use-bluetooth-classic-rfcomm-or-bluetooth-low-energy-gatt"></a>Какие интерфейсы API следует использовать? Bluetooth Classic (RFCOMM) или Bluetooth с низким энергопотреблением (GATT)?
В сети существуют различные обсуждения об этой общей теме, поэтому сосредоточим этот ответ исключительно на разнице относительно Windows. Ниже приведены некоторые общие рекомендации.

### <a name="bluetooth-le-windowsdevicesbluetoothgenericattributeprofile"></a>Bluetooth LE (Windows.Devices.Bluetooth.GenericAttributeProfile)

Используйте интерфейсы API GATT при подключении к устройству, которое поддерживает Bluetooth с низким энергопотреблением. Если ваш вариант использования является нечастой, низкой пропускной способностью или требует низкого энергопотребления, то ответом Bluetooth является низкий уровень энергии. Основным пространством имен, которое содержит эту функцию, является [Windows.Devices.Bluetooth.GenericAttributeProfile](https://docs.microsoft.com/uwp/api/Windows.Devices.Bluetooth.GenericAttributeProfile). 

**Когда не следует использовать Bluetooth с низким энергопотреблением**
- Сценарии с высокой пропускной способностью и высокой частотой Если нужно постоянно синхронизировать большие объемы данных, лучше использовать классический Bluetooth или даже Wi-Fi. 

### <a name="bluetooth-classic-windowsdevicesbluetoothrfcomm"></a>Bluetooth Classic (Windows.Devices.Bluetooth.Rfcomm)

Интерфейсы API RFCOMM дают разработчикам возможность выполнять двунаправленную связь в стиле последовательного порта. Когда у вас есть сокет, методы для записи и чтения являются довольно стандартными. Реализация этого представлена в [примере чата Rfcomm](https://github.com/Microsoft/Windows-universal-samples/tree/dev/Samples/BluetoothRfcommChat). 

**Когда не следует использовать Bluetooth Rfcomm** 
- Уведомления. Протокол Bluetooth GATT имеет определенную команду для этого и приведет к значительно меньшему потреблению энергии и сократит время ответа. 
- Проверка близости или определение наличия. Лучше использовать [Интерфейсы API для рекламных объявлений](https://docs.microsoft.com/uwp/api/windows.devices.bluetooth.advertisement) и подключиться через Bluetooth с низким энергопотреблением. 


## <a name="why-does-my-bluetooth-le-device-stop-responding-after-a-disconnect"></a>Почему мое устройство Bluetooth LE перестает отвечать после отключения?

Чаще всего это происходит из-за того, что удаленное устройство потеряло сведения о парах. Для большого числа старых устройств Bluetooth не требуется проверка подлинности. Для защиты пользователя все связанные транзакции, выполняемые из приложения параметров, будут требовать проверки подлинности, и некоторые устройства не были спроектированы с учетом этого. 

Начиная с выпуска Windows 10 1511 разработчики могут управлять подтверждением связывания. В [примере перечисления и связывания устройств](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/DeviceEnumerationAndPairing) описываются различные аспекты связывания новых устройств.

В этом примере мы начинаем связывание с устройством без шифрования. Обратите внимание, что это сработает, только если удаленное устройство не запрашивает шифрование или проверку подлинности.

```csharp
// Get ceremony type and protection level selections
// You must select at least ConfirmOnly or the pairing attempt will fail
    DevicePairingKinds ceremonySelected = DevicePairingKinds.ConfirmOnly;

//  Workaround remote devices losing pairing information
    DevicePairingProtectionLevel protectionLevel = DevicePairingProtectionLevel.None

    DeviceInformationCustomPairing customPairing = deviceInfoDisp.DeviceInformation.Pairing.Custom;

// Declare an event handler - you don't need to do much in PairingRequestedHandler since the ceremony is "None"
    customPairing.PairingRequested += PairingRequestedHandler;
    DevicePairingResult result = await customPairing.PairAsync(ceremonySelected, protectionLevel);
```

## <a name="do-i-have-to-pair-bluetooth-devices-before-using-them"></a>Требуется ли привязать устройства Bluetooth для их использования?

Вам не нужно связывать устройства, прежде чем использовать их, если вы используете Bluetooth RFCOMM (классическая модель). Начиная с выпуска 1607 Windows 10 вы можете просто запросить ближайшие устройства и подключиться к ним. Эта функция показана в обновленном [примере чата RFCOMM](https://github.com/Microsoft/Windows-universal-samples/tree/dev/Samples/BluetoothRfcommChat). 

**(14393 и ниже)** Эта функция недоступна для Bluetooth с низким энергопотреблением (GATT Client), поэтому для доступа к этим устройствам по-прежнему придется связываться с помощью страницы параметров или с помощью API-интерфейсов [Windows. Devices. Enumeration.](https://docs.microsoft.com/uwp/api/windows.devices.enumeration)

**(15030 и более поздние версии)** Связывание устройств Bluetooth больше не требуется. Используйте новые API Async, такие как GetGattServicesAsync и GetCharacteristicsAsync, для запроса текущего состояния удаленного устройства. Дополнительные сведения см. в [документации клиента](gatt-client.md). 

## <a name="when-should-i-pair-with-a-device-before-communicating-with-it"></a>Когда следует связаться с устройством перед взаимодействием с ним?
Как правило, если вам требуется надежная, долгосрочная облигации с устройством, свяжите с ним или направьте пользователя на страницу параметров или используйте перечисление устройств и попарные API-интерфейсы. Если вам нужно просто прочитать информацию с устройства, доступного для общего доступа (датчик температуры или маяк), подключитесь к рекламным данным или прослушайте рекламу, не прибегая к работе с устройством. Это предотвратит проблемы взаимодействия в долгосрочном запуске, так как большое число устройств не поддерживает связывание. 

## <a name="do-all-windows-devices-support-peripheral-role"></a>Все ли устройства Windows поддерживают периферийную роль?

Нет. Это аппаратно зависимая функция, но для запроса того, поддерживается ли он, Блуетусадаптер. Исперифералролесуппортед предоставляет метод.  К устройствам, поддерживающимся в настоящее время, относятся Windows Phone на 8992+ и RPi3 (Windows IoT). 

## <a name="can-i-access-these-apis-from-win32"></a>Можно ли получить доступ к этим API с Win32?

Да, все эти API должны работать. В этой публикации блога описан способ вызова [API Windows из приложений для настольных компьютеров](https://blogs.windows.com/buildingapps/2017/01/25/calling-windows-10-apis-desktop-application/). 
## <a name="is-this-functionality-supposed-to-exist-on--insert-sku-here-"></a>Должна ли поддерживаться эта функция на *-Вставить здесь SKU-*?

**Bluetooth с низким энергопотреблением**: да, все функции есть в OneCore и должны быть доступны на последних устройствах со стеком Bluetooth с низким энергопотреблением. 
> Предостережение. роль периферийных устройств зависит от оборудования, а некоторые выпуски Windows Server не поддерживают Bluetooth. 

**Bluetooth BR/ЕДР (классическая модель)**. Существует несколько вариантов, но в основном они имеют очень похожую поддержку на уровне профиля. См. документацию по [RFCOMM](send-or-receive-files-with-rfcomm.md) и поддерживаемые документы профиля для [ПК](https://support.microsoft.com/help/10568/windows-10-supported-bluetooth-profiles) и [телефона](https://support.microsoft.com/help/10569/windows-10-mobile-supported-bluetooth-profiles)
