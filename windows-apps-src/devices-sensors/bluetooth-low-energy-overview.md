---
author: msatranjr
title: Bluetooth с низким энергопотреблением
description: В этом разделе представлен краткий обзор Bluetooth с низким ЭНЕРГОПОТРЕБЛЕНИЕМ в приложениях UWP.
ms.author: misatran
ms.date: 03/15/2017
ms.topic: article
keywords: Windows 10, uwp, bluetooth, bluetooth с низким ЭНЕРГОПОТРЕБЛЕНИЕМ, низком энергопотреблении, gatt, зазора, центральное, периферийного устройства, клиент, сервер, наблюдателя, "издатель"
ms.localizationpriority: medium
ms.openlocfilehash: 9e5bef16c76ee14c2abb7a5a41ab02d150a97333
ms.sourcegitcommit: cd00bb829306871e5103db481cf224ea7fb613f0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/31/2018
ms.locfileid: "5871596"
---
# <a name="bluetooth-low-energy"></a>Bluetooth с низким энергопотреблением
Bluetooth с низким энергопотреблением (LE) — это спецификация, определяющая протоколы для обнаружения и обмен данными между энергопотребление устройства. Обнаружение устройств выполняется через протокол универсального профиля доступа (пробел). После обнаружения обмена данными для устройства выполняется по протоколу Generic Attribute (GATT). В этом разделе представлен краткий обзор Bluetooth с низким ЭНЕРГОПОТРЕБЛЕНИЕМ в приложениях UWP. Более подробные сведения о Bluetooth с низким ЭНЕРГОПОТРЕБЛЕНИЕМ, см. в разделе [Спецификации Core Bluetooth](https://www.bluetooth.com/specifications/bluetooth-core-specification) версии 4.0, где был введен Bluetooth с низким ЭНЕРГОПОТРЕБЛЕНИЕМ. 

![Bluetooth с низким ЭНЕРГОПОТРЕБЛЕНИЕМ ролей](images/gatt-roles.png)

*Роли GATT и РАЗРЫВА появились в Windows 10 версии 1703*

Протоколы GATT и РАЗРЫВА может осуществляться в приложении UWP с помощью следующих пространств имен.
- [Windows.Devices.Bluetooth.GenericAttributeProfile](https://docs.microsoft.com/en-us/uwp/api/windows.devices.bluetooth.genericattributeprofile)
- [Windows.Devices.Bluetooth.Advertisement](https://docs.microsoft.com/en-us/uwp/api/windows.devices.bluetooth.genericattributeprofile)

## <a name="central-and-peripheral"></a>Централизованной и периферийных устройств
Две основные функции обнаружения, называются центрально- и периферийного устройства. Как правило Windows работает в режиме централизованной и подключается к различным периферийных устройств. 

## <a name="attributes"></a>Атрибуты
Распространенные аббревиатуре, которое вы увидите в API-интерфейсы Windows Bluetooth — Generic Attribute (GATT). Профиль GATT определяет структуру данных и режимы работы, по которым связи два устройства Bluetooth с низким ЭНЕРГОПОТРЕБЛЕНИЕМ. Атрибут является основной стандартного блока GATT. Приведены основные типы атрибутов служб, характеристик и дескрипторов. Эти атрибуты по-разному выполнения между клиентами и серверами, поэтому полезнее обсудить их взаимодействие в соответствующих разделах. 

![Обычно атрибут иерархии в обычный профиль](images/gatt-service.png)

*Служба частота пульса выражается в виде API сервер GATT*

## <a name="client-and-server"></a>Клиент и сервер
После установки подключения устройства, который содержит данные (обычно датчик небольших IoT или носимое устройство) называется сервера. Устройство, которое использует эти данные для выполнения функций известная как клиент. Например ПК с Windows (клиент) считывает данные из монитора частота пульса (сервер), чтобы отслеживать пользователь работу оптимально. Дополнительные сведения см. в разделах [Клиент GATT](gatt-client.md) и [Сервер GATT](gatt-server.md) .

## <a name="watchers-and-publishers-beacons"></a>Наблюдатели и издателей (маяки)
В дополнение к роли центрального и периферийные устройства роли наблюдателя и вещателем. Источников часто называется маяков, они не передают данные через GATT так, как они используют ограниченном пространстве, предоставленных в пакете объявления для обмена данными. Аналогичным образом наблюдателя не нужно установить подключение для получения данных, сканирование на соседние рекламу. Для настройки Windows отслеживать соседние рекламу, используйте класс [BluetoothLEAdvertisementWatcher](https://docs.microsoft.com/en-us/uwp/api/windows.devices.bluetooth.advertisement.bluetoothleadvertisementwatcher) . Чтобы рассылка маяка полезных данных, используйте класс [BluetoothLEAdvertisementPublisher](https://docs.microsoft.com/en-us/uwp/api/windows.devices.bluetooth.advertisement.bluetoothleadvertisementpublisher) . Дополнительные сведения см. в разделе [объявления](ble-beacon.md) .

## <a name="see-also"></a>См. также
- [Windows.Devices.Bluetooth.GenericAttributeProfile](https://docs.microsoft.com/en-us/uwp/api/windows.devices.bluetooth.genericattributeprofile)
- [Windows.Devices.Bluetooth.Advertisement](https://docs.microsoft.com/en-us/uwp/api/windows.devices.bluetooth.genericattributeprofile)
- [Спецификация Bluetooth Core](https://www.bluetooth.com/specifications/bluetooth-core-specification)