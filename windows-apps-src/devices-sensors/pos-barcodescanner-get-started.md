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
ms.openlocfilehash: ed0fa79f5bbdfdaf8ca1f3273fa8d741f17efe1d
ms.sourcegitcommit: ab92c3e0dd294a36e7f65cf82522ec621699db87
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/03/2018
ms.locfileid: "1833330"
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

## <a name="working-with-symbologies"></a>Работа с символиками
[**Символика штрихкодов**](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodesymbologies) — это сопоставление данных с конкретным форматом штрихкодов. К распространенным символикам относятся UPC, Code 128, QR Code и др. API-интерфейсы для сканеров штрихкодов в UWP позволяют приложению управлять тем, как сканер обрабатывает эти символики, без ручной настройки сканера. 

### <a name="determine-which-symbologies-are-supported"></a>Определение поддерживаемых символик 
Поскольку ваше приложение может использоваться в сочетании со сканерами штрихкодов различных моделей и различных производителей, имеет смысл запросить сканер, чтобы определить список поддерживаемых им символик.  Это удобно делать, если вашему приложению требуется определенная символика, которая может не поддерживаться всеми сканерами, или если вам нужно включить символики, отключенные на сканере вручную или программным образом.
Как только вы получите объект [**BarcodeScanner**](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodescanner) с помощью метода [**BarcodeScanner.FromIdAsync**](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodescanner.fromidasync), вызовите метод [**GetSupportedSymbologiesAsync**](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodescanner.getsupportedsymbologiesasync#Windows_Devices_PointOfService_BarcodeScanner_GetSupportedSymbologiesAsync), чтобы получить список символик, поддерживаемых устройством.

### <a name="determine-if-a-specific-symbology-is-supported"></a>Определение того, поддерживается ли определенная символика
Чтобы определить, поддерживает ли сканер определенную символику, можно вызвать метод [**IsSymbologySupportedAsync**](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodescanner.issymbologysupportedasync#Windows_Devices_PointOfService_BarcodeScanner_IsSymbologySupportedAsync_System_UInt32_)

### <a name="changing-which-symbologies-are-recognized"></a>Изменение распознаваемых символик
В некоторых случаях может потребоваться использовать подмножество символик, поддерживаемых сканером штрихкодов.  Это особенно удобно делать для блокирования символик, которые вы не планируете использовать в своем приложении. Например, чтобы гарантировать, что пользователь отсканирует нужный штрихкод, стоит ограничить сканирование символиками UPC или EAN, когда нужно получать SKU товаров, или ограничить сканирование символикой Code 128, когда стоит задача получать серийные номера.
Зная, какие символики поддерживает ваш сканер, вы можете задать символики, которые он должен распознавать.  Это можно сделать после создания объекта [**ClaimedBarcodeScanner**](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.claimedbarcodescanner) с помощью метода [**ClaimScannerAsyc**](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodescanner.claimscannerasync#Windows_Devices_PointOfService_BarcodeScanner_ClaimScannerAsync). Вы можете вызвать метод [**SetActiveSymbologiesAsync**](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.claimedbarcodescanner.setactivesymbologiesasync#Windows_Devices_PointOfService_ClaimedBarcodeScanner_SetActiveSymbologiesAsync_Windows_Foundation_Collections_IIterable_System_UInt32__), чтобы включить конкретный набор символик. Символики, которых нет в заданном вами списке, отключаются.

### <a name="restricting-scan-data-by-data-length"></a>Ограничение сканируемых данных по длине
Некоторым символикам, например Code 39 и Code 128, свойственна переменная длина.  Штрихкоды таких символик могут находиться рядом друг с другом и содержать разные данные, зачастую отличаясь при этом длиной. Задание конкретной длины данных, которые вы хотите получить, помогает предотвратить недействительные считывания.

| Метод    | Описание |
| :-------- | :---------- |
| [**SetSymbologyAttributesAsync**](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.claimedbarcodescanner.setsymbologyattributesasync#Windows_Devices_PointOfService_ClaimedBarcodeScanner_SetSymbologyAttributesAsync_System_UInt32_Windows_Devices_PointOfService_BarcodeSymbologyAttributes_) | Позволяет настроить желаемый диапазон длин декодированных данных и то, как сканер обрабатывает контрольную цифру. |
| [**GetSymbologyAttributesAsync**](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.claimedbarcodescanner.getsymbologyattributesasync#Windows_Devices_PointOfService_ClaimedBarcodeScanner_GetSymbologyAttributesAsync_System_UInt32_) | Позволяет получить текущую длину и конфигурации контрольных цифр. |

> [!Important] 
> Убедитесь, что сканер штрихкодов поддерживает использование атрибутов символик, проверив сначала следующие свойства: [**SetSymbologyAttributesAsync**](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.claimedbarcodescanner.setsymbologyattributesasync#Windows_Devices_PointOfService_ClaimedBarcodeScanner_SetSymbologyAttributesAsync_System_UInt32_Windows_Devices_PointOfService_BarcodeSymbologyAttributes_) или [**GetSymbologyAttributesAsync**](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.claimedbarcodescanner.getsymbologyattributesasync#Windows_Devices_PointOfService_ClaimedBarcodeScanner_GetSymbologyAttributesAsync_System_UInt32_) | Позволяет получить текущую длину и конфигурации контрольных цифр. :
> - [**IsDecodeLengthSupported**](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodesymbologyattributes.isdecodelengthsupported#Windows_Devices_PointOfService_BarcodeSymbologyAttributes_IsDecodeLengthSupported)
> - [**ICheckDigitTransmissionSupported**](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodesymbologyattributes.ischeckdigittransmissionsupported#Windows_Devices_PointOfService_BarcodeSymbologyAttributes_IsCheckDigitTransmissionSupported)
> - [**IsCheckDigitValidationSupported**](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodesymbologyattributes.ischeckdigitvalidationsupported#Windows_Devices_PointOfService_BarcodeSymbologyAttributes_IsCheckDigitValidationSupported)

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
