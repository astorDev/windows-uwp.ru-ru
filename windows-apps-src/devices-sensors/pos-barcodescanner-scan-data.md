---
author: eliotcowley
title: Получение и понимать данные штрих-кода
description: Узнайте, как получить и интерпретации данных штрих-кода при сканировании.
ms.author: elcowle
ms.date: 08/29/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: Windows 10, UWP, точка обслуживания, POS
ms.localizationpriority: medium
ms.openlocfilehash: 0992ea54092063ba53f23871599905e58f1b456e
ms.sourcegitcommit: 7aa1933e6970f878faf50d59e1f799b90afd7cc7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/05/2018
ms.locfileid: "3374727"
---
# <a name="obtain-and-understand-barcode-data"></a>Получение и понимать данные штрих-кода

После настройки сканера штрих-кода, конечно необходим способ понимания данных при сканировании. При сканировании штрих-кода, возникает событие [DataReceived](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.claimedbarcodescanner.datareceived) . [ClaimedBarcodeScanner](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.claimedbarcodescanner) следует подписаться на это событие. Событие **DataReceived** передает объект [BarcodeScannerDataReceivedEventArgs](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodescannerdatareceivedeventargs) , который можно использовать для доступа к данным со штрих-кодом.

## <a name="subscribe-to-the-datareceived-event"></a>Подписаться на событие DataReceived

После **ClaimedBarcodeScanner**, имеют его подписки на событие **DataReceived** .

```cs
claimedBarcodeScanner.DataReceived += ClaimedBarcodeScanner_DataReceived;
```

