---
title: Bluetooth с низким энергопотреблением
description: В этом разделе представлен краткий обзор Bluetooth с низким ЭНЕРГОПОТРЕБЛЕНИЕМ в приложениях UWP.
ms.date: 03/15/2017
ms.topic: article
keywords: Windows 10, uwp, bluetooth, bluetooth с низким ЭНЕРГОПОТРЕБЛЕНИЕМ, низком энергопотреблении, gatt, зазора, центральная, периферийного устройства, клиент, сервер, наблюдателя, "издатель"
ms.localizationpriority: medium
ms.openlocfilehash: ecc78bd4bb079adbaaa58c981ce55457c522764b
ms.sourcegitcommit: 49d58bc66c1c9f2a4f81473bcb25af79e2b1088d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/11/2018
ms.locfileid: "8938126"
---
# <a name="bluetooth-low-energy"></a>Bluetooth с низким энергопотреблением
Bluetooth с низким энергопотреблением (LE) — это спецификация, определяющая протоколы для обнаружения и обмен данными между энергопотребление устройства. Обнаружение устройств выполняется через протокол универсального профиля доступа (пробел). После обнаружения обмена данными для устройства выполняется по протоколу Generic Attribute (GATT). В этом разделе представлен краткий обзор Bluetooth с низким ЭНЕРГОПОТРЕБЛЕНИЕМ в приложениях UWP. Более подробные сведения о Bluetooth с низким ЭНЕРГОПОТРЕБЛЕНИЕМ, см. в разделе [Спецификации Core Bluetooth](https://www.bluetooth.com/specifications/bluetooth-core-specification) версии 4.0, где был введен Bluetooth с низким ЭНЕРГОПОТРЕБЛЕНИЕМ. 

![Роли Bluetooth с низким ЭНЕРГОПОТРЕБЛЕНИЕМ](images/gatt-roles.png)

*GATT и РАЗРЫВА роли появились в Windows 10 версии 1703*

Протоколы GATT и РАЗРЫВА может осуществляться в приложении UWP с помощью следующих пространств имен.
- [Windows.Devices.Bluetooth.GenericAttributeProfile](https://docs.microsoft.com/en-us/uwp/api/windows.devices.bluetooth.genericattributeprofile)
- [Windows.Devices.Bluetooth.Advertisement](https://docs.microsoft.com/en-us/uwp/api/windows.devices.bluetooth.genericattributeprofile)

## <a name="central-and-peripheral"></a>Центр и периферийных устройств
Две основные функции обнаружения называются центральная и периферийных устройств. Как правило Windows работает в режиме центрального и подключается к различным периферийные устройства. 

## <a name="attributes"></a>Атрибуты
Распространенные аббревиатуре, которое вы увидите в API-интерфейсы Windows Bluetooth — Generic Attribute (GATT). Профиль GATT определяет структуру данных и режима работы, по которым связи два устройства Bluetooth с низким ЭНЕРГОПОТРЕБЛЕНИЕМ. Атрибут является основного создание блока GATT. Приведены основные типы атрибутов служб, характеристик и дескрипторов. Эти атрибуты по-разному выполнять между клиентами и серверами, поэтому это удобнее обсуждать их взаимодействие в соответствующих разделах. 

![Типичные иерархии атрибута в общего профиля](images/gatt-service.png)

*Служба частота пульса выражается в форме API сервер GATT*

## <a name="client-and-server"></a>Клиента и сервера
После установления подключения, устройство, которое содержит данные (обычно датчик небольшой IoT или носимое устройство) называется сервера. Устройство, которое использует эти данные для выполнения функций называется клиента. Например ПК с Windows (клиент) считывает данные из монитор частота пульса (сервер), чтобы отслеживать пользователь работу оптимально. Дополнительные сведения см. в разделах [Клиент GATT](gatt-client.md) и [Сервер GATT](gatt-server.md) .

## <a name="watchers-and-publishers-beacons"></a>Наблюдатели и издателей (маяки)
В дополнение к роли центрального и периферийные устройства роли наблюдателя и вещателем. Источников часто называются маяков, они не передают данные по GATT так, как они используют ограниченном пространстве, предоставленных в пакете объявления для обмена данными. Аналогичным образом наблюдателя не нужно установить подключение для получения данных, он ищет поблизости объявления. Для настройки Windows отслеживать соседние рекламу, используйте класс [BluetoothLEAdvertisementWatcher](https://docs.microsoft.com/en-us/uwp/api/windows.devices.bluetooth.advertisement.bluetoothleadvertisementwatcher) . Чтобы рассылка маяка полезных данных, используйте класс [BluetoothLEAdvertisementPublisher](https://docs.microsoft.com/en-us/uwp/api/windows.devices.bluetooth.advertisement.bluetoothleadvertisementpublisher) . Дополнительные сведения см. в разделе [объявления](ble-beacon.md) .

## <a name="see-also"></a>См. также
- [Windows.Devices.Bluetooth.GenericAttributeProfile](https://docs.microsoft.com/en-us/uwp/api/windows.devices.bluetooth.genericattributeprofile)
- [Windows.Devices.Bluetooth.Advertisement](https://docs.microsoft.com/en-us/uwp/api/windows.devices.bluetooth.genericattributeprofile)
- [Спецификации Bluetooth Core](https://www.bluetooth.com/specifications/bluetooth-core-specification)