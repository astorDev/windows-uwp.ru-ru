---
author: TerryWarwick
title: Модель присвоения устройства PointOfService
description: Сведения о модели присвоения устройства PointOfService
ms.author: jken
ms.date: 06/4/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: Windows 10, UWP, точка обслуживания, POS
ms.localizationpriority: medium
ms.openlocfilehash: 202234530945e55ef9c0d0fb68cf9ca83d2e15c3
ms.sourcegitcommit: ce45a2bc5ca6794e97d188166172f58590e2e434
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/06/2018
ms.locfileid: "1983739"
---
# <a name="point-of-service-device-claim-model"></a>Модель присвоения POS-устройства

## <a name="claiming-a-device-for-exclusive-use"></a>Присвоение устройства для исключительного использования

После успешного создания объекта устройства PointOfService его необходимо присвоить, используя соответствующий метод присвоения для типа устройства, прежде чем вы сможете использовать устройство для ввода или вывода данных.  Присвоение предоставляет приложению монопольный доступ ко многим функциям устройства. Это позволяет обеспечить, что приложению не будет мешать использовать устройство другое приложение.  Только одно приложение одновременно может претендовать на монопольный доступ к устройству PointOfService. 

В этом примере показано, как присвоить устройство со сканером штрихкодов после создания объекта сканера штрихкодов.

```Csharp
try
{
    claimedBarcodeScanner = await barcodeScanner.ClaimScannerAsync();
}
catch (Exception ex)
{
    Debug.WriteLine("EX: ClaimScannerAsync() - " + ex.Message);
}
```

> [!Warning]
> Присвоение может быть утеряно при следующих условиях.
> 1. Другое приложение запросило присвоение этого же устройства, а ваше приложение не выдало **RetainDevice** в ответ на событие **ReleaseDeviceRequested**.  (Дополнительные сведения см. в разделе [Согласование присвоения](#Claim-negotiation).)
> 2. Приложение было приостановлено, что привело к закрытию объекта устройства, и в результате присвоение стало недействительным. (Дополнительные сведения см. в разделе [Жизненный цикл объекта устройства](pos-basics-deviceobject.md#device-object-lifecycle).)

### <a name="apis-used-for-claiming"></a>API-интерфейсы, используемые для присвоения

|Устройство|Присвоение |
|-|:-|
|BarcodeScanner | [ClaimScannerAsync](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodescanner.claimscannerasync) | 
|CashDrawer | [ClaimDrawerAsync](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.cashdrawer.claimdrawerasync) | 
|LineDisplay | [ClaimAsync](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.linedisplay.claimasync) |
|MagneticStripeReader | [ClaimReaderAsync](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.magneticstripereader.claimreaderasync) | 
|PosPrinter | [ClaimPrinterAsync](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.posprinter.claimprinterasync) | 
|

## <a name="claim-negotiation"></a>Согласование присвоения

Поскольку Windows — это многозадачная среда, несколько приложений на одном и том же компьютере могут одновременно запрашивать доступ к периферийным устройствам.  API-интерфейсы PointOfService предоставляют модель согласования, которая позволяет нескольким приложениям совместно использовать периферийные устройства, подключенные к компьютеру.

Когда второе приложение на одном и том же компьютере запрашивает присвоение для периферийного устройства PointOfService, которое уже присвоено другим приложением, публикуется уведомление о событии **ReleaseDeviceRequested**. Приложение с активным присвоением должно ответить на уведомление о событии, вызвав метод **RetainDevice**, если приложение в настоящий момент использует устройство во избежание потери присвоения. 

Если приложение с активным присвоением сразу же не ответит с помощью метода **RetainDevice**, будет предполагаться, что приложение было приостановлено или не нуждается в устройстве, что приведет к отзыву присвоения и его передаче другому приложению. 

В этом примере показано, как сохранить присвоенный сканер штрихкодов после того, как другое приложение пришлет запрос на его освобождение.  

```Csharp
claimedBarcodeScanner.ReleaseDeviceRequested += claimedBarcodeScanner_ReleaseDeviceRequested;

void claimedBarcodeScanner_ReleaseDeviceRequested(object sender, ClaimedBarcodeScanner myScanner)
{
    // Retain exclusive access to the device
    myScanner.RetainDevice();  
}
```
### <a name="apis-used-for-claim-negotiation"></a>API-интерфейсы, используемые для согласования присвоения

|Присвоенное устройство|Уведомление о выпуске| Удержание устройства |
|-|:-|:-|
|ClaimedBarcodeScanner | [ReleaseDeviceRequested](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.claimedbarcodescanner.releasedevicerequested) | [RetainDevice](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.claimedbarcodescanner.retaindevice)
|ClaimedCashDrawer | [ReleaseDeviceRequested](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.claimedcashdrawer.releasedevicerequested) | [RetainDevice](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.claimedcashdrawer.retaindevice)
|ClaimedLineDisplay | [ReleaseDeviceRequested](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.claimedlinedisplay.releasedevicerequested) | [RetainDevice](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.claimedlinedisplay.retaindevice)
|ClaimedMagneticStripeReader | [ReleaseDeviceRequested](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.claimedmagneticstripereader.releasedevicerequested) | [RetainDevice](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.claimedlinedisplay.retaindevice)
|ClaimedPosPrinter | [ReleaseDeviceRequested](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.claimedposprinter.releasedevicerequested) | [RetainDevice](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.claimedposprinter.retaindevice)
|
