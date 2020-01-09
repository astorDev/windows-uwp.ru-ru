---
ms.assetid: F8A741B4-7A6A-4160-8C5D-6B92E267E6EA
title: Связывание устройств
description: Некоторые устройства необходимо связать, прежде чем их можно будет использовать. Пространство имен Windows.Devices.Enumeration поддерживает три разных способа связывания устройств.
ms.date: 04/19/2019
ms.topic: article
keywords: Windows 10, uwp
ms.localizationpriority: medium
ms.custom: 19H1
ms.openlocfilehash: 1dbf843d9a45cbf31e5ec5c1a538e6e5e2b53ee2
ms.sourcegitcommit: 26bb75084b9d2d2b4a76d4aa131066e8da716679
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/06/2020
ms.locfileid: "75684686"
---
# <a name="pair-devices"></a>Связывание устройств



**Важные API**

- [**Windows. Devices. Enumeration**](https://docs.microsoft.com/uwp/api/Windows.Devices.Enumeration)

Некоторые устройства необходимо связать, прежде чем их можно будет использовать. Пространство имен [**Windows.Devices.Enumeration**](https://docs.microsoft.com/uwp/api/Windows.Devices.Enumeration) поддерживает три разных способа связывания устройств.

-   Автоматическое связывание
-   Базовое связывание
-   Пользовательское связывание

**Совет**  некоторые устройства не обязательно должны быть парными для использования. Такие случаи описаны в разделе об автоматическом связывании.

 

## <a name="automatic-pairing"></a>Автоматическое связывание


Иногда вам необходимо использовать устройство в своем приложении, но вас не интересует, связано ли это устройство. Вы просто хотите иметь возможность использовать функцию, связанную с устройством. Например, если вашему приложению нужно просто сделать снимок веб-камерой, вы не обязательно заинтересованы в самом устройстве. Вас интересует только съемка. При наличии API устройства, доступных для интересующего вас устройства, этот сценарий относится к автоматическому связыванию.

В этом случае вы просто используете API, связанные с устройством, осуществляя вызовы по мере необходимости и доверяя системе обработку каждого связывания, которое может потребоваться. Некоторые устройства не нужно связывать, чтобы использовать их функции. Если устройство не требует связывания, API устройства незаметно обработают действие связывания. Следовательно, вам не нужно интегрировать эту функцию в свое приложение. Вашему приложению не будет известно о том, связано ли данное устройство или требуется ли это действие вообще, но вы сможете получать доступ к устройству и использовать его функции.

## <a name="basic-pairing"></a>Базовое связывание


Базовое связывание происходит, когда ваше приложение использует API [**Windows.Devices.Enumeration**](https://docs.microsoft.com/uwp/api/Windows.Devices.Enumeration), чтобы попытаться связать устройство. В этом сценарии вы предоставляете Windows контроль над процессом связывания и его обработкой. При необходимости взаимодействия с пользователем таковое будет обработано Windows. Вам можете использовать базовое связывание при необходимости связывания с устройством в отсутствие релевантного API устройства, который будет предпринимать попытку автоматического связывания. Вы просто хотите иметь возможность использовать устройство, а для этого необходимо выполнить связывание.

Чтобы попытаться выполнить базовое связывание, сначала необходимо получить объект [**DeviceInformation**](https://docs.microsoft.com/uwp/api/Windows.Devices.Enumeration.DeviceInformation) для устройства, которое вас интересует. Получив этот объект, вы сможете взаимодействовать со свойством [**DeviceInformation.Pairing**](https://docs.microsoft.com/uwp/api/windows.devices.enumeration.deviceinformation.pairing), являющимся объектом [**DeviceInformationPairing**](https://docs.microsoft.com/uwp/api/windows.devices.enumeration.deviceinformation.pairing). Чтобы выполнить связывание, просто вызовите [**DeviceInformationPairing.PairAsync**](https://docs.microsoft.com/uwp/api/windows.devices.enumeration.deviceinformationpairing.pairasync). Вам необходимо будет **подождать** результат, чтобы предоставить приложению время попытаться завершить действие связывания. Будет возвращен результат действия связывания, и при отсутствии ошибок устройство будет связано.

В случае использования базового связывания вы также получаете доступ к дополнительной информации о состоянии связывания устройства. Например, вы знаете состояние связывания ([**IsPaired**](https://docs.microsoft.com/uwp/api/Windows.Devices.Enumeration.DeviceInformationPairing.IsPaired)) и вам известно о возможности связывания устройства ([**CanPair**](https://docs.microsoft.com/uwp/api/Windows.Devices.Enumeration.DeviceInformationPairing.CanPair)). Эти показатели являются свойствами объекта [**DeviceInformationPairing**](https://docs.microsoft.com/uwp/api/windows.devices.enumeration.deviceinformation.pairing). При использовании автоматического связывания у вас может не быть доступа к этим сведениям, если вы не получите релевантные объекты [**DeviceInformation**](https://docs.microsoft.com/uwp/api/Windows.Devices.Enumeration.DeviceInformation).

## <a name="custom-pairing"></a>Пользовательское связывание


Пользовательское связывание позволяет приложению участвовать в процессе связывания. Это позволяет вашему приложению указать [**DevicePairingKinds**](https://docs.microsoft.com/uwp/api/Windows.Devices.Enumeration.DevicePairingKinds), поддерживаемые для процесса связывания. На вас также ляжет ответственность за создание собственного пользовательского интерфейса для взаимодействия с пользователем при необходимости. Используйте пользовательское связывание, если необходимо, чтобы приложение имело несколько больше влияния на ход процесса связывания, или для отображения собственного пользовательского интерфейса связывания.

Для реализации пользовательского связывания вам потребуется получить объект [**DeviceInformation**](https://docs.microsoft.com/uwp/api/Windows.Devices.Enumeration.DeviceInformation) для необходимого устройства, как и в случае с базовым связыванием. Однако конкретным свойством, в котором вы заинтересованы, является [**DeviceInformation.Pairing.Custom**](https://docs.microsoft.com/uwp/api/windows.devices.enumeration.deviceinformationpairing.custom). Это даст вам объект [**DeviceInformationCustomPairing**](https://docs.microsoft.com/uwp/api/windows.devices.enumeration.deviceinformationcustompairing). Все методы [**DeviceInformationCustomPairing.PairAsync**](https://docs.microsoft.com/uwp/api/windows.devices.enumeration.deviceinformationcustompairing.pairasync) требуют включения параметра [**DevicePairingKinds**](https://docs.microsoft.com/uwp/api/Windows.Devices.Enumeration.DevicePairingKinds). Это указывает на действия, которые пользователю необходимо предпринять, чтобы попытаться связать устройство. Дополнительные сведения о различных видах и необходимых действиях см. на справочной странице **DevicePairingKinds**. Как и в случае с базовым связыванием, вам необходимо будет **подождать** результат, чтобы предоставить приложению время попытаться выполнить действие связывания. Будет возвращен результат действия связывания, и при отсутствии ошибок устройство будет связано.

Для поддержки пользовательского связывания вам необходимо создать обработчик для события [**PairingRequested**](https://docs.microsoft.com/uwp/api/windows.devices.enumeration.deviceinformationcustompairing.pairingrequested). Этот обработчик должен учитывать все различные значения [**DevicePairingKinds**](https://docs.microsoft.com/uwp/api/Windows.Devices.Enumeration.DevicePairingKinds), которые могут использоваться в сценарии пользовательского связывания. Соответствующее действие будет зависеть от значения **DevicePairingKinds**, предоставляемого в числе аргументов события.

Важно помнить о том, что пользовательское связывание всегда является операцией на уровне системы. Из-за этого при работе на настольном компьютере или в Windows Phone перед началом связывания на экране всегда будет отображаться диалоговое окно системы. Это связано с тем, что для обеих этих платформ требуется согласие пользователя. Поскольку это диалоговое окно создается автоматически, вам не нужно будет создавать собственное диалоговое окно при выборе [**DevicePairingKinds**](https://docs.microsoft.com/uwp/api/Windows.Devices.Enumeration.DevicePairingKinds)**ConfirmOnly** во время работы на этих платформах. Для другого **DevicePairingKinds** необходимо будет выполнить специальную обработку в зависимости от конкретного значения **DevicePairingKinds**. См. пример обработки пользовательского связывания для разных значений **DevicePairingKinds**.

Начиная с Windows 10 версии 1903, поддерживается новый **девицепаирингкиндс** , **провидепассвордкредентиал**. Это значение означает, что приложение должно запросить имя пользователя и пароль у пользователя для проверки подлинности на связанном устройстве. Для обработки этого сценария вызовите метод [**акцептвиспассвордкредентиал**](https://docs.microsoft.com/uwp/api/windows.devices.enumeration.devicepairingrequestedeventargs.acceptwithpasswordcredential?branch=release-19h1#Windows_Devices_Enumeration_DevicePairingRequestedEventArgs_AcceptWithPasswordCredential_Windows_Security_Credentials_PasswordCredential_) аргументов события обработчика событий **паирингрекуестед** , чтобы принять связывание. Передайте объект [**PasswordCredential**](https://docs.microsoft.com/uwp/api/windows.security.credentials.passwordcredential) , инкапсулирующий имя пользователя и пароль в качестве параметра. Обратите внимание, что имя пользователя и пароль для удаленного устройства отличаются от и часто не совпадают с учетными данными для пользователя, выполнившего вход локально.

## <a name="unpairing"></a>Отмена связывания


Отмена связывания уместна только в сценариях базового или пользовательского связывания, описанных выше. При использовании автоматического связывания нет необходимости в отмене связывания, так как приложение не учитывает состояние связывания устройства. Процесс отмены связывания устройства является одинаковым для базового и пользовательского связывания. Это обусловлено тем, что нет необходимости предоставлять дополнительные сведения или участвовать в отмене связывания.

Первым шагом при отмене связывания является получение объекта [**DeviceInformation**](https://docs.microsoft.com/uwp/api/Windows.Devices.Enumeration.DeviceInformation) для устройства, связывание которого необходимо отменить. Затем необходимо получить свойство [**DeviceInformation.Pairing**](https://docs.microsoft.com/uwp/api/windows.devices.enumeration.deviceinformation.pairing) и вызвать [**DeviceInformationPairing.UnpairAsync**](https://docs.microsoft.com/uwp/api/windows.devices.enumeration.deviceinformationpairing.unpairasync). Как и при связывании, нужно будет **подождать** результат. Будет возвращен результат действия отмены связывания, и при отсутствии ошибок связывание устройства будет отменено.

## <a name="sample"></a>Пример


Чтобы скачать пример использования API [**Windows.Devices.Enumeration**](https://docs.microsoft.com/uwp/api/Windows.Devices.Enumeration), перейдите [по этой ссылке](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/DeviceEnumerationAndPairing).

 

 
