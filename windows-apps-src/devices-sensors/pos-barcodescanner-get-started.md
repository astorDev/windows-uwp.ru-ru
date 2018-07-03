---
author: TerryWarwick
title: Начало работы со сканерами штрихкодов
description: Узнайте, как взаимодействовать со сканером штрихкодов из универсальных приложений для Windows.
ms.author: jken
ms.date: 05/1/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: Windows 10, UWP, точка обслуживания, POS
ms.localizationpriority: medium
ms.openlocfilehash: 0fddfa3aa78c274735634315b1230b0893020805
ms.sourcegitcommit: dc3389ef2e2c94b324872a086877314d6f963358
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/11/2018
ms.locfileid: "1874202"
---
# <a name="getting-started-with-barcode-scanners"></a>Начало работы со сканерами штрихкодов

Узнайте, как взаимодействовать со сканером штрихкодов из универсальных приложений для Windows.  В этой статье содержится информация о функциональных возможностях, связанных со сканерами штрихкодов.

## <a name="configuring-your-barcode-scanner"></a>Настройка сканера штрихкодов
Настраивать сканеры штрихкодов можно в нескольких режимах.  Очень важно, чтобы сканер штрихкодов был настроен в соответствии с его предполагаемым применением.  Многие сканеры штрихкодов могут быть настроены в режиме **разрыва клавиатуры**, в результате чего Windows воспринимает сканер штрихкодов как клавиатуру.  Это позволяет сканировать штрихкоды в приложения, которые не предназначены непосредственно для работы со сканером штрихкодов, например в Блокнот.  При сканировании штрихкода в этом режиме декодированные данные со сканера штрихкодов вставляются в точку вставки, как будто бы вы ввели эти данные с клавиатуры.  Если вы хотите в большей степени контролировать сканер штрихкодов из своего приложения UWP, вам понадобится настроить его в другом режиме.

### <a name="usb-barcode-scanner"></a>Сканер штрихкодов, подключаемый по USB
Сканер штрихкодов, подключаемый по USB, должен быть настроен в режиме **Сканер POS HID**, чтобы он мог работать с драйвером сканера, входящим в состав Windows. Этот драйвер является реализацией спецификации **HID Point of Sale Usage Tables**, опубликованной на сайте [**USB-HID**](http://www.usb.org/developers/hidpage/).  Чтобы узнать, как включить сканер штрихкодов в режиме **Сканер POS HID**, обратитесь к документации сканера или свяжитесь с производителем сканера.  Будучи настроенным как **Сканер POS HID**, ваш сканер штрихкодов появится в диспетчере устройств в узле **Сканер штрихкодов пункта обслуживания** и будет называться **Сканер штрихкодов POS HID**.
У производителя сканера штрихкодов также может быть собственный драйвер, который поддерживает API-интерфейсы для сканеров штрихкодов в UWP и использует другой режим (не **Сканер POS HID**).  Если вы уже установили драйвер производителя, совместимый с API-интерфейсами для сканеров штрихкодов в UWP, вы можете увидеть в списке **Сканер штрихкодов пункта обслуживания** диспетчера устройств устройство с названием, заданным производителем устройства.

### <a name="bluetooth-barcode-scanner"></a>Сканер штрихкодов, подключаемый по Bluetooth
Сканер штрихкодов, подключаемый по Bluetooth, для работы с API-интерфейсами для сканеров штрихкодов в UWP должен быть настроен в режиме **Протокол последовательного порта - простой последовательный интерфейс (SPP-SSI)**.  Чтобы узнать, как включить сканер штрихкодов в режиме **SPP-SSI**, обратитесь к документации сканера или свяжитесь с производителем сканера.  
Прежде чем использовать сканер штрихкодов, подключаемый про Bluetooth, необходимо связать его (Параметры > Устройства > Bluetooth и другие устройства > Добавить Bluetooth или другое устройство).  
Инициировать процедуру связывания и управлять связыванием можно с помощью пространства имен **Windows.Devices.Enumeration**.  Подробнее: [**Связывание устройств**](https://docs.microsoft.com/windows/uwp/devices-sensors/pair-devices).

## <a name="using-software-trigger-with-barcode-scanners"></a>Использование программного триггера в сочетании со сканерами штрихкодов
### <a name="initiate-scan-from-software"></a>Запуск сканирования из программного обеспечения
Если вы используете сканер штрихкодов в режиме презентации или если у него нет физического триггера (как, например, его нет у сканера штрихкодов на базе камеры), имеет смысл управлять действием сканирования из программного обеспечения. Запускать процесс сканирования можно путем вызова метода [**StartSoftwareTriggerAsync**](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.claimedbarcodescanner.startsoftwaretriggerasync#Windows_Devices_PointOfService_ClaimedBarcodeScanner_StartSoftwareTriggerAsync).  
В зависимости от значения свойства [**IsDisabledOnDataReceived**](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.claimedbarcodescanner.isdisabledondatareceived#Windows_Devices_PointOfService_ClaimedBarcodeScanner_IsDisabledOnDataReceived) сканер может отсканировать только один штрихкод и остановиться или сканировать постоянно, пока не будет вызван метод [**StopSoftwareTriggerAsync**](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.claimedbarcodescanner.stopsoftwaretriggerasync#Windows_Devices_PointOfService_ClaimedBarcodeScanner_StopSoftwareTriggerAsync).

Задайте одно из значений [**IsDisabledOnDataReceived**](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.claimedbarcodescanner.isdisabledondatareceived#Windows_Devices_PointOfService_ClaimedBarcodeScanner_IsDisabledOnDataReceived), чтобы задать нужное поведение сканера при декодировании штрихкода.

| Значение | Описание |
| ----- | ----------- |
| True   | Сканировать только один штрихкод, затем остановиться |
| False  | Сканировать штрихкоды без остановки |


> [!Important]
> Убедитесь, что сканер штрихкодов поддерживает использование программного триггера, проверив сначала свойство [**IsSoftwareTriggerSupported**](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodescannercapabilities.issoftwaretriggersupported#Windows_Devices_PointOfService_BarcodeScannerCapabilities_IsSoftwareTriggerSupported).
