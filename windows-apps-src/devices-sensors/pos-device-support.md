---
author: mukin
title: "Поддержка устройств POS"
description: "Эта статья содержит сведения о поддержке устройств для каждого семейства устройств POS"
ms.author: mukin
ms.date: 05/17/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: Windows 10, uwp
ms.openlocfilehash: 58018385082f7f7e49edba0351d919bc840ade05
ms.sourcegitcommit: 53930c9871461f6106f785ae4fabb2296eb359f1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/23/2017
---
# <a name="pos-device-support"></a>Поддержка устройств POS

## <a name="barcode-scanner"></a>Сканер штрихкодов
| Подключение | Поддержка |
| -------------|-------------|
| USB          | <p>Windows содержит встроенный драйвер класса для подключаемых через USB сканеров штрихкодов, разработанный на основе спецификации HID POS Scanner Usage Table (8c), опубликованной на сайте [USB.org](http://www.usb.org/developers/hidpage/). Список известных совместимых устройств см. в таблице ниже.  Смотрите руководство для сканера штрихкодов или обратитесь к производителю, чтобы определить, можно ли настроить устройство в режиме USB.HID.POS.Scanner. </p><p>Windows также поддерживает внедрение драйверов конкретных поставщиков для поддержки дополнительных сканеров штрихкодов, которые не поддерживают стандарт USB.HID.POS Scanner. Наличие драйверов для определенных поставщиков уточняйте у производителя сканера штрихкодов.</p>|
| Bluetooth    | <p>Windows поддерживает сканеры штрихкодов Bluetooth на основе SPP-SSI. Список известных совместимых устройств см. в таблице ниже.</p> |

### <a name="compatible-hardware"></a>Совместимое оборудование
| Категория | Подключение | Изготовитель / модель |
|--------------|-----------|-----------|
| **Ручные сканеры 1D** | **USB** |Honeywell Voyager 1200g<br/>Honeywell Voyager 1202g<br/>Honeywell Voyager 1202-bf<br/>Honeywell Voyager 145Xg (обновляемый)|
| **Ручные сканеры 1D** | **Bluetooth** |Socket Mobile CHS 7Ci<br/> Socket Mobile CHS 7Di<br/> Socket Mobile CHS 7Mi<br/> Socket Mobile CHS 7Pi<br/>Socket Mobile DuraScan D700<br/> Socket Mobile DuraScan D730<br/>Socket Mobile SocketScan S800 (ранее CHS 8Ci) <br/>|
|**Ручные сканеры 2D** | **USB** |Code Reader™ 950<br/>Code Reader™ 1021<br/>Code Reader™ 1421<br/> Honeywell Granit 198Xi<br/>Honeywell Granit 191Xi<br/>Honeywell Xenon 1900g<br/>Honeywell Xenon 1902g<br/>Honeywell Xenon 1902g-bf<br/>Honeywell Xenon 1900h<br/>Honeywell Xenon 1902h<br/>Honeywell Voyager 145Xg (обновляемый)<br/>Honeywell Voyager 1602g<br/>Intermec SG20|
|**Ручные сканеры 2D** | **Bluetooth** |Socket Mobile SocketScan S850 (ранее CHS 8Qi)|
| **Сканеры презентации** | **USB** |Code Reader™ 5000<br/>Honeywell Genesis 7580g<br/>Honeywell Orbit 7190g|
| **Сканер счетчиков** | **USB** |Honeywell Stratos 2700|
| **Ядра сканирования** | **USB** | Honeywell N5680<br/>Honeywell N3680|
| **Мобильные устройства Windows**| **Встроенные методы** |Bluebird EF400<br/>Bluebird EF500<br/>Bluebird EF500R<br/>Honeywell CT50<br/>Honeywell D75e<br/>Janam XT2<br/>Panasonic FZ-E1<br/>Panasonic FZ-F1<br/>PointMobile PM80<br/>Zebra TC700j|
| **Мобильные устройства Windows**| **Настраиваемое** | HP Elite X3 с Barcode Scanner Jacket |

## <a name="cash-drawer"></a>Кассовый аппарат
| Подключение | Поддержка |
| -------------|-------------|
| Сеть или Bluetooth | <p> Прямое подключение к кассовому аппарату может быть выполнено по сети или через Bluetooth, в зависимости от возможностей конкретного кассового аппарата. </p>|
| Порт DK | <p> Кассовые аппараты без поддержки сети или Bluetooth можно подключить через порт DK на поддерживаемом принтере POS или периферийном устройстве Star Micronics DK-AirCash. </p>
| OPOS    | <p> Поддерживает любые кассовые аппараты, совместимые с драйверами OPOS, и/или предоставляет служебные объекты OPOS. Установите драйверы OPOS согласно инструкциям производителя конкретного устройства. Это обеспечивает подключение по USB и другие способы подключения на основе спецификаций производителя. </p> |

**Примечание.** Для получения дополнительных сведений о DK-AirCash обращайтесь в Star Micronics.

### <a name="networkbluetooth"></a>Сеть или Bluetooth
| Изготовитель |    Модели |
|--------------|-----------|
| Кассовый аппарат APG |    NetPRO, BluePRO |

## <a name="line-display"></a>Строковый дисплей
Поддерживает любые устройства со строковым дисплеем, совместимые с драйверами OPOS, и/или предоставляет служебные объекты OPOS. Установите драйверы OPOS согласно инструкциям производителя конкретного устройства.

## <a name="magnetic-stripe-reader"></a>Считыватель магнитных карт

Windows содержит встроенный драйвер класса для считывателей магнитных карт, подключаемых через USB. Драйвер разработан на основе спецификации HID POS Scanner Usage Table (8c), опубликованной на сайте [USB.org](http://www.usb.org/developers/hidpage/).

### <a name="vendor-specific-support"></a>Поддержка определенных поставщиков
Windows предоставляет поддержку для перечисленных ниже считывателей магнитных карт от Magtek и IDTech, используя их идентификатор поставщика и код продукта (VID/PID).

| Изготовитель |     Модели |    Номер детали |
|--------------|-----------|--------------|
| IDTech | SecureMag (VID:0ACD PID:2010) | IDRE-3x5xxxx |
| |    MiniMag (VID:0ACD PID:0500) |    IDMB-3x5xxxx |
| Magtek | MagneSafe (VID:0801 PID:0011) |    210730xx |
| |    Dynamag (VID:0801 PID:0002) |    210401xx |

### <a name="custom-vendor-specific"></a>Настройка конкретного поставщика
Windows поддерживает внедрение дополнительных драйверов от определенных поставщиков для поддержки дополнительных считывателей магнитных карт. Наличие дополнительных драйверов уточняйте у изготовителя считывателя магнитных карт.

## <a name="pos-printer"></a>Принтер POS
Windows поддерживает возможность печати на сетевых и подключенных по Bluetooth принтерах квитанций с использованием языка управления принтерами Epson ESC/POS. Дополнительные сведения о ESC/POS см. в разделе [Epson ESC/POS с форматированием](https://docs.microsoft.com/en-us/windows/uwp/devices-sensors/epson-esc-pos-with-formatting).

Несмотря на то, что классы, перечисления и интерфейсы в API поддерживают принтер квитанций, принтер накладных и принтер журналов, интерфейс драйвера поддерживает только принтер квитанций. Попытка использования принтера накладных или принтера журнала в настоящее время приводит к возврату состояния "не реализовано".

| Подключение | Поддержка |
| -------------|-------------|
| Сеть или Bluetooth | <p> Прямое подключение к принтеру POS возможно по сети или через Bluetooth, в зависимости от функциональности конкретного принтера POS. </p>|
| OPOS    | <p> Поддерживает все принтеры POS, совместимые с драйверами OPOS, и/или предоставляет служебные объекты OPOS. Установите драйверы OPOS согласно инструкциям производителя конкретного устройства. Это обеспечивает подключение по USB и другие способы подключения на основе спецификаций производителя. </p> |

### <a name="stationary-pos-printers-networkbluetooth"></a>Стационарные принтеры POS (сетевые, Bluetooth)
| Изготовитель |    Модели |
|--------------|-----------|
| Epson |    TM-T88V, TM-T70, TM-T20, TM-U220 |

### <a name="mobile-pos-printers-bluetooth"></a>Мобильные принтеры POS (Bluetooth)
| Изготовитель |    Модели |
|--------------|-----------|
| Epson |    Mobilink P20 (TM-P20), Mobilink P60 (TM-P60), Mobilink P80 (TM-P80) |