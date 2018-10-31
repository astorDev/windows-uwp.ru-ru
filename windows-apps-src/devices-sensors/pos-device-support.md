---
author: TerryWarwick
title: Поддержка аппаратных возможностей POS-устройств
description: В этой статье содержится информация о поддержке аппаратных возможностей, предусмотренной в каждом из классов POS-устройств.
ms.author: jken
ms.date: 06/13/2018
ms.topic: article
keywords: Windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: df6e2c15260759f164a37b68365e0268633b22d5
ms.sourcegitcommit: ca96031debe1e76d4501621a7680079244ef1c60
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2018
ms.locfileid: "5814881"
---
# <a name="supported-point-of-service-peripherals"></a>Поддерживаемые периферийные устройства точек обслуживания

## <a name="barcode-scanner"></a>Сканер штрихкодов
| Подключение | Поддержка |
| -------------|-------------|
| USB          | <p>Windows содержит встроенный драйвер класса для подключаемых через USB сканеров штрихкодов, разработанный на основе спецификации HID POS Scanner Usage Table (8c), опубликованной на сайте [USB.org](http://www.usb.org/developers/hidpage/). Список известных совместимых устройств см. в таблице ниже.  Смотрите руководство для сканера штрихкодов или обратитесь к производителю, чтобы определить, как настроить сканер в режиме **USB.HID.POS Scanner**. </p><p>Windows также поддерживает внедрение драйверов конкретных поставщиков для поддержки дополнительных сканеров штрихкодов, которые не поддерживают стандарт USB.HID.POS Scanner. Наличие драйверов для определенных поставщиков уточняйте у производителя сканера штрихкодов.</p><p>Производителям сканеров штрихкодов следует ознакомиться с информацией о создании пользовательского драйвера сканера штрихкодов в разделе [Руководство по проектированию драйверов для сканеров штрихкодов](https://aka.ms/pointofservice-drv)</p> |
| Bluetooth    | <p>Windows поддерживает Bluetooth-сканеры штрихкодов на основе SPP-SSI (протокол последовательного порта— простой последовательный интерфейс). Список известных совместимых устройств см. в таблице ниже. Смотрите руководство для сканера штрихкодов или обратитесь к производителю, чтобы определить, как настроить сканер в режиме **SPP-SSI**.</p> |
| Веб-камера       | <p>Начиная с Windows 10 версии 1803 можно считывать штрихкоды через объектив стандартной камеры из универсального приложения для Windows. Рекомендуется использовать камеру, поддерживающая автоматическую фокусировку и имеющую разрешение не менее 1920 x 1440.  Некоторые камеры с более низким разрешением способны считывать стандартные штрихкоды, если штрихкод напечатан достаточно крупно.  Штрихкоды с более тонкими элементами могут требовать камер с более высоким разрешением.</p>| 
|


| Изготовитель  | Модель                          | Возможность | "Подключение"    | Тип         | Режим                      |
|---------------|--------------------------------|------------|--------------|--------------|---------------------------|
| Код          | Reader™ 950                    | 2D         | USB          | Ручные     | HID POS Scanner           |
| Код          | Reader™ 1021                   | 2D         | USB          | Ручные     | HID POS Scanner           |
| Код          | Reader™ 1421                   | 2D         | USB          | Ручные     | HID POS Scanner           |
| Код          | Reader™ 5000                   | 2D         | USB          | Презентации | HID POS Scanner           |
| Honeywell     | Genesis 7580g                  | 2D         | USB          | Презентации | HID POS Scanner           |
| Honeywell     | Granit 198Xi                   | 2D         | USB          | Ручные     | HID POS Scanner           |
| Honeywell     | Granit 191Xi                   | 2D         | USB          | Ручные     | HID POS Scanner           |
| Honeywell     | N5680                          | 2D         | внутренний     | Компонент    | HID POS Scanner           |
| Honeywell     | N3680                          | 2D         | внутренний     | Компонент    | HID POS Scanner           |
| Honeywell     | Орбита 7190g                    | 2D         | USB          | Презентации | HID POS Scanner           |
| Honeywell     | Stratos 2700                   | 2D         | USB          | Из счетчиков   | HID POS Scanner           |
| Honeywell     | Voyager 1200g                  | 1D         | USB          | Ручные     | HID POS Scanner           |
| Honeywell     | Voyager 1202g                  | 1D         | USB          | Ручные     | HID POS Scanner           |
| Honeywell     | Voyager 1202-bf                | 1D         | USB          | Ручные     | HID POS Scanner           |
| Honeywell     | Voyager 145Xg                  | 1D / 2D ¹   | USB          | Ручные     | HID POS Scanner           |
| Honeywell     | Voyager 1602g                  | 2D         | USB          | Ручные     | HID POS Scanner           |
| Honeywell     | Xenon 1900g                    | 2D         | USB          | Ручные     | HID POS Scanner           |
| Honeywell     | Xenon 1902g                    | 2D         | USB          | Ручные     | HID POS Scanner           |
| Honeywell     | Xenon 1902g-bf                 | 2D         | USB          | Ручные     | HID POS Scanner           |
| Honeywell     | Xenon 1900h                    | 2D         | USB          | Ручные     | HID POS Scanner           |
| Honeywell     | Xenon 1902h                    | 2D         | USB          | Ручные     | HID POS Scanner           |
| HP            | Сканер штрихкодов значение (HR2150) | 2D         | USB          | Ручные     | HID POS Scanner           |
| Intermec      | SG20                           | 2D         | USB          | Ручные     | HID POS Scanner           |
| Socket Mobile | CHS 7Ci                        | 1D         | Bluetooth    | Ручные     | Профиль последовательного порта (SPP) |
| Socket Mobile | CHS 7Di                        | 1D         | Bluetooth    | Ручные     | Профиль последовательного порта (SPP) |
| Socket Mobile | CHS 7Mi                        | 1D         | Bluetooth    | Ручные     | Профиль последовательного порта (SPP) |
| Socket Mobile | CHS 7Pi                        | 1D         | Bluetooth    | Ручные     | Профиль последовательного порта (SPP) |
| Socket Mobile | Название — CHS 8Ci                        | 1D         | Bluetooth    | Ручные     | Профиль последовательного порта (SPP) |
| Socket Mobile | DuraScan D700                  | 1D         | Bluetooth    | Ручные     | Профиль последовательного порта (SPP) |
| Socket Mobile | DuraScan D730                  | 1D         | Bluetooth    | Ручные     | Профиль последовательного порта (SPP) |
| Socket Mobile | DuraScan D740                  | 2D         | Bluetooth    | Ручные     | Профиль последовательного порта (SPP) |
| Socket Mobile | SocketScan S700                | 1D         | Bluetooth    | Ручные     | Профиль последовательного порта (SPP) |
| Socket Mobile | SocketScan S730                | 1D         | Bluetooth    | Ручные     | Профиль последовательного порта (SPP) |
| Socket Mobile | SocketScan S740                | 2D         | Bluetooth    | Ручные     | Профиль последовательного порта (SPP) |
| Socket Mobile | SocketScan S800                | 1D         | Bluetooth    | Ручные     | Профиль последовательного порта (SPP) |
| Socket Mobile | SocketScan S850                | 2D         | Bluetooth    | Ручные     | Профиль последовательного порта (SPP) |
| Zebra         | DS2278                         | 2D         | USB          | Ручные     | HID POS Scanner           |
| Zebra         | DS8108²                        | 2D         | USB          | Ручные     | HID POS Scanner           |
|


¹ Upgradable для поддержки двухмерной штрихкоды через Honeywell <br/>
² Минимальная версия встроенного по — 016 (2018.01.18) требуется. Обновляемый с помощью Zebra [123Scan](http://www.zebra.com/123Scan). 


<hr>

### <a name="windows-devices-with-built-in-barcode-scanner"></a>Устройства Windows с помощью сканер штрих-кодов
| Изготовитель   | Модель | Операционная система |
|----------------|-------|------------------|
| Innowi         | ChecOut M | Windows 10   |

### <a name="windows-mobile-devices-with-built-in-barcode-scanner"></a>Устройства с Windows Mobile со сканера штрихкодов встроенных
| Изготовитель   | Модель | Операционная система |
|----------------|-------|------------------|
| Bluebird       | EF400 | Windows Mobile   |
| Bluebird       | EF500 | Windows Mobile   |
| Bluebird       | EF500R | Windows Mobile   |
| Honeywell      | CT50   | Windows Mobile   |
| Honeywell      | D75e | Windows Mobile   |
| Janam          | XT2      | Windows Mobile   |
| Panasonic      | FZ-E1 | Windows Mobile   |
| Panasonic      | FZ-F1 |Windows Mobile   |
| PointMobile    | PM80 | Windows Mobile   |
| Zebra          | TC700j | Windows Mobile   |
| HP             | Элитному X3 Jacket | Windows Mobile   |




## <a name="cash-drawer"></a>Кассовый аппарат
| Возможности подключения | Поддержка |
| -------------|-------------|
| Сеть или Bluetooth | <p> Прямое подключение к кассовому аппарату может быть выполнено по сети или через Bluetooth, в зависимости от возможностей конкретного кассового аппарата. </p><p>Кассовый аппарат APG: NetPRO, BluePRO</p> |
| Порт DK | <p> Кассовые аппараты без поддержки сети или Bluetooth можно подключить через порт DK на поддерживаемом чековом принтере или периферийном устройстве Star Micronics DK-AirCash. </p>
| OPOS    | <p> Поддерживает все совместимые с OPOS кассовые аппараты через служебные объекты OPOS, предоставляемые производителем. Установите драйверы OPOS согласно инструкциям производителя устройства. </p> |


## <a name="customer-display-linedisplay"></a>Дисплей для клиента (LineDisplay)
Поддерживает все совместимые с OPOS дисплеи для клиентов через служебные объекты OPOS, предоставляемые производителем. Установите драйверы OPOS согласно инструкциям производителя устройства.

## <a name="magnetic-stripe-reader"></a>Считыватель магнитных карт
Windows предоставляет поддержку для перечисленных ниже считывателей магнитных карт от Magtek и IDTech, используя их идентификатор поставщика и код продукта (VID/PID).

| Изготовитель |    Модели |  Номер детали |
|--------------|-----------|--------------|
| IDTech | SecureMag (VID:0ACD PID:2010) | IDRE-3x5xxxx |
| | MiniMag (VID:0ACD PID:0500) |   IDMB-3x5xxxx |
| Magtek | MagneSafe (VID:0801 PID:0011) |  210730xx |
| | Dynamag (VID:0801 PID:0002) |   210401xx |

 Windows поддерживает внедрение дополнительных драйверов от определенных поставщиков для поддержки дополнительных считывателей магнитных карт. Наличие дополнительных драйверов уточняйте у изготовителя считывателя магнитных карт. Производителям считывателей магнитных карт следует ознакомиться с информацией о создании пользовательского драйвера считывателя магнитных карт в разделе [Руководство по проектированию драйверов для считывателей магнитных карт](https://aka.ms/pointofservice-drv).

## <a name="receipt-printer-posprinter"></a>Чековый принтер (POSPrinter)
| Возможности подключения | Поддержка |
| -------------|-------------|
| Сеть и Bluetooth | <p>Windows поддерживает сетевые и подключенные по Bluetooth чековые принтеры с использованием языка управления принтерами Epson ESC/POS.  Указанные ниже принтеры обнаруживаются автоматически с помощью API POSPrinter. Дополнительные чековые принтеры с функцией эмуляции ESC/POS также могут поддерживаться, но для них потребуется привязка через процесс [связывания вне диапазона](https://aka.ms/pointofservice-oobpairing).</p><p>Примечание. Этот метод не поддерживает бланковые и журнальные станции.</p> |
| OPOS    | <p> Поддерживает любые совместимые с OPOS чековые принтеры через служебные объекты OPOS. Установите драйверы OPOS согласно инструкциям производителя устройства. </p> |

### <a name="stationary-receipt-printers-networkbluetooth"></a>Стационарные чековые принтеры (сетевые, Bluetooth)
| Изготовитель |    Модели |
|--------------|-----------|
| Epson |   TM-T88V, TM-T70, TM-T20, TM-U220 |

### <a name="mobile-receipt-printers-bluetooth"></a>Мобильные чековые принтеры (Bluetooth)
| Изготовитель |    Модели |
|--------------|-----------|
| Epson |   Mobilink P20 (TM-P20), Mobilink P60 (TM-P60), Mobilink P80 (TM-P80) |
