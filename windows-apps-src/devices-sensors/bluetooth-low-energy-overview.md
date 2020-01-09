---
title: Bluetooth с низким энергопотреблением
description: В этом разделе содержится краткий обзор Bluetooth с низким потреблением в приложениях UWP.
ms.date: 03/19/2017
ms.topic: article
keywords: windows 10, uwp, bluetooth, bluetooth с низким энергопотреблением, низкое энергопотребление, gatt, gap, центральное устройство, периферийное устройство, клиент, сервер, наблюдатель, издатель
ms.localizationpriority: medium
ms.openlocfilehash: 4859dfb540b252f379a0ec3cbfe52985c0776fd9
ms.sourcegitcommit: 26bb75084b9d2d2b4a76d4aa131066e8da716679
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/06/2020
ms.locfileid: "75684849"
---
# <a name="bluetooth-low-energy"></a>Bluetooth с низким энергопотреблением
Bluetooth с низким энергопотреблением (LE) является спецификацией, определяющей протоколы для обнаружения и обмена данными между энергоэффективными устройствами. Обнаружение устройств выполняется с помощью протокола Generic Access Profile (GAP). После обнаружения обмен данными между устройствами выполняется с помощью протокола Generic Attribute (GATT). В этом разделе содержится краткий обзор Bluetooth с низким потреблением в приложениях UWP. Более подробные сведения о Bluetooth с низким энергопотреблением см. в разделе [Основные характеристики Bluetooth](https://www.bluetooth.com/specifications/bluetooth-core-specification/) версии 4.0, где представлена технология Bluetooth с низким энергопотреблением. 

![Роли Bluetooth с низким энергопотреблением](images/gatt-roles.png)

*Роли GATT и GAP появились в Windows 10 версии 1703*

Протоколы GATT и GAP можно внедрить в приложение UWP с помощью указанных ниже пространств имен.
- [Windows.Devices.Bluetooth.GenericAttributeProfile](https://docs.microsoft.com/uwp/api/windows.devices.bluetooth.genericattributeprofile)
- [Windows. Devices. Bluetooth. Advertisement](https://docs.microsoft.com/uwp/api/windows.devices.bluetooth.advertisement)

## <a name="central-and-peripheral"></a>Центральное и периферийное устройство
Две основные роли обнаружения называются центральное устройство и периферийное устройство. Как правило, Windows работает в режиме центрального устройства и подключается к различным периферийным устройствам. 

## <a name="attributes"></a>Атрибуты
Распространенная аббревиатура, которую можно увидеть в API Windows Bluetooth, — это Generic Attribute (GATT). Профиль GATT определяет структуру данных и режимы работы, по которым взаимодействуют два устройства Bluetooth с низким энергопотреблением. Атрибут является основным блоком построения GATT. Основными типами атрибутов являются службы, характеристики и дескрипторы. Эти атрибуты действуют по-разному между клиентами и серверами, поэтому полезнее обсудить их взаимодействие в соответствующих разделах. 

![Типичная иерархия атрибута в общем профиле](images/gatt-service.png)

*Служба "частота сердца" выражается в форме API сервера GATT.*

## <a name="client-and-server"></a>Клиент и сервер
После установления подключения устройство, которое содержит данные (обычно небольшой датчик IoT или носимое устройство), называется сервером. Устройство, которое использует данные для выполнения функций, называется клиентом. Например, компьютер под управлением Windows (клиент) считывает данные с монитора частоты пульса (сервер) для отслеживания оптимальных тренировок пользователя. Дополнительные сведения см. в разделах [Клиент GATT](gatt-client.md) и [Сервер GATT](gatt-server.md).

## <a name="watchers-and-publishers-beacons"></a>Наблюдатели и издатели (маяки)
Кроме ролей центрального и периферийного устройств, существуют роли наблюдателя и вещателя. Вещатели обычно называются маяками, они не взаимодействуют через GATT, поскольку используют ограниченное пространство, предоставленное в пакете объявления для передачи данных. Аналогично наблюдатель не должен устанавливать подключения для получения данных, он сканирует ближайшие рекламные объявления. Чтобы настроить Windows для отслеживания ближайших рекламных объявлений, используйте класс [BluetoothLEAdvertisementWatcher](https://docs.microsoft.com/uwp/api/windows.devices.bluetooth.advertisement.bluetoothleadvertisementwatcher). Чтобы транслировать полезные данные маяка, используйте класс [BluetoothLEAdvertisementPublisher](https://docs.microsoft.com/uwp/api/windows.devices.bluetooth.advertisement.bluetoothleadvertisementpublisher). Дополнительные сведения см. в разделе [Рекламные объявления](ble-beacon.md).

## <a name="see-also"></a>См. также
- [Windows.Devices.Bluetooth.GenericAttributeProfile](https://docs.microsoft.com/uwp/api/windows.devices.bluetooth.genericattributeprofile)
- [Windows. Devices. Bluetooth. Advertisement](https://docs.microsoft.com/uwp/api/windows.devices.bluetooth.advertisement)
- [Спецификация ядра Bluetooth](https://www.bluetooth.com/specifications/bluetooth-core-specification)
