---
author: msatranjr
title: Bluetooth с низким энергопотреблением
description: В этом разделе приведен краткий обзор из Bluetooth LE в приложениях UWP.
ms.author: misatran
ms.date: 03/15/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: Windows 10, uwp, bluetooth, bluetooth LE, низкой электроэнергии, gatt, разрывов, центра, периферийных, клиента, сервер, наблюдатель, publisher
ms.localizationpriority: medium
ms.openlocfilehash: 0d6cc1fb5a0b3cb96748b99c490b23a9e1df128f
ms.sourcegitcommit: 897a111e8fc5d38d483800288ad01c523e924ef4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/13/2018
ms.locfileid: "304973"
---
# <a name="bluetooth-low-energy"></a>Bluetooth с низким энергопотреблением
Электроэнергии Bluetooth Low (LE) — это спецификация, определяющая протоколы для обнаружения и обмена данными между энергопотребление устройств. Обнаружение устройств осуществляется с помощью протокола профилей универсального доступа (пробел). После обнаружения устройств устройство связи осуществляется с помощью протокола универсальный атрибут (GATT). В этом разделе приведен краткий обзор из Bluetooth LE в приложениях UWP. Более подробные сведения о Bluetooth LE см [Bluetooth спецификация основной](https://www.bluetooth.com/specifications/bluetooth-core-specification) версии 4.0, где была введена Bluetooth LE. 

![Роли LE Bluetooth](images/gatt-roles.png)

*Роли GATT и РАЗРЫВОВ были представлены в 10 Windows версии 1703*

Протоколы GATT и РАЗРЫВОВ в ваше приложение UWP можно реализовать с помощью следующих пространств имен.
- [Windows.Devices.Bluetooth.GenericAttributeProfile](https://docs.microsoft.com/en-us/uwp/api/windows.devices.bluetooth.genericattributeprofile)
- [Windows.Devices.Bluetooth.Advertisement](https://docs.microsoft.com/en-us/uwp/api/windows.devices.bluetooth.genericattributeprofile)

## <a name="central-and-peripheral"></a>Центра и периферийный
Две основные роли обнаружения называются центра и периферийных устройств. В общем случае Windows в режиме центра и подключается к различным периферийных устройств. 

## <a name="attributes"></a>Атрибуты
Распространенные сокращение, отображаемыми в API-интерфейсы Bluetooth Windows — универсальный атрибут (GATT). Профиль GATT определяет структуру данных и режима работы, с помощью которого два устройства Bluetooth LE общаться. Атрибут — это основной строительным блоком GATT. Основные типы атрибутов, служб, характеристики и дескрипторов. Эти атрибуты, выполните по-разному между клиентами и серверами, чтобы сделать его более полезно рассмотреть их взаимодействие в соответствующие разделы. 

![Типичная иерархии атрибута в общих профилей](images/gatt-service.png)

*Служба частоты выражается в виде GATT Server API*

## <a name="client-and-server"></a>Клиент и сервер
После установки подключения устройства, который содержит данные (обычно малых датчика IoT или переносной) называется сервера. Устройство, использующее эти данные для выполнения функции называется клиента. Например ПК Windows (клиент) считывает данные из монитор частоты (сервер) для отслеживания, что пользователь работает в работе, оптимально. Для получения дополнительных сведений см [GATT клиентские](gatt-client.md) и [Серверные GATT](gatt-server.md) .

## <a name="watchers-and-publishers-beacons"></a>Наблюдателей и издателей (маяки)
В дополнение к роли центра и периферийных существует наблюдателя и вещателем роли. Источников обычно называемую маркеров, они не устанавливать связь по GATT так, как они используют ограниченный пространства, содержащиеся в пакете объявления для обмена данными. Аналогично наблюдатель не имеет для установления соединения для получения данных, сканирует рядом оповещений. Чтобы настроить Windows выполняли ближайших рекламные, используйте класс [BluetoothLEAdvertisementWatcher](https://docs.microsoft.com/en-us/uwp/api/windows.devices.bluetooth.advertisement.bluetoothleadvertisementwatcher) . Для вещания маяков полезных данных, используйте класс [BluetoothLEAdvertisementPublisher](https://docs.microsoft.com/en-us/uwp/api/windows.devices.bluetooth.advertisement.bluetoothleadvertisementpublisher) . Дополнительные сведения см в разделе [объявлений](ble-beacon.md) .

## <a name="see-also"></a>См. также
- [Windows.Devices.Bluetooth.GenericAttributeProfile](https://docs.microsoft.com/en-us/uwp/api/windows.devices.bluetooth.genericattributeprofile)
- [Windows.Devices.Bluetooth.Advertisement](https://docs.microsoft.com/en-us/uwp/api/windows.devices.bluetooth.genericattributeprofile)
- [Спецификация основных Bluetooth](https://www.bluetooth.com/specifications/bluetooth-core-specification)