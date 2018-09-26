---
author: eliotcowley
title: Получите и распознавать данные штрих-кода
description: Узнайте, как получить и интерпретации данных штрихкодов, которые вы сканирования.
ms.author: elcowle
ms.date: 08/29/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: Windows 10, UWP, точка обслуживания, POS
ms.localizationpriority: medium
ms.openlocfilehash: 0992ea54092063ba53f23871599905e58f1b456e
ms.sourcegitcommit: e4f3e1b2d08a02b9920e78e802234e5b674e7223
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/26/2018
ms.locfileid: "4211008"
---
# <a name="obtain-and-understand-barcode-data"></a>Получите и распознавать данные штрих-кода

После настройки сканера штрихкодов, конечно нужен способ для понимания данных при сканировании. При сканировании штрихкода, создается событие [DataReceived](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.claimedbarcodescanner.datareceived) . [ClaimedBarcodeScanner](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.claimedbarcodescanner) необходимо подписаться на это событие. Событие **DataReceived** передает объект [BarcodeScannerDataReceivedEventArgs](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodescannerdatareceivedeventargs) , который можно использовать для доступа к данным штрих-кода.

## <a name="subscribe-to-the-datareceived-event"></a>Подписаться на событие DataReceived

Получив **ClaimedBarcodeScanner**вас подписаться на событие **DataReceived** :

```cs
claimedBarcodeScanner.DataReceived += ClaimedBarcodeScanner_DataReceived;
```

Обработчик событий будет передаваться **ClaimedBarcodeScanner** и объект **BarcodeScannerDataReceivedEventArgs** . Доступ к данным штрихкодов можно через свойство [отчета](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodescannerdatareceivedeventargs.report#Windows_Devices_PointOfService_BarcodeScannerDataReceivedEventArgs_Report) этот объект, который имеет тип [BarcodeScannerReport](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodescannerreport).

```cs
private async void ClaimedBarcodeScanner_DataReceived(ClaimedBarcodeScanner sender, BarcodeScannerDataReceivedEventArgs args)
{
    // Parse the data
}
```

## <a name="get-the-data"></a>Получение данных

После создания **BarcodeScannerReport**, можно получить доступ к и синтаксического анализа данных штрих-кода. **BarcodeScannerReport** имеет три свойства:

* [ScanData](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodescannerreport.scandata): штрихкодов полное и необработанные данные.
* [ScanDataLabel](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodescannerreport.scandatalabel): метке расшифрованного штрих-кода, которая не включать заголовок, контрольной суммы и других данных.
* [ScanDataType](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodescannerreport.scandatatype): тип метки расшифрованного штрих-кода. Возможные значения определены в классе [BarcodeSymbologies](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodesymbologies) .

Если вы хотите получить доступ к **ScanDataLabel** или **ScanDataType**, необходимо сначала настроить [IsDecodeDataEnabled](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.claimedbarcodescanner.isdecodedataenabled#Windows_Devices_PointOfService_ClaimedBarcodeScanner_IsDecodeDataEnabled) значение **true**.

```cs
claimedBarcodeScanner.IsDecodeDataEnabled = true;
```

### <a name="get-the-scan-data-type"></a>Получить тип данных

Получив тип декодированного штрихкодов метки вполне достаточно&mdash;мы просто вызываем [GetName](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodesymbologies.getname) на **ScanDataType**.

```cs
private string GetSymbology(BarcodeScannerDataReceivedEventArgs args)
{
    return BarcodeSymbologies.GetName(args.Report.ScanDataType);
}
```

### <a name="get-the-scan-data-label"></a>Получить значок данных проверок

Для получения метки расшифрованного штрихкодов, существуют некоторые действия, которые необходимо учитывать. Только определенные типы данных содержат закодированного текста, поэтому необходимо сначала проверить, если символика можно преобразовать в строку, а затем преобразовать буфера, который мы получаем из **ScanDataLabel** закодированного строку UTF-8.

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

### <a name="get-the-raw-scan-data"></a>Получение данных необработанные проверки

Чтобы получить полный, необработанные данные из штрих-кода, мы просто преобразовать буфера, который мы получаем из **ScanData** в строку.

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

Эти данные являются, как правило, в формате, поставленные со сканера. Сведения о трейлер и заголовок сообщения будут удалены, тем не менее, так как они не содержат полезные сведения для приложения и может быть определенной сканера.

Общие сведения о заголовке является символ префикса (например, символ STX). Общие сведения о трейлера является символ конца (например, ETX или CR символов) и символ проверку блок, если он создан сканером.

Это свойство необходимо включить символ символика, если один возвращается сканером (например, **A** для UPC A). Оно также должно включать проверку цифр, если они есть в метке и возвращается сканером. (Заметьте, что символика символов и проверка цифр может или может отсутствовать, в зависимости от конфигурации сканера. Сканер будет возвращать их, если присутствует, но не расчета их, если они отсутствуют или создания.)

Некоторые товара могут быть помечены с дополнительные штрих-кода. Это штрихкодов помещается в правой части основного штрих-кода, как правило и состоит из дополнительных два бита или пять символов информации. Если сканер считывает товара, содержащий основные и дополнительные штрихкоды, добавленные дополнительные символы главными персонажами и результат передается приложению в виде одной метки. (Обратите внимание, что сканер может поддерживать конфигурацию, которая включает или отключает считывание Дополнительные коды).

Некоторые товара могут быть помечены с помощью нескольких метки, иногда называют *multisymbol метки* или *Многоуровневые метки*. Эти штрихкоды обычно упорядочены по вертикали, а также могут быть символика же или другие. Если сканер считывает товара, который содержит несколько меток, каждый штрихкодов доставляется приложению в виде отдельных метку. Это необходимо, из-за текущего отсутствие стандартизации из этих типов штрих-кода. Один не сможет определить, все варианты, на основе данных отдельных штрих-кода. Таким образом приложение необходимо определить, когда несколько штрихкодов метку была прочитана зависимости от данных, возвращаемых. (Обратите внимание, что сканер может поддерживать не считывания данных с несколькими метки.)

Это значение до **DataReceived** события в приложение.

[!INCLUDE [feedback](./includes/pos-feedback.md)]

## <a name="see-also"></a>См. также
* [Сканер штрихкодов](pos-barcodescanner.md)
* [Класс ClaimedBarcodeScanner](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodesymbologies.getname)
* [Класс BarcodeScannerDataReceivedEventArgs](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodescannerdatareceivedeventargs)
* [Класс BarcodeScannerReport](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodescannerreport)
* [Класс BarcodeSymbologies](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodesymbologies)