---
author: eliotcowley
title: Использование триггера программного обеспечения
description: Научитесь контролировать процесс сканирования из программного обеспечения.
ms.author: elcowle
ms.date: 08/29/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: Windows 10, UWP, точка обслуживания, POS
ms.localizationpriority: medium
ms.openlocfilehash: ddd8ec979cb6d5a72b48b9b8b6a60adb73c35657
ms.sourcegitcommit: 914b38559852aaefe7e9468f6f53a7465bf36e30
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/06/2018
ms.locfileid: "3394831"
---
# <a name="use-a-software-trigger"></a>Использование триггера программного обеспечения

Если вы используете сканер штрихкодов в режиме презентации или если у него нет физического триггера (как, например, его нет у сканера штрихкодов на базе камеры), имеет смысл управлять действием сканирования из программного обеспечения. Запускать процесс сканирования можно путем вызова метода [StartSoftwareTriggerAsync](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.claimedbarcodescanner.startsoftwaretriggerasync#Windows_Devices_PointOfService_ClaimedBarcodeScanner_StartSoftwareTriggerAsync).

В зависимости от значения свойства [IsDisabledOnDataReceived](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.claimedbarcodescanner.isdisabledondatareceived#Windows_Devices_PointOfService_ClaimedBarcodeScanner_IsDisabledOnDataReceived) сканер может отсканировать только один штрихкод и остановиться или сканировать постоянно, пока не будет вызван метод [StopSoftwareTriggerAsync](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.claimedbarcodescanner.stopsoftwaretriggerasync#Windows_Devices_PointOfService_ClaimedBarcodeScanner_StopSoftwareTriggerAsync).

Задайте одно из значений [IsDisabledOnDataReceived](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.claimedbarcodescanner.isdisabledondatareceived#Windows_Devices_PointOfService_ClaimedBarcodeScanner_IsDisabledOnDataReceived), чтобы задать нужное поведение сканера при декодировании штрихкода.

| Значение | Описание |
| ----- | ----------- |
| True   | Сканировать только один штрихкод, затем остановиться |
| False  | Сканировать штрихкоды без остановки |


> [!Important]
> Убедитесь, что сканер штрихкодов поддерживает использование программного триггера, проверив сначала свойство [IsSoftwareTriggerSupported](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodescannercapabilities.issoftwaretriggersupported#Windows_Devices_PointOfService_BarcodeScannerCapabilities_IsSoftwareTriggerSupported).

Следующий пример показывает, как инициировать сканирование с помощью триггера программное обеспечение, которое будет остановить сканирование после он проверяет один штрих-код:

```cs
private void SoftwareTrigger(BarcodeScanner barcodeScanner, ClaimedBarcodeScanner claimedBarcodeScanner) 
{
    if (barcodeScanner.Capabilities.IsSoftwareTriggerSupported)
    {
        claimedBarcodeScanner.IsDisabledOnDataReceived = true;
        await claimedBarcodeScanner.StartSoftwareTriggerAsync();
    }
}
```

[!INCLUDE [feedback](./includes/pos-feedback.md)]