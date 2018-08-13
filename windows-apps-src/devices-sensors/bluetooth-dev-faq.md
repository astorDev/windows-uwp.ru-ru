---
author: msatranjr
title: Вопросы и ответы для Bluetooth-разработчиков
description: Эта статья содержит ответы на часто задаваемые вопросы, связанные с API-интерфейсами UWP для работы с Bluetooth.
ms.author: misatran
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp
ms.assetid: e7dee32d-3756-430d-a026-32c1ee288a85
ms.localizationpriority: medium
ms.openlocfilehash: c0af6a19e17a62ed82c32e68ea1732e1f51d4641
ms.sourcegitcommit: 897a111e8fc5d38d483800288ad01c523e924ef4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/13/2018
ms.locfileid: "301928"
---
# <a name="bluetooth-developer-faq"></a>Вопросы и ответы для Bluetooth-разработчиков

Эта статья содержит ответы на часто задаваемые вопросы об API-интерфейсах UWP для работы с Bluetooth.

## <a name="what-apis-do-i-use-bluetooth-classic-rfcomm-or-bluetooth-low-energy-gatt"></a>Какие API-интерфейсы следует использовать? Классический Bluetooth (RFCOMM) или Bluetooth Low электроэнергии (GATT)?
Существуют различные обсуждение online в этом разделе Общие давайте непосредственно сохранить этот ответ на разницы относительно Windows. Ниже приведены некоторые общие рекомендации:

### <a name="bluetooth-le-windowsdevicesbluetoothgenericattributeprofile"></a>Bluetooth LE (Windows.Devices.Bluetooth.GenericAttributeProfile)

Используйте API GATT, когда вы общаетесь устройств, поддерживающих Bluetooth Low электроэнергии. Если вы используете case является относительно редкие процедуры объединения, низкой пропускной способности или требуется энергосбережения, электроэнергии Low Bluetooth — это ответ. Основные пространства имен, который включает в себя эту функцию — [Windows.Devices.Bluetooth.GenericAttributeProfile](https://docs.microsoft.com/en-us/uwp/api/Windows.Devices.Bluetooth.GenericAttributeProfile). 

**Когда не следует использовать Bluetooth LE**
- Высокой пропускной способностью, высокая частота сценариев. Если вам потребуется постоянно хранить синхронизации с большими объемами данных, рекомендуется использовать классический или может быть даже WiFi Bluetooth. 

### <a name="bluetooth-classic-windowsdevicesbluetoothrfcomm"></a>Классический Bluetooth (Windows.Devices.Bluetooth.Rfcomm)

API-интерфейсы RFCOMM предоставить разработчикам сокетов для выполнения связи стиль последовательный порт направление бизнес-аналитики. После того, как разъем, методов записи и чтении из нее в значительной степени стандартизированы. Реализация этого представлены в [Пример Rfcomm разговора](https://github.com/Microsoft/Windows-universal-samples/tree/dev/Samples/BluetoothRfcommChat). 

**Когда не следует использовать Bluetooth Rfcomm** 
- Уведомления. Протокол Bluetooth GATT имеет базы данных для определенной команде и приведет к значительно меньше потребления энергии и сократить время ожидания ответа. 
- Проверка обнаружения близости или сведения о присутствии. Рекомендуется использовать [Интерфейсы API объявления](https://docs.microsoft.com/en-us/uwp/api/windows.devices.bluetooth.advertisement) и подключения через Bluetooth LE. 


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

**(14393 и ниже)** Этот компонент недоступен для электроэнергии Bluetooth Low (GATT клиента), поэтому будут по-прежнему необходимо соединить с помощью страницы параметров или с помощью [Windows.Devices.Enumeration](https://msdn.microsoft.com/en-us/library/windows/apps/windows.devices.enumeration.aspx) API-интерфейсы в порядке доступа этих устройств.

**(15030 и выше)** Связывания устройства Bluetooth больше не требуется. Использование новых API Async, например GetGattServicesAsync и GetCharacteristicsAsync для запроса текущего состояния удаленных устройств. В разделе [документы клиента](gatt-client.md) для получения дополнительных сведений. 

## <a name="when-should-i-pair-with-a-device-before-communicating-with-it"></a>Когда следует ли пары с устройством затем связаться с ним?
Как правило если требуется надежных, долгосрочного связь с устройством пары с ним (направление пользователя на странице "Параметры" или с помощью перечисления устройства и связывания API). Если нужно просто прочитать сведения устройства, предоставляемые всем (датчика температуры или маяков), подключение или приема оповещений без внесения все возможное, чтобы связать с устройством. Это предотвратит проблемы взаимодействия в долгосрочной перспективе узла устройств не поддерживают связывания. 

## <a name="do-all-windows-devices-support-peripheral-role"></a>Все устройства Windows поддерживают периферийный роли?

Нет — это зависимый компонент оборудования, но метод предоставляются (BluetoothAdapter.IsPeripheralRoleSupported) для запроса ли он поддерживается или нет.  Поддерживаемые устройства включают Windows Phone на 8992 + и RPi3 (Windows IoT). 

## <a name="can-i-access-these-apis-from-win32"></a>Можно ли эти API-интерфейсы доступа из Win32?

Да, должны работать эти интерфейсы API. В этом блоге подробно описывает способ вызова [API Windows из приложения для настольных компьютеров](https://blogs.windows.com/buildingapps/2017/01/25/calling-windows-10-apis-desktop-application/). 
## <a name="is-this-functionality-supposed-to-exist-on--insert-sku-here-"></a>Эту функцию должен существовать на *- Здесь Вставка SKU -*?

**Bluetooth LE**: Да, находится в OneCore и должны быть доступны на последних устройствах с работает стек Bluetooth LE все функциональные возможности. 
> Предупреждение: Периферийный роль зависит от аппаратного обеспечения и некоторые выпуски Windows Server не поддерживают Bluetooth. 

**Bluetooth BR/EDR (классические)**: существует несколько вариантов, но в целом, у них есть очень похожи поддержка уровня профилей. Просмотреть документы на [RFCOMM](send-or-receive-files-with-rfcomm.md) и эти документы поддерживаемые профиля для [ПК](https://support.microsoft.com/en-us/help/10568/windows-10-supported-bluetooth-profiles) и [Телефон](https://support.microsoft.com/en-us/help/10569/windows-10-mobile-supported-bluetooth-profiles)

