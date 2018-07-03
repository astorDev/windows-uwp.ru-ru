---
author: TerryWarwick
title: Работа с символиками сканера штрихкодов
description: Эта статья содержит сведения о символиках сканера штрихкодов.
ms.author: jken
ms.date: 05/3/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: Windows 10, UWP, точка обслуживания, POS
ms.localizationpriority: medium
ms.openlocfilehash: f6e03d62316a1b842330f39ac958e4471a895815
ms.sourcegitcommit: dc3389ef2e2c94b324872a086877314d6f963358
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/11/2018
ms.locfileid: "1874533"
---
# <a name="working-with-symbologies"></a>Работа с символиками
[Символика штрихкодов](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodesymbologies) — это сопоставление данных с конкретным форматом штрихкодов. К распространенным символикам относятся UPC, Code 128, QR Code и др. API-интерфейсы для сканеров штрихкодов в UWP позволяют приложению управлять тем, как сканер обрабатывает эти символики, без ручной настройки сканера. 

## <a name="determine-which-symbologies-are-supported"></a>Определение поддерживаемых символик 
Поскольку ваше приложение может использоваться в сочетании со сканерами штрихкодов различных моделей и различных производителей, имеет смысл запросить сканер, чтобы определить список поддерживаемых им символик.  Это удобно делать, если вашему приложению требуется определенная символика, которая может не поддерживаться всеми сканерами, или если вам нужно включить символики, отключенные на сканере вручную или программным образом.
Как только вы получите объект [BarcodeScanner](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodescanner) с помощью метода [BarcodeScanner.FromIdAsync](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodescanner.fromidasync), вызовите метод [GetSupportedSymbologiesAsync](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodescanner.getsupportedsymbologiesasync#Windows_Devices_PointOfService_BarcodeScanner_GetSupportedSymbologiesAsync), чтобы получить список символик, поддерживаемых устройством.

## <a name="determine-if-a-specific-symbology-is-supported"></a>Определение того, поддерживается ли определенная символика
Чтобы определить, поддерживает ли сканер определенную символику, можно вызвать метод [IsSymbologySupportedAsync](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodescanner.issymbologysupportedasync#Windows_Devices_PointOfService_BarcodeScanner_IsSymbologySupportedAsync_System_UInt32_)

## <a name="changing-which-symbologies-are-recognized"></a>Изменение распознаваемых символик
В некоторых случаях может потребоваться использовать подмножество символик, поддерживаемых сканером штрихкодов.  Это особенно удобно делать для блокирования символик, которые вы не планируете использовать в своем приложении. Например, чтобы гарантировать, что пользователь отсканирует нужный штрихкод, стоит ограничить сканирование символиками UPC или EAN, когда нужно получать SKU товаров, или ограничить сканирование символикой Code 128, когда стоит задача получать серийные номера.
Зная, какие символики поддерживает ваш сканер, вы можете задать символики, которые он должен распознавать.  Это можно сделать после создания объекта [ClaimedBarcodeScanner](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.claimedbarcodescanner) с помощью метода [ClaimScannerAsyc](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodescanner.claimscannerasync#Windows_Devices_PointOfService_BarcodeScanner_ClaimScannerAsync). Вы можете вызвать метод [SetActiveSymbologiesAsync](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.claimedbarcodescanner.setactivesymbologiesasync#Windows_Devices_PointOfService_ClaimedBarcodeScanner_SetActiveSymbologiesAsync_Windows_Foundation_Collections_IIterable_System_UInt32__), чтобы включить конкретный набор символик. Символики, которых нет в заданном вами списке, отключаются.

## <a name="restricting-scan-data-by-data-length"></a>Ограничение сканируемых данных по длине
Некоторым символикам, например Code 39 и Code 128, свойственна переменная длина.  Штрихкоды таких символик могут находиться рядом друг с другом и содержать разные данные, зачастую отличаясь при этом длиной. Задание конкретной длины данных, которые вы хотите получить, помогает предотвратить недействительные считывания.

| Метод    | Описание |
| :-------- | :---------- |
| [SetSymbologyAttributesAsync](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.claimedbarcodescanner.setsymbologyattributesasync#Windows_Devices_PointOfService_ClaimedBarcodeScanner_SetSymbologyAttributesAsync_System_UInt32_Windows_Devices_PointOfService_BarcodeSymbologyAttributes_) | Позволяет настроить желаемый диапазон длин декодированных данных и то, как сканер обрабатывает контрольную цифру. |
| [GetSymbologyAttributesAsync](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.claimedbarcodescanner.getsymbologyattributesasync#Windows_Devices_PointOfService_ClaimedBarcodeScanner_GetSymbologyAttributesAsync_System_UInt32_) | Позволяет получить текущую длину и конфигурации контрольных цифр. |

> [!Important] 
> Убедитесь, что сканер штрихкодов поддерживает использование атрибутов символики, проверив сначала следующие свойства: 
> - [IsDecodeLengthSupported](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodesymbologyattributes.isdecodelengthsupported#Windows_Devices_PointOfService_BarcodeSymbologyAttributes_IsDecodeLengthSupported)
> - [ICheckDigitTransmissionSupported](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodesymbologyattributes.ischeckdigittransmissionsupported#Windows_Devices_PointOfService_BarcodeSymbologyAttributes_IsCheckDigitTransmissionSupported)
> - [IsCheckDigitValidationSupported](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodesymbologyattributes.ischeckdigitvalidationsupported#Windows_Devices_PointOfService_BarcodeSymbologyAttributes_IsCheckDigitValidationSupported)
