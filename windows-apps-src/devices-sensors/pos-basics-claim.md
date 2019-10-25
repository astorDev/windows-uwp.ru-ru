---
title: Утверждение устройства PointOfService и включение модели
description: Сведения о утверждении PointOfService и включении модели
ms.date: 06/19/2018
ms.topic: article
keywords: Windows 10, UWP, точка обслуживания, POS
ms.localizationpriority: medium
ms.openlocfilehash: 0f3fc2b2aa10fedf143c55158e521b2c1cd5b75d
ms.sourcegitcommit: 6fbf645466278c1f014c71f476408fd26c620e01
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72816689"
---
# <a name="point-of-service-device-claim-and-enable-model"></a>Утверждение устройства службы и включение модели

## <a name="claiming-for-exclusive-use"></a>Утверждение для монопольного использования

После успешного создания объекта устройства PointOfService его необходимо присвоить, используя соответствующий метод присвоения для типа устройства, прежде чем вы сможете использовать устройство для ввода или вывода данных.  Присвоение предоставляет приложению монопольный доступ ко многим функциям устройства. Это позволяет обеспечить, что приложению не будет мешать использовать устройство другое приложение.  Только одно приложение одновременно может претендовать на монопольный доступ к устройству PointOfService. 

> [!Note]
> Действие утверждения устанавливает эксклюзивную блокировку для устройства, но не переводит его в рабочее состояние.  Дополнительные сведения см. в разделе [Включение устройства для операций ввода-вывода](#enable-device-for-io-operations) .

### <a name="apis-used-to-claim--release"></a>API, используемые для утверждения или выпуска

|Устройство|Присвоение | Выпуск | 
|-|:-|:-|
|BarcodeScanner | [Баркодесканнер. Клаимсканнерасинк](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodescanner.claimscannerasync) | [Клаимедбаркодесканнер. Close](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.claimedbarcodescanner.close) |
|CashDrawer | [Кашдравер. Клаимдраверасинк](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.cashdrawer.claimdrawerasync) | [Клаимедкашдравер. Close](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.claimedcashdrawer.close) | 
|LineDisplay | [Линедисплай. Клаимасинк](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.linedisplay.claimasync) |  [Клаимединедисплай. Close](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.claimedlinedisplay.close) | 
|MagneticStripeReader | [Магнетикстрипереадер. Клаимреадерасинк](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.magneticstripereader.claimreaderasync) |  [Клаимедмагнетикстрипереадер. Close](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.claimedmagneticstripereader.close) | 
|PosPrinter | [Поспринтер. Клаимпринтерасинк](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.posprinter.claimprinterasync) |  [Клаимедпоспринтер. Close](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.claimedposprinter.close) | 
 | 

## <a name="enable-device-for-io-operations"></a>Включить устройство для операций ввода-вывода

Действие утверждения просто устанавливает исключительные права на устройство, но не перемещает его в рабочее состояние.  Чтобы получить события или выполнить операции вывода, необходимо включить устройство с помощью **енаблеасинк**.  И наоборот, можно вызвать **дисаблеасинк** для прекращения прослушивания событий с устройства или выполнения выходных данных.  Можно также использовать **функцию Enable** , чтобы определить состояние устройства.

### <a name="apis-used-enable--disable"></a>Используемые API включения и отключения

| Устройство | Включить | Отключить | IsEnabled? |
|-|:-|:-|:-|
|ClaimedBarcodeScanner | [енаблеасинк](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.claimedbarcodescanner.enableasync) | [дисаблеасинк](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.claimedbarcodescanner.disableasync) | [IsEnabled](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.claimedbarcodescanner.isenabled) | 
|ClaimedCashDrawer | [енаблеасинк](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.claimedcashdrawer.enableasync) | [дисаблеасинк](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.claimedcashdrawer.disableasync) | [IsEnabled](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.claimedcashdrawer.isenabled) |
|ClaimedLineDisplay | Неприменимо ¹ | Неприменимо ¹ | Неприменимо ¹ | 
|ClaimedMagneticStripeReader | [енаблеасинк](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.claimedmagneticstripereader.enableasync) | [дисаблеасинк](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.claimedmagneticstripereader.disableasync) | [IsEnabled](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.claimedmagneticstripereader.isenabled) |  
|ClaimedPosPrinter | [енаблеасинк](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.claimedposprinter.enableasync) | [дисаблеасинк](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.claimedposprinter.disableasync) | [IsEnabled](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.claimedposprinter.isenabled) |
|

¹ линейный дисплей не требует явного включения устройства для операций ввода-вывода.  Включение выполняется автоматически с помощью интерфейсов API PointOfService Линедисплай, которые выполняют операции ввода-вывода.

## <a name="code-sample-claim-and-enable"></a>Пример кода: утверждение и включение

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

Первым шагом является создание обработчика событий, реагирующего на событие **релеаседевицерекуестед** с **ретаиндевице**.  

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

Затем зарегистрируйте обработчик событий в связи с запрошенным устройством.

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
|ClaimedBarcodeScanner | [релеаседевицерекуестед](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.claimedbarcodescanner.releasedevicerequested) | [ретаиндевице](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.claimedbarcodescanner.retaindevice)
|ClaimedCashDrawer | [релеаседевицерекуестед](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.claimedcashdrawer.releasedevicerequested) | [ретаиндевице](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.claimedcashdrawer.retaindevice)
|ClaimedLineDisplay | [релеаседевицерекуестед](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.claimedlinedisplay.releasedevicerequested) | [ретаиндевице](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.claimedlinedisplay.retaindevice)
|ClaimedMagneticStripeReader | [релеаседевицерекуестед](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.claimedmagneticstripereader.releasedevicerequested) | [ретаиндевице](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.claimedlinedisplay.retaindevice)
|ClaimedPosPrinter | [релеаседевицерекуестед](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.claimedposprinter.releasedevicerequested) | [ретаиндевице](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.claimedposprinter.retaindevice)
|