Обработчик событий будет передан объект **BarcodeScannerDataReceivedEventArgs** и **ClaimedBarcodeScanner** . Данные штрих-кода можно обращаться из этого объекта [отчета](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodescannerdatareceivedeventargs.report#Windows_Devices_PointOfService_BarcodeScannerDataReceivedEventArgs_Report) свойства, которое имеет тип [BarcodeScannerReport](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodescannerreport).

```cs
private async void ClaimedBarcodeScanner_DataReceived(ClaimedBarcodeScanner sender, BarcodeScannerDataReceivedEventArgs args)
{
    // Parse the data
}
```

## <a name="get-the-data"></a>Получить данные

После **BarcodeScannerReport**, можно получить доступ к и анализа данных штрих-кода. **BarcodeScannerReport** имеет три свойства:

* [ScanData](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodescannerreport.scandata): штрих-кода полностью, необработанные данные.
* [ScanDataLabel](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodescannerreport.scandatalabel): декодированные штрих-код, который не содержит заголовок, контрольная сумма и другую информацию.
* [ScanDataType](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodescannerreport.scandatatype): тип метки декодированные штрих-кода. Возможные значения определяются в классе [BarcodeSymbologies](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodesymbologies) .

Если вы хотите получить доступ к **ScanDataLabel** или **ScanDataType**, сначала необходимо установить [IsDecodeDataEnabled](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.claimedbarcodescanner.isdecodedataenabled#Windows_Devices_PointOfService_ClaimedBarcodeScanner_IsDecodeDataEnabled) значение **true**.

```cs
claimedBarcodeScanner.IsDecodeDataEnabled = true;
```

### <a name="get-the-scan-data-type"></a>Получить тип проверки данных

Получение тип метки декодированные штрих-кода является довольно тривиальной&mdash;мы просто вызываем [GetName](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodesymbologies.getname) на **ScanDataType**.

```cs
private string GetSymbology(BarcodeScannerDataReceivedEventArgs args)
{
    return BarcodeSymbologies.GetName(args.Report.ScanDataType);
}
```

### <a name="get-the-scan-data-label"></a>Получить подпись данных сканирования

Чтобы получить расшифрованный штрих, есть несколько вещей, которые нужно иметь в виду. Только определенные типы данных содержат Кодированный текст, поэтому необходимо проверить, если символика может быть преобразован в строку, а затем преобразуйте буфера, который мы получаем из **ScanDataLabel** в зашифрованную строку UTF-8.

```cs
private string GetDataLabel(BarcodeScannerDataReceivedEventArgs args)
{
    uint scanDataType = args.Report.ScanDataType;

    // Only certain data types contain encoded text.
    // To keep this simple, we'll just decode a few of them.
    if (args.Report.ScanDataLabel == null)
    {
        return "No data";
    }

    // This is not an exhaustive list of symbologies that can be converted to a string.
    else if (scanDataType == BarcodeSymbologies.Upca ||
        scanDataType == BarcodeSymbologies.UpcaAdd2 ||
        scanDataType == BarcodeSymbologies.UpcaAdd5 ||
        scanDataType == BarcodeSymbologies.Upce ||
        scanDataType == BarcodeSymbologies.UpceAdd2 ||
        scanDataType == BarcodeSymbologies.UpceAdd5 ||
        scanDataType == BarcodeSymbologies.Ean8 ||
        scanDataType == BarcodeSymbologies.TfStd)
    {
        // The UPC, EAN8, and 2 of 5 families encode the digits 0..9
        // which are then sent to the app in a UTF8 string (like "01234").
        return CryptographicBuffer.ConvertBinaryToString(BinaryStringEncoding.Utf8, args.Report.ScanDataLabel);
    }

    // Some other symbologies (typically 2-D symbologies) contain binary data that
    // should not be converted to text.
    else
    {
        return "Decoded data unavailable.";
    }
}
```

### <a name="get-the-raw-scan-data"></a>Получения непосредственного сканирования данных

Для получения полной необработанные данные из штрих-код, мы просто преобразовать буфера, который мы получаем из **ScanData** в строку.

```cs
private string GetRawData(BarcodeScannerDataReceivedEventArgs args)
{
    // Get the full, raw barcode data.
    if (args.Report.ScanData == null)
    {
        return "No data";
    }

    // Just to show that we have the raw data, we'll print the value of the bytes.
    else
    {
        return CryptographicBuffer.ConvertBinaryToString(BinaryStringEncoding.Utf8, args.Report.ScanData);
    }
}
```

Эти данные являются, в целом, в формате, поставляемые со сканера. Заголовок и трейлер сообщения удаляются, тем не менее, так как они не содержат полезную информацию для приложения и, скорее всего, определенным сканера.

Общие сведения о заголовке является символ префикса (например символ STX). Общие сведения о трейлер является символом конца (например символ ETX или CR) и символ блока проверки, если создается сканером.

Это свойство следует включать символ символика, если один возвращаемый сканер (например, **A** для UPC-A). Оно также должно включать цифры проверки, если они присутствуют в метке и возвращенные сканера. (Заметьте, что символика символов и цифр проверки может или может отсутствовать, в зависимости от конфигурации сканера. Сканер будет возвращать их, если отсутствует, но не вычислить их, если они отсутствуют или создать.)

Некоторые товары могут быть помечены с дополнительной штрих-кода. Это штрих-код обычно размещается в правой части главного штрих-кода и состоит из дополнительных двух или пяти символов информации. Если сканер считывает рекламные материалы, содержащая основной и дополнительный штрих-кодов, дополнительные символы добавляются к основной знаков и результат будет доставлено приложению в виде одной метки. (Обратите внимание, что сканер может поддерживать конфигурацию, которая разрешает или запрещает чтение дополнительных кодов).

Некоторые товары могут быть помечены с несколько меток, иногда называют *multisymbol метки* или *многоуровневых подписей*. Эти штрих-коды обычно располагаются вертикально, а может быть из того же или разных символика. Если сканер считывает рекламные материалы, которые содержит несколько меток, доставляется каждый штрих-кода приложения как отдельные метки. Это необходимо из-за текущего отсутствие стандартизации этих типов штрих-кодов. Он не может определить все варианты на основе данных отдельных штрих-кода. Таким образом приложению необходимо определить, когда несколько штрих-код метки будет прочитана основе возвращаемых данных. (Заметьте, что сканер может или не поддерживает чтение несколько меток).

Это значение до **DataReceived** события в приложение.

[!INCLUDE [feedback](./includes/pos-feedback.md)]

## <a name="see-also"></a>См. также
* [Сканер штрихкодов](pos-barcodescanner.md)
* [Класс ClaimedBarcodeScanner](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodesymbologies.getname)
* [Класс BarcodeScannerDataReceivedEventArgs](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodescannerdatareceivedeventargs)
* [Класс BarcodeScannerReport](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodescannerreport)
* [Класс BarcodeSymbologies](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodesymbologies)