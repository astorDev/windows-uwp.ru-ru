---
author: eliotcowley
title: Получение и понять данные штрих-кода
description: Узнайте, как получить и интерпретации данных при сканировании штрихкода.
ms.author: elcowle
ms.date: 08/29/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: Windows 10, UWP, точка обслуживания, POS
ms.localizationpriority: medium
ms.openlocfilehash: 0992ea54092063ba53f23871599905e58f1b456e
ms.sourcegitcommit: 914b38559852aaefe7e9468f6f53a7465bf36e30
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/06/2018
ms.locfileid: "3393450"
---
# <a name="obtain-and-understand-barcode-data"></a>Получение и понять данные штрих-кода

После настройки сканера штрихкодов, конечно, необходимо способ Общие сведения о данных, которые вы сканирования. При сканировании штрихкода, создается событие [DataReceived](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.claimedbarcodescanner.datareceived) . [ClaimedBarcodeScanner](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.claimedbarcodescanner) необходимо подписаться на это событие. Событие **DataReceived** передает объект [BarcodeScannerDataReceivedEventArgs](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodescannerdatareceivedeventargs) , который можно использовать для доступа к данным штрих-кода.

## <a name="subscribe-to-the-datareceived-event"></a>Подписаться на событие DataReceived

Получив **ClaimedBarcodeScanner**ею подписаться на событие **DataReceived** :

```cs
claimedBarcodeScanner.DataReceived += ClaimedBarcodeScanner_DataReceived;
```

Обработчик событий будет передаваться **ClaimedBarcodeScanner** и **BarcodeScannerDataReceivedEventArgs** объект. Данные штрих-кода можно получить с помощью свойства этого объекта [отчета](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodescannerdatareceivedeventargs.report#Windows_Devices_PointOfService_BarcodeScannerDataReceivedEventArgs_Report) , которое имеет тип [BarcodeScannerReport](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodescannerreport).

```cs
private async void ClaimedBarcodeScanner_DataReceived(ClaimedBarcodeScanner sender, BarcodeScannerDataReceivedEventArgs args)
{
    // Parse the data
}
```

## <a name="get-the-data"></a>Получение данных

Получив **BarcodeScannerReport**, можно получить доступ к и анализа данных штрихкодов. **BarcodeScannerReport** имеет три свойства:

* [ScanData](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodescannerreport.scandata): штрихкодов полное и необработанные данные.
* [ScanDataLabel](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodescannerreport.scandatalabel): метке расшифрованного штрих-кода, которая не содержит заголовок, контрольной суммы и других данных.
* [ScanDataType](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodescannerreport.scandatatype): тип метки расшифрованного штрих-кода. Возможные значения определены в классе [BarcodeSymbologies](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodesymbologies) .

Если вы хотите получить доступ к **ScanDataLabel** или **ScanDataType**, необходимо сначала настроить [IsDecodeDataEnabled](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.claimedbarcodescanner.isdecodedataenabled#Windows_Devices_PointOfService_ClaimedBarcodeScanner_IsDecodeDataEnabled) значение **true**.

```cs
claimedBarcodeScanner.IsDecodeDataEnabled = true;
```

### <a name="get-the-scan-data-type"></a>Получить тип данных

Получение тип декодированного штрихкодов метки вполне достаточно&mdash;мы просто вызовем метод [GetName](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodesymbologies.getname) на **ScanDataType**.

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

### <a name="get-the-raw-scan-data"></a>Получение данных необработанные сканирования

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

Эти данные являются, как правило, в формате, который доставляется из сканера. Сведения о трейлер и заголовок сообщения будут удалены, тем не менее, так как они не содержат полезные сведения для приложения и, скорее всего, будет определенной сканера.

Общие сведения о заголовке является символ префикса (например, символ STX). Общие сведения о трейлера является символ конца (например, ETX или CR символов) и символ блок флажок, если один создается сканером.

Это свойство должно включать символика символ, если один возвращается сканером (например, **A** для UPC A). Оно также должно включать цифры флажок, если они есть в метке и возвращенная сканера. (Обратите внимание, может содержать символика символов и проверка цифр или может отсутствовать, в зависимости от конфигурации сканера. Сканер будет возвращать их, если присутствует, но не вычислить их, если они отсутствуют или создания.)

Некоторые товара могут быть помечены дополнительные штрихкодов. Это штрихкодов помещается справа от основного штрих-кода, как правило и состоит из дополнительных два бита или пять символов информации. Если сканер считывает товара, содержащий основные и дополнительные штрих-кодов, дополнительные символы добавляются в конец главными персонажами и результат передается приложению в виде одной метки. (Обратите внимание, что сканер может поддерживать конфигурацию, которая включает или отключает считывание Дополнительные коды).

Некоторые товара могут быть помечены несколько метками, иногда называют *multisymbol метки* или *Многоуровневые метки*. Эти штрихкоды обычно упорядочиваются по вертикали, а также могут быть символика одного и того же или другие. Если сканер считывает товара, который содержит несколько меток, каждый штрихкодов доставляются приложению в виде отдельных метку. Это необходимо из-за текущего отсутствие стандартизации из этих типов штрихкодов. Он не может определить все варианты на основе данных отдельных штрих-кода. Таким образом приложение будет необходимо определить, когда несколько штрихкодов метки была прочитана основе возвращенными данными. (Обратите внимание, что сканер может или не поддерживает чтение несколько меток.)

Это значение до **DataReceived** события в приложение.

[!INCLUDE [feedback](./includes/pos-feedback.md)]

## <a name="see-also"></a>См. также
* [Сканер штрихкодов](pos-barcodescanner.md)
* [Класс ClaimedBarcodeScanner](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodesymbologies.getname)
* [Класс BarcodeScannerDataReceivedEventArgs](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodescannerdatareceivedeventargs)
* [Класс BarcodeScannerReport](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodescannerreport)
* [Класс BarcodeSymbologies](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodesymbologies)