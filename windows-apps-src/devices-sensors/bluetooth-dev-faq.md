---
title: Вопросы и ответы для Bluetooth-разработчиков
description: Эта статья содержит ответы на часто задаваемые вопросы, связанные с API-интерфейсами UWP для работы с Bluetooth.
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.assetid: e7dee32d-3756-430d-a026-32c1ee288a85
ms.localizationpriority: medium
ms.openlocfilehash: 03b72b5722a3ece0165fc63e7ce4abc1238bc135
ms.sourcegitcommit: b4c502d69a13340f6e3c887aa3c26ef2aeee9cee
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/03/2018
ms.locfileid: "8467787"
---
# <a name="bluetooth-developer-faq"></a>Вопросы и ответы для Bluetooth-разработчиков

Эта статья содержит ответы на часто задаваемые вопросы об API-интерфейсах UWP для работы с Bluetooth.

## <a name="what-apis-do-i-use-bluetooth-classic-rfcomm-or-bluetooth-low-energy-gatt"></a>Какие API-интерфейсы использовать? Классический Bluetooth (RFCOMM) или Bluetooth с низким энергопотреблением (GATT)?
Существуют различные обсуждения в Интернете вокруг подраздел общие давайте каким оставьте этот ответ о различиях относительно Windows. Ниже приведены некоторые общие рекомендации.

### <a name="bluetooth-le-windowsdevicesbluetoothgenericattributeprofile"></a>Bluetooth с низким ЭНЕРГОПОТРЕБЛЕНИЕМ (Windows.Devices.Bluetooth.GenericAttributeProfile)

Используйте API-интерфейсы GATT, когда при подключении устройства с поддержкой Bluetooth с низким энергопотреблением. Если вы будете использовать вариант — нечастых, низкой пропускной способности или требуется пониженного энергопотребления, Bluetooth с низким энергопотреблением, представляет собой ответ. Главное пространство имен, включающий этой функциональности — [Windows.Devices.Bluetooth.GenericAttributeProfile](https://docs.microsoft.com/en-us/uwp/api/Windows.Devices.Bluetooth.GenericAttributeProfile). 

**Когда не следует использовать Bluetooth с низким ЭНЕРГОПОТРЕБЛЕНИЕМ**
- Высокой пропускной способностью, высокой частотой сценариев. Если вам нужно постоянно поддерживать синхронизацию с большими объемами данных, рассмотрите возможность использования Bluetooth классический или возможно даже Wi-Fi. 

### <a name="bluetooth-classic-windowsdevicesbluetoothrfcomm"></a>Классический Bluetooth (Windows.Devices.Bluetooth.Rfcomm)

В API RFCOMM предоставить разработчикам сокет для выполнения связи стиль, двусторонняя последовательный порт. После того, как сокет, запись и чтение из него методам довольно standard. Реализация этого представлена в [образце чата Rfcomm](https://github.com/Microsoft/Windows-universal-samples/tree/dev/Samples/BluetoothRfcommChat). 

**Когда не следует использовать Bluetooth Rfcomm** 
- Уведомления. Протокол Bluetooth GATT имеет определенную команду для этого и приведет к значительно меньше рисования питания и сократить время ожидания ответа. 
- Проверка близости или наличие обнаружения. Лучше использовать [API -интерфейсы рекламы](https://docs.microsoft.com/en-us/uwp/api/windows.devices.bluetooth.advertisement) и подключение через Bluetooth с низким ЭНЕРГОПОТРЕБЛЕНИЕМ. 


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

**(14393 и ниже)** Эта функция не доступны для Bluetooth с низким энергопотреблением (клиент GATT), поэтому вы по-прежнему потребуется связать посредством на странице параметров или с помощью [Windows.Devices.Enumeration](https://msdn.microsoft.com/en-us/library/windows/apps/windows.devices.enumeration.aspx) API-интерфейсов в порядке доступа этих устройств.

**(15030 и выше)** Связывание устройств Bluetooth больше не требуется. Использование новых асинхронных API, таких как GetGattServicesAsync и GetCharacteristicsAsync для запроса текущее состояние удаленного устройства. См. [документы клиента](gatt-client.md) для получения дополнительных сведений. 

## <a name="when-should-i-pair-with-a-device-before-communicating-with-it"></a>Когда следует ли связывания с устройством перед связи с его помощью?
Как правило если вам требуется доверенным, долгосрочной bond с устройством, свяжите с ним (направление пользователя на странице параметров или с помощью перечисления устройств и связывание API). Если нужно просто читать данные отключать устройства, предоставляется общедоступной (датчик температуры или маяка), подключения или приема оповещений без все усилия, чтобы связать с устройством. Это позволит избежать проблемы взаимодействия в долгосрочной перспективе, поскольку узел устройств не поддерживают связывание. 

## <a name="do-all-windows-devices-support-peripheral-role"></a>Все устройства с Windows поддерживают Периферийная роль?

Нет – это зависимые компоненты оборудования, но метод предоставляются (BluetoothAdapter.IsPeripheralRoleSupported) для запроса ли он поддерживается или нет.  Поддерживаемые устройства включают Windows Phone на 8992 + и RPi3 (Windows IoT). 

## <a name="can-i-access-these-apis-from-win32"></a>Получить доступ к этим API из Win32?

Да, все эти API-интерфейсы должны работать. Этом блоге подробное описание способ вызова [API Windows из классических приложений](https://blogs.windows.com/buildingapps/2017/01/25/calling-windows-10-apis-desktop-application/). 
## <a name="is-this-functionality-supposed-to-exist-on--insert-sku-here-"></a>Эта функция должна существует на *- Здесь вставить SKU -*?

**Bluetooth с низким ЭНЕРГОПОТРЕБЛЕНИЕМ**: Да, все функции в OneCore и должен быть доступен на последний устройств с функционирующего стека Bluetooth с низким ЭНЕРГОПОТРЕБЛЕНИЕМ. 
> Периферийная роль оговоркой: — Это зависит от аппаратного обеспечения и некоторые выпуски Windows Server не поддерживают Bluetooth. 

**Bluetooth BR/EDR (классические)**: существует несколько вариантов, но в целом, они имеют очень похожий поддержки уровней профиля. См. документы [RFCOMM](send-or-receive-files-with-rfcomm.md) и эти документы поддерживаемого профиля для [ПК](https://support.microsoft.com/en-us/help/10568/windows-10-supported-bluetooth-profiles) и [телефоне](https://support.microsoft.com/en-us/help/10569/windows-10-mobile-supported-bluetooth-profiles)

