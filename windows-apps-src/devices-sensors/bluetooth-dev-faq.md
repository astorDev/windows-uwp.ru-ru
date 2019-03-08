---
title: Вопросы и ответы для Bluetooth-разработчиков
description: Эта статья содержит ответы на часто задаваемые вопросы, связанные с API-интерфейсами UWP для работы с Bluetooth.
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.assetid: e7dee32d-3756-430d-a026-32c1ee288a85
ms.localizationpriority: medium
ms.openlocfilehash: 4cc1bafb90b20083d55a622873dea7be5efbf5b7
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57633489"
---
# <a name="bluetooth-developer-faq"></a>Вопросы и ответы для Bluetooth-разработчиков

Эта статья содержит ответы на часто задаваемые вопросы об API-интерфейсах UWP для работы с Bluetooth.

## <a name="what-apis-do-i-use-bluetooth-classic-rfcomm-or-bluetooth-low-energy-gatt"></a>Какие интерфейсы API следует использовать? Bluetooth Classic (RFCOMM) или Bluetooth с низким энергопотреблением (GATT)?
В сети существуют различные обсуждения об этой общей теме, поэтому сосредоточим этот ответ исключительно на разнице относительно Windows. Вот некоторые общие рекомендации.

### <a name="bluetooth-le-windowsdevicesbluetoothgenericattributeprofile"></a>Bluetooth LE (Windows.Devices.Bluetooth.GenericAttributeProfile)

Используйте интерфейсы API GATT при подключении к устройству, которое поддерживает Bluetooth с низким энергопотреблением. Если ваш вариант использования будет нечастым, с низкой пропускной способностью или будет требовать низкое энергопотребление, Bluetooth с низким энергопотреблением является подходящим вариантом. Основным пространством имен, которое содержит эту функцию, является [Windows.Devices.Bluetooth.GenericAttributeProfile](https://docs.microsoft.com/en-us/uwp/api/Windows.Devices.Bluetooth.GenericAttributeProfile). 

**Когда не следует использовать Bluetooth с НИЗКИМ**
- Сценарии с высокой пропускной способностью и высокой частотой Если нужно постоянно синхронизировать большие объемы данных, лучше использовать классический Bluetooth или даже Wi-Fi. 

### <a name="bluetooth-classic-windowsdevicesbluetoothrfcomm"></a>Bluetooth Classic (Windows.Devices.Bluetooth.Rfcomm)

Интерфейсы API RFCOMM предоставляют разработчикам сокет для выполнения двусторонней связи через последовательный порт. После получения сокета методы записи на него и чтения с него являются довольно стандартными. Реализация этого представлена в [примере чата Rfcomm](https://github.com/Microsoft/Windows-universal-samples/tree/dev/Samples/BluetoothRfcommChat). 

**Когда не следует использовать Bluetooth Rfcomm** 
- Уведомления. Протокол Bluetooth GATT имеет определенную команду для этого и приведет к значительно меньшему потреблению энергии и сократит время ответа. 
- Проверка близости или определение наличия. Лучше использовать [Интерфейсы API для рекламных объявлений](https://docs.microsoft.com/en-us/uwp/api/windows.devices.bluetooth.advertisement) и подключиться через Bluetooth с низким энергопотреблением. 


## <a name="why-does-my-bluetooth-le-device-stop-responding-after-a-disconnect"></a>Почему мое устройство Bluetooth LE перестает отвечать после отключения?

Чаще всего это происходит, потому что удаленное устройство потеряло сведения о связывании. Множество устройств Bluetooth ранних моделей не требуют проверки подлинности. Чтобы защитить пользователя, для всех процессов связывания, которые выполняются в приложении "Параметры", требуется проверка подлинности, а некоторые устройства не поддерживают этот процесс. 

Начиная с выпуска 1511 Windows 10 разработчики могут управлять процессом связывания. В [примере перечисления и связывания устройств](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/DeviceEnumerationAndPairing) описываются различные аспекты связывания новых устройств.

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

Это не требуется для классических устройств Bluetooth RFCOMM. Начиная с выпуска 1607 Windows 10 вы можете просто запросить ближайшие устройства и подключиться к ним. Эта функция показана в обновленном [примере чата RFCOMM](https://github.com/Microsoft/Windows-universal-samples/tree/dev/Samples/BluetoothRfcommChat). 

**(14393 и более ранние версии)** Эта функция недоступна для устройств Bluetooth Low Energy (клиент GATT), поэтому вам по-прежнему потребуется связать устройство на странице параметров или с помощью API-интерфейсов [Windows.Devices.Enumeration](https://msdn.microsoft.com/en-us/library/windows/apps/windows.devices.enumeration.aspx), чтобы получить доступ к ним.

**(15030 и более поздние версии)** Связывание устройств Bluetooth больше не требуется. Используйте новые API Async, такие как GetGattServicesAsync и GetCharacteristicsAsync, для запроса текущего состояния удаленного устройства. Дополнительные сведения см. в [документации клиента](gatt-client.md). 

## <a name="when-should-i-pair-with-a-device-before-communicating-with-it"></a>Когда следует связаться с устройством перед взаимодействием с ним?
Как правило, если требуется надежная и долгосрочная связь с устройством, свяжитесь с ним (направив пользователя на страницу параметров или используя перечисление устройств и API-интерфейсы связывания). Если вам просто нужно прочитать сведения устройство, которое является общедоступным (датчик температуры или предупреждения), а затем подключиться или прослушивать объявления, не внося никаких действий, чтобы связать с устройством. Это позволит избежать проблем взаимодействия в долгосрочной перспективе, поскольку узел устройств не поддерживает связывание. 

## <a name="do-all-windows-devices-support-peripheral-role"></a>Все ли устройства Windows поддерживают периферийную роль?

Нет, эта функция зависит от оборудования, но предоставляется метод (BluetoothAdapter.IsPeripheralRoleSupported) для запроса, поддерживается ли он или нет.  К устройствам, поддерживающимся в настоящее время, относятся Windows Phone на 8992+ и RPi3 (Windows IoT). 

## <a name="can-i-access-these-apis-from-win32"></a>Можно ли получить доступ к этим API с Win32?

Да, все эти API должны работать. В этой публикации блога описан способ вызова [API Windows из приложений для настольных компьютеров](https://blogs.windows.com/buildingapps/2017/01/25/calling-windows-10-apis-desktop-application/). 
## <a name="is-this-functionality-supposed-to-exist-on--insert-sku-here-"></a>Должна ли поддерживаться эта функция на *-Вставить здесь SKU-*?

**Bluetooth LE**: Да, все функции в OneCore и должен быть доступен на самой последней устройств с работает Bluetooth с НИЗКИМ стека. 
> Одно предостережение. Периферийные роль зависит от аппаратного обеспечения и некоторые выпуски Windows Server не поддерживают Bluetooth. 

**Bluetooth (Классическая модель) BR/EDR**: Существует несколько вариантов, но в целом, они имеют очень похожа поддержка уровня профиля. См. документацию по [RFCOMM](send-or-receive-files-with-rfcomm.md) и документацию по этим поддерживаемым профилям для [ПК](https://support.microsoft.com/en-us/help/10568/windows-10-supported-bluetooth-profiles) и [телефона](https://support.microsoft.com/en-us/help/10569/windows-10-mobile-supported-bluetooth-profiles)

