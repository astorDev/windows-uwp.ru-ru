---
author: msatranjr
title: "Вопросы и ответы для Bluetooth-разработчиков"
description: "Эта статья содержит ответы на часто задаваемые вопросы, связанные с API-интерфейсами UWP для работы с Bluetooth."
ms.author: misatran
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp
ms.assetid: e7dee32d-3756-430d-a026-32c1ee288a85
ms.openlocfilehash: 3dabc5ad2833eecfec1f397bdd5bf7f2b807a48d
ms.sourcegitcommit: 909d859a0f11981a8d1beac0da35f779786a6889
translationtype: HT
---
# <a name="bluetooth-developer-faq"></a>Вопросы и ответы для Bluetooth-разработчиков

Эта статья содержит ответы на часто задаваемые вопросы об API-интерфейсах UWP для работы с Bluetooth.

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

Эта функция недоступна для устройств Bluetooth Low Energy (клиент GATT), поэтому вам по-прежнему потребуется связать устройство на странице параметров или с помощью API-интерфейсов [Windows.Devices.Enumeration](https://msdn.microsoft.com/en-us/library/windows/apps/windows.devices.enumeration.aspx), чтобы получить доступ к ним.

