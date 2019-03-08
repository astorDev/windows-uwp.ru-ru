---
title: Получение и распознавание данных штрих-кода
description: Узнайте, как получать и интерпретировать данные штрих-кода при сканировании.
ms.date: 08/29/2018
ms.topic: article
keywords: Windows 10, UWP, точка обслуживания, POS
ms.localizationpriority: medium
ms.custom: RS5
ms.openlocfilehash: ece246ffd369ee21c089598f07b2566424757f55
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57605969"
---
# <a name="obtain-and-understand-barcode-data"></a>Получение и распознавание данных штрих-кода

После настройки сканер штрихкодов, само собой необходим способ понимания данных, при сканировании. Когда сканирование штрихкода, [DataReceived](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.claimedbarcodescanner.datareceived) события. [ClaimedBarcodeScanner](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.claimedbarcodescanner) должна быть подписана на это событие. **DataReceived** событие передает [BarcodeScannerDataReceivedEventArgs](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodescannerdatareceivedeventargs) объект, который можно использовать для доступа к данным штрих-кода.

## <a name="subscribe-to-the-datareceived-event"></a>Подписаться на событие DataReceived

При наличии **ClaimedBarcodeScanner**, он подписка на **DataReceived** событий:

```cs
claimedBarcodeScanner.DataReceived += ClaimedBarcodeScanner_DataReceived;
```

Обработчик событий будет передаваться **ClaimedBarcodeScanner** и **BarcodeScannerDataReceivedEventArgs** объекта. Можно обращаться из данных штрихкодов этот объект [отчетов](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodescannerdatareceivedeventargs.report#Windows_Devices_PointOfService_BarcodeScannerDataReceivedEventArgs_Report) свойство, которое имеет тип [BarcodeScannerReport](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodescannerreport).

```cs
private async void ClaimedBarcodeScanner_DataReceived(ClaimedBarcodeScanner sender, BarcodeScannerDataReceivedEventArgs args)
{
    // Parse the data
}
```

## <a name="get-the-data"></a>Получить данные

При наличии **BarcodeScannerReport**, вы можете обращаться и проанализировать данные штрихкодов. **BarcodeScannerReport** имеет три свойства:

* [ScanData](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodescannerreport.scandata): Штрихкод полных, необработанные данные.
* [ScanDataLabel](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodescannerreport.scandatalabel): Метка декодированный штрих-кода, которая не включает заголовок, контрольной суммы и другую информацию.
* [ScanDataType](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodescannerreport.scandatatype): Метка типа декодированный штрих-кода. Возможные значения определяются в [BarcodeSymbologies](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodesymbologies) класса.

Если вы хотите получить доступ к **ScanDataLabel** или **ScanDataType**, необходимо сначала установить [IsDecodeDataEnabled](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.claimedbarcodescanner.isdecodedataenabled#Windows_Devices_PointOfService_ClaimedBarcodeScanner_IsDecodeDataEnabled) для **true**.

```cs
claimedBarcodeScanner.IsDecodeDataEnabled = true;
```

### <a name="get-the-scan-data-type"></a>Получить тип проверки данных

Получение типа метки декодированный штрих-код крайне прост&mdash;мы просто вызываем [GetName](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodesymbologies.getname) на **ScanDataType**.

```cs
private string GetSymbology(BarcodeScannerDataReceivedEventArgs args)
{
    return BarcodeSymbologies.GetName(args.Report.ScanDataType);
}
```

### <a name="get-the-scan-data-label"></a>Получение эта метка данных

Чтобы получить декодированный штрих, существует несколько вещей, которые следует учитывать. Только для определенных типов данных содержат кодированного текста, поэтому необходимо проверить, если символикой можно преобразовать в строку, а затем преобразовать буфера, мы получаем от **ScanDataLabel** к закодированной строки UTF-8.

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

### <a name="get-the-raw-scan-data"></a>Получить данные сканирования

Чтобы получить полный, необработанные данные из штрих-код, мы просто преобразовать буфера, мы получаем от **ScanData** в строку.

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

Эти данные, как правило, в формате, поставляемым путем сканирования. Сведения о сообщении заголовка и окончания, удаляются, тем не менее, так как они не содержат полезные сведения для приложения и, скорее всего, должна соответствовать конкретной сканера.

Общие сведения о заголовке — это символ префикса (например, символ STX). Общие сведения о трейлер является символом конца (например, символ ETX или CR) и символ блок проверки при его создании с помощью средства проверки.

Это свойство следует использовать знак символика, если возвращается одно с помощью средства проверки (например, **объект** для UPC-A). Оно также должно включать проверку цифр, если они присутствуют в метке и возвращается с помощью средства проверки. (Обратите внимание, что символика символов и цифр проверки может, или может отсутствовать, в зависимости от конфигурации средства проверки. Средство проверки возвращает их, если отсутствует, но не вычислить их, если они отсутствуют или создать.)

Товар может быть помечены атрибутом дополнительное штрихкода. Этот штрихкод обычно помещается в правой части основной штрихкод и состоит из дополнительных два или пять символов сведения. Если сканер считывает товары, содержащая основной и дополнительный штрихкоды, дополнительные символы добавляются в основной символы и результат доставляется приложению в виде одной метки. (Обратите внимание, что сканер может поддерживать конфигурацию, которая включает или отключает считывание Дополнительные коды.)

Некоторые товары, которые могут быть помечены с несколькими метками, иногда называют *multisymbol метки* или *многоуровневые метки*. Эти штрихкоды обычно располагаются вертикально и может быть в том же или другом символикой. Если сканер считывает рекламные материалы, которые содержит несколько меток, каждый штрихкод доставляется приложению в виде отдельной метки. Это необходимо из-за текущей недостаточной стандартизации этих типов штрих-кода. Он не может определить все варианты, на основе данных отдельных штрихкода. Таким образом приложение будет необходимо определить, когда несколько штрих-код метки считывания зависимости от данных, возвращаемых. (Обратите внимание, что сканер может поддерживать или не поддерживать чтение несколько меток.)

Это значение до **DataReceived** события в приложение.

[!INCLUDE [feedback](./includes/pos-feedback.md)]

## <a name="see-also"></a>См. также
* [Сканер штрихкодов](pos-barcodescanner.md)
* [Класс ClaimedBarcodeScanner](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodesymbologies.getname)
* [Класс BarcodeScannerDataReceivedEventArgs](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodescannerdatareceivedeventargs)
* [Класс BarcodeScannerReport](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodescannerreport)
* [Класс BarcodeSymbologies](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodesymbologies)
