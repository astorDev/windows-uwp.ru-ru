---
author: muhsinking
ms.assetid: E0B9532F-1195-4927-99BE-F41565D891AD
title: Перечисление устройств по сети
description: Помимо обнаружения локально подключенных устройств вы можете использовать интерфейсы API Windows.Devices.Enumeration для перечисления устройств по беспроводным и сетевым протоколам.
ms.author: mukin
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 00f8d4314d67828fa30007d3b8af4c4e1d06c154
ms.sourcegitcommit: cd00bb829306871e5103db481cf224ea7fb613f0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/31/2018
ms.locfileid: "5860292"
---
# <a name="enumerate-devices-over-a-network"></a>Перечисление устройств по сети



**Важные API**

- [**Windows.Devices.Enumeration**](https://docs.microsoft.com/en-us/uwp/api/Windows.Devices.Enumeration)

Помимо обнаружения локально подключенных устройств вы можете использовать интерфейсы API [**Windows.Devices.Enumeration**](https://msdn.microsoft.com/library/windows/apps/BR225459) для перечисления устройств по беспроводным и сетевым протоколам.

## <a name="enumerating-devices-over-networked-or-wireless-protocols"></a>Перечисление устройств по сетевым или беспроводным протоколам

Иногда вам необходимо перечислить устройства, которые не подключены локально и могут быть обнаружены только по протоколам проводной или беспроводной сети. Для этого у API [**Windows.Devices.Enumeration**](https://msdn.microsoft.com/library/windows/apps/BR225459) есть три различных типа объектов устройства: **AssociationEndpoint** (AEP), **AssociationEndpointContainer** (контейнер AEP) и **AssociationEndpointService** (служба AEP). Вместе данные типы называют объектами AEP.

Некоторые API устройства предоставляют строку средства выбора, которую можно использовать для перечисления доступных объектов AEP. К ним могут относиться связанные и не связанные с системой устройства. Для некоторых устройств связывание может не потребоваться. Эти API устройств могут попытаться связать устройство, если связывание необходимо для взаимодействия с ним. Примером API, которые используют этот шаблон, служит Wi-Fi Direct. Если эти API устройства не связывают устройство автоматически, можно выполнить сопряжение с помощью объекта [**DeviceInformationPairing**](https://msdn.microsoft.com/library/windows/apps/Mt168396), доступного в [**DeviceInformation.Pairing**](https://msdn.microsoft.com/library/windows/apps/Dn705960).

Однако в некоторых случаях может потребоваться вручную обнаружить устройства без заданной строки средства выбора. Например, вы можете просто собрать информацию об устройствах AEP без взаимодействия с ними или получить больше объектов AEP, чем можно обнаружить с помощью заданной строки средства выбора. В этом случае вы сами создадите строку средства выбора и используете ее, следуя инструкциям в разделе [Создание средства выбора устройств](build-a-device-selector.md).

При создании собственного средства выбора устройств настоятельно рекомендуется ограничить область перечисления протоколами, которые вас интересуют. Например, если вас интересуют UPnP-устройства, нет необходимости выполнять поиск устройств Wi-Fi Direct с помощью радио Wi-Fi. Windows присвоила удостоверение каждому протоколу. Их можно использовать при определении области перечисления. В таблице ниже приведены типы и коды протоколов.

| Протокол или тип сетевого устройства              | Id                                         |
|----------------------------------------------|--------------------------------------------|
| UPnP (включая DIAL и DLNA)               | **{0e261de4-12f0-46e6-91ba-428607ccef64}** |
| Web Services on Devices (WSD)                | **{782232aa-a2f9-4993-971b-aedc551346b0}** |
| Wi-Fi Direct                                 | **{0407d24e-53de-4c9a-9ba1-9ced54641188}** |
| Обнаружение служб DNS (DNS-SD)               | **{4526e8c1-8aac-4153-9b16-55e86ada0e54}** |
| POS-терминал                             | **{d4bf61b3-442e-4ada-882d-fa7B70c832d9}** |
| Сетевые принтеры (принтеры Active Directory) | **{37aba761-2124-454c-8d82-c42962c2de2b}** |
| Windows Connect Now (WCN)                    | **{4c1b1ef8-2f62-4b9f-9bc5-b21ab636138f}** |
| Док-станция WiGig                                  | **{a277f3a5-8764-4f88-8045-4c5e962640b1}** |
| Подготовка Wi-Fi для принтеров HP           | **{c85ef710-f344-4792-bb6d-85a4346f1e69}** |
| Bluetooth                                    | **{e0cbf06c-cd8b-4647-bb8a-263b43f0f974}** |
| Bluetooth с низким энергопотреблением                                 | **{bb7bb05e-5972-42b5-94fc-76eaa7084d49}** |

 

## <a name="aqs-examples"></a>Примеры AQS

У каждого типа AEP есть свойство, которое можно использовать, чтобы ограничить перечисление определенным протоколом. Не забудьте, что в AQS-фильтре можно использовать оператор OR, чтобы сгруппировать несколько протоколов. Ниже приведено несколько примеров строк AQS-фильтра, показывающих, как запросить наличие устройств AEP.

Эта AQS-строка запрашивает все UPnP-объекты **AssociationEndpoint**, когда [**DeviceInformationKind**](https://msdn.microsoft.com/library/windows/apps/Dn948991) имеет значение **AsssociationEndpoint**.

``` syntax
System.Devices.Aep.ProtocolId:="{0e261de4-12f0-46e6-91ba-428607ccef64}"
```

Эта AQS-строка запрашивает все UPnP-объекты и WSD-объекты **AssociationEndpoint**, когда [**DeviceInformationKind**](https://msdn.microsoft.com/library/windows/apps/Dn948991) имеет значение **AsssociationEndpoint**.

``` syntax
System.Devices.Aep.ProtocolId:="{782232aa-a2f9-4993-971b-aedc551346b0}" OR
System.Devices.Aep.ProtocolId:="{0e261de4-12f0-46e6-91ba-428607ccef64}"
```

Эта AQS-строка запрашивает все UPnP-объекты **AssociationEndpointService**, когда [**DeviceInformationKind**](https://msdn.microsoft.com/library/windows/apps/Dn948991) имеет значение **AsssociationEndpointService**.

``` syntax
System.Devices.AepService.ProtocolId:="{0e261de4-12f0-46e6-91ba-428607ccef64}"
```

Эта AQS-строка запрашивает объекты **AssociationEndpointContainer**, у которых [**DeviceInformationKind**](https://msdn.microsoft.com/library/windows/apps/Dn948991) имеет значение **AssociationEndpointContainer**, но находит их только в том случае, если перечисление выполняется по протоколу UPnP. Обычно неэффективно перечислять контейнеры, которые поступают только из одного протокола. Однако это может быть полезно, если нужно ограничить фильтр протоколами, по которым можно обнаружить ваше устройство.

``` syntax
System.Devices.AepContainer.ProtocolIds:~~"{0e261de4-12f0-46e6-91ba-428607ccef64}"
```

 

 
