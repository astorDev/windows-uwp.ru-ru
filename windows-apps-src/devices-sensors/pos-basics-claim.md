---
title: Утверждение и включить модели устройства PointOfService
description: Узнайте о утверждения PointOfService и включить модели
ms.date: 06/19/2018
ms.topic: article
keywords: Windows 10, UWP, точка обслуживания, POS
ms.localizationpriority: medium
ms.openlocfilehash: 7169848084b587793ba1537ea3d6ad78d31892d5
ms.sourcegitcommit: b4c502d69a13340f6e3c887aa3c26ef2aeee9cee
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/03/2018
ms.locfileid: "8485100"
---
# <a name="point-of-service-device-claim-and-enable-model"></a>Утверждение и включить модели POS-устройства

## <a name="claiming-for-exclusive-use"></a>Присвоение для исключительного использования

После успешного создания объекта устройства PointOfService его необходимо присвоить, используя соответствующий метод присвоения для типа устройства, прежде чем вы сможете использовать устройство для ввода или вывода данных.  Присвоение предоставляет приложению монопольный доступ ко многим функциям устройства. Это позволяет обеспечить, что приложению не будет мешать использовать устройство другое приложение.  Только одно приложение одновременно может претендовать на монопольный доступ к устройству PointOfService. 

> [!Note]
> Действие утверждения устанавливает монопольной блокировки на устройстве, но не помещать в рабочее состояние.  Дополнительные сведения см. [Включение устройства для операции ввода-вывода](#Enable-device-for-I/O-operations) .

### <a name="apis-used-to-claim--release"></a>Использовать API-интерфейсы для присвоения / выпуска

|Устройство|Присвоение | Выпуск | 
|-|:-|:-|
|BarcodeScanner | [BarcodeScanner.ClaimScannerAsync](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodescanner.claimscannerasync) | [ClaimedBarcodeScanner.Close](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.claimedbarcodescanner.close) |
|CashDrawer | [CashDrawer.ClaimDrawerAsync](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.cashdrawer.claimdrawerasync) | [ClaimedCashDrawer.Close](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.claimedcashdrawer.close) | 
|LineDisplay | [LineDisplay.ClaimAsync](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.linedisplay.claimasync) |  [ClaimedineDisplay.Close](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.claimedlinedisplay.close) | 
|MagneticStripeReader | [MagneticStripeReader.ClaimReaderAsync](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.magneticstripereader.claimreaderasync) |  [ClaimedMagneticStripeReader.Close](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.claimedmagneticstripereader.close) | 
|PosPrinter | [PosPrinter.ClaimPrinterAsync](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.posprinter.claimprinterasync) |  [ClaimedPosPrinter.Close](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.claimedposprinter.close) | 
 | 

## <a name="enable-device-for-io-operations"></a>Подготовьте устройство к операции ввода-вывода

Действие утверждения просто устанавливает права монопольного доступа к устройству, но не помещать в рабочее состояние.  Для получения событий или выполнения любых операций, выходные данные необходимо включить устройства с помощью **EnableAsync**.  И наоборот вы можете вызвать **DisableAsync** , чтобы остановить прослушивание события из устройства или выполнение выходных данных.  Можно также использовать **IsEnabled** для определения состояния устройства.

### <a name="apis-used-enable--disable"></a>API, используемые включить / отключить

| Устройство | Включить | Отключить | IsEnabled? |
|-|:-|:-|:-|
|ClaimedBarcodeScanner | [EnableAsync](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.claimedbarcodescanner.enableasync) | [DisableAsync](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.claimedbarcodescanner.disableasync) | [IsEnabled](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.claimedbarcodescanner.isenabled) | 
|ClaimedCashDrawer | [EnableAsync](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.claimedcashdrawer.enableasync) | [DisableAsync](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.claimedcashdrawer.disableasync) | [IsEnabled](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.claimedcashdrawer.isenabled) |
|ClaimedLineDisplay | Не Applicable¹ | Не Applicable¹ | Не Applicable¹ | 
|ClaimedMagneticStripeReader | [EnableAsync](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.claimedmagneticstripereader.enableasync) | [DisableAsync](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.claimedmagneticstripereader.disableasync) | [IsEnabled](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.claimedmagneticstripereader.isenabled) |  
|ClaimedPosPrinter | [EnableAsync](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.claimedposprinter.enableasync) | [DisableAsync](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.claimedposprinter.disableasyc) | [IsEnabled](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.claimedposprinter.isenabled) |
|

¹ строковый дисплей не требуется явным образом включить устройство для операции ввода-вывода.  Включение выполняется автоматически с помощью API LineDisplay PointOfService, которое выполнения операций ввода-вывода.

## <a name="code-sample-claim-and-enable"></a>Пример кода: утверждения и включение

В этом примере показано, как присвоить устройство со сканером штрихкодов после создания объекта сканера штрихкодов.

```Csharp

    BarcodeScanner barcodeScanner = await BarcodeScanner.FromIdAsync(DeviceId);

    if(barcodeScanner != null)
    {
        // after successful creation, claim the scanner for exclusive use 
        claimedBarcodeScanner = await barcodeScanner.ClaimScannerAsync();

        if(claimedBarcodeScanner != null)
        {
            // after successful claim, enable scanner for data events to fire
            await claimedBarcodeScanner.EnableAsync();
        }
        else
        {
            Debug.WriteLine("Failure to claim barcodeScanner");
        }
    }
    else
    {
        Debug.WriteLine("Failure to create barcodeScanner object");
    }
    
```

> [!Warning]
> Присвоение может быть утеряно при следующих условиях.
> 1. Другое приложение запросило присвоение этого же устройства, а ваше приложение не выдало **RetainDevice** в ответ на событие **ReleaseDeviceRequested**.  (Дополнительные сведения см. в разделе [Согласование присвоения](#Claim-negotiation).)
> 2. Приложение было приостановлено, что привело к закрытию объекта устройства, и в результате присвоение стало недействительным. (Дополнительные сведения см. в разделе [Жизненный цикл объекта устройства](pos-basics-deviceobject.md#device-object-lifecycle).)


## <a name="claim-negotiation"></a>Согласование присвоения

Поскольку Windows — это многозадачная среда, несколько приложений на одном и том же компьютере могут одновременно запрашивать доступ к периферийным устройствам.  API-интерфейсы PointOfService предоставляют модель согласования, которая позволяет нескольким приложениям совместно использовать периферийные устройства, подключенные к компьютеру.

Когда второе приложение на одном и том же компьютере запрашивает присвоение для периферийного устройства PointOfService, которое уже присвоено другим приложением, публикуется уведомление о событии **ReleaseDeviceRequested**. Приложение с активным присвоением должно ответить на уведомление о событии, вызвав метод **RetainDevice**, если приложение в настоящий момент использует устройство во избежание потери присвоения. 

Если приложение с активным присвоением сразу же не ответит с помощью метода **RetainDevice**, будет предполагаться, что приложение было приостановлено или не нуждается в устройстве, что приведет к отзыву присвоения и его передаче другому приложению. 

Первым шагом является создание обработчика событий, который реагирует на событие **ReleaseDeviceRequested** с **RetainDevice**.  

```Csharp
    /// <summary>
    /// Event handler for the ReleaseDeviceRequested event which occurs when 
    /// the claimed barcode scanner receives a Claim request from another application
    /// </summary>
    void claimedBarcodeScanner_ReleaseDeviceRequested(object sender, ClaimedBarcodeScanner myScanner)
    {
        // Retain exclusive access to the device
        myScanner.RetainDevice();
    }
```

Зарегистрируйте обработчик событий в связи с присвоенное устройство

```Csharp
    BarcodeScanner barcodeScanner = await BarcodeScanner.FromIdAsync(DeviceId);

    if(barcodeScanner != null)
    {
        // after successful creation, claim the scanner for exclusive use 
        claimedBarcodeScanner = await barcodeScanner.ClaimScannerAsync();

        if(claimedBarcodeScanner != null)
        {
            // register a release request handler to prevent loss of scanner during active use
            claimedBarcodeScanner.ReleaseDeviceRequested += claimedBarcodeScanner_ReleaseDeviceRequested;

            // after successful claim, enable scanner for data events to fire
            await claimedBarcodeScanner.EnableAsync();          
        }
        else
        {
            Debug.WriteLine("Failure to claim barcodeScanner");
        }
    }
    else
    {
        Debug.WriteLine("Failure to create barcodeScanner object");
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
