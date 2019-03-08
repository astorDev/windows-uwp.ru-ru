---
title: Работа с символиками сканера штрихкодов
description: Эта статья содержит сведения о символиках сканера штрихкодов.
ms.date: 08/29/2018
ms.topic: article
keywords: Windows 10, UWP, точка обслуживания, POS
ms.localizationpriority: medium
ms.openlocfilehash: 690b6b8ee688f62dcae375ed48e07797c921bf43
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57637599"
---
# <a name="working-with-symbologies"></a>Работа с символиками
[Символика штрихкодов](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodesymbologies) — это сопоставление данных с конкретным форматом штрихкодов. Некоторые распространенные symbologies включают UPC, 128 кода, QR-кода и т. д.  Сканер штрихкодов универсальной платформы Windows API-интерфейсы позволяют приложению для управления как сканер обрабатывает эти symbologies без настройки средства проверки вручную. 

## <a name="determine-which-symbologies-are-supported"></a>Определение поддерживаемых символик 
Поскольку ваше приложение может использоваться в сочетании со сканерами штрихкодов различных моделей и различных производителей, имеет смысл запросить сканер, чтобы определить список поддерживаемых им символик.  Это удобно делать, если вашему приложению требуется определенная символика, которая может не поддерживаться всеми сканерами, или если вам нужно включить символики, отключенные на сканере вручную или программным образом.

Как только вы получите объект [BarcodeScanner](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodescanner) с помощью метода [BarcodeScanner.FromIdAsync](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodescanner.fromidasync), вызовите метод [GetSupportedSymbologiesAsync](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodescanner.getsupportedsymbologiesasync#Windows_Devices_PointOfService_BarcodeScanner_GetSupportedSymbologiesAsync), чтобы получить список символик, поддерживаемых устройством.

Следующий пример возвращает список поддерживаемых symbologies сканера штрихкодов и отображает их в блок текста:

```cs
private void DisplaySupportedSymbologies(BarcodeScanner barcodeScanner, TextBlock textBlock) 
{
    var supportedSymbologies = await barcodeScanner.GetSupportedSymbologiesAsync();

    foreach (uint item in supportedSymbologies)
    {
        string symbology = BarcodeSymbologies.GetName(item);
        textBlock.Text += (symbology + "\n");
    }
}
```

## <a name="determine-if-a-specific-symbology-is-supported"></a>Определение того, поддерживается ли определенная символика
Чтобы определить, поддерживает ли сканер конкретных символика, вы можете вызвать [IsSymbologySupportedAsync](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodescanner.issymbologysupportedasync#Windows_Devices_PointOfService_BarcodeScanner_IsSymbologySupportedAsync_System_UInt32_).

В следующем примере проверяется, поддерживает ли сканер штрихкодов **Code32** символика:

```cs
bool symbologySupported = await barcodeScanner.IsSymbologySupportedAsync(BarcodeSymbologies.Code32);
```

## <a name="change-which-symbologies-are-recognized"></a>Изменить какие symbologies распознаются
В некоторых случаях может потребоваться использовать подмножество символик, поддерживаемых сканером штрихкодов.  Это особенно удобно делать для блокирования символик, которые вы не планируете использовать в своем приложении. Например, чтобы гарантировать, что пользователь отсканирует нужный штрихкод, стоит ограничить сканирование символиками UPC или EAN, когда нужно получать SKU товаров, или ограничить сканирование символикой Code 128, когда стоит задача получать серийные номера.

Зная, какие символики поддерживает ваш сканер, вы можете задать символики, которые он должен распознавать.  Это можно сделать после установки [ClaimedBarcodeScanner](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.claimedbarcodescanner) с помощью [ClaimScannerAsync](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodescanner.claimscannerasync#Windows_Devices_PointOfService_BarcodeScanner_ClaimScannerAsync). Вы можете вызвать метод [SetActiveSymbologiesAsync](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.claimedbarcodescanner.setactivesymbologiesasync#Windows_Devices_PointOfService_ClaimedBarcodeScanner_SetActiveSymbologiesAsync_Windows_Foundation_Collections_IIterable_System_UInt32__), чтобы включить конкретный набор символик. Символики, которых нет в заданном вами списке, отключаются.

В следующем примере задается active symbologies из заявленной штрихкода для [Code39](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodesymbologies.code39#Windows_Devices_PointOfService_BarcodeSymbologies_Code39) и [Code39Ex](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodesymbologies.code39ex):

```cs
private async void SetSymbologies(ClaimedBarcodeScanner claimedBarcodeScanner) 
{
    var symbologies = new List<uint>{ BarcodeSymbologies.Code39, BarcodeSymbologies.Code39Ex };
    await claimedBarcodeScanner.SetActiveSymbologiesAsync(symbologies);
}
```

## <a name="barcode-symbology-attributes"></a>Атрибуты символика штрих-кода
Symbologies выбрать другой код может иметь различные атрибуты, такие как вспомогательные несколько декодировать длинами передачи контрольного разряда на узел как часть необработанные данные и выполнить проверку из цифр. С помощью [BarcodeSymbologyAttributes](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodesymbologyattributes) класса, можно получить и задать эти атрибуты для заданного [ClaimedBarcodeScanner](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.claimedbarcodescanner) и штрихкода.

Вы можете получить атрибуты заданного символика с [GetSymbologyAttributesAsync](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.claimedbarcodescanner.getsymbologyattributesasync#Windows_Devices_PointOfService_ClaimedBarcodeScanner_GetSymbologyAttributesAsync_System_UInt32_). В следующем фрагменте кода получает атрибуты символикой Upca для **ClaimedBarcodeScanner**.

```cs
BarcodeSymbologyAttributes barcodeSymbologyAttributes = 
    await claimedBarcodeScanner.GetSymbologyAttributesAsync(BarcodeSymbologies.Upca);
```

Завершив изменение атрибутов и готовы к их настройки, можно вызвать [SetSymbologyAttributesAsync](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.claimedbarcodescanner.setsymbologyattributesasync). Этот метод возвращает **bool**, который является **true** Если атрибуты были успешно установлены.

```cs
bool success = await claimedBarcodeScanner.SetSymbologyAttributesAsync(
    BarcodeSymbologies.Upca, barcodeSymbologyAttributes);
```

### <a name="restrict-scan-data-by-data-length"></a>Ограничение длины данных данных проверки
Некоторым символикам, например Code 39 и Code 128, свойственна переменная длина.  Штрих эти symbologies может находиться рядом друг с другом содержащий различные данные часто определенной длины. Задание конкретной длины данных, которые вы хотите получить, помогает предотвратить недействительные считывания.

Прежде чем задать длину декодирования, проверьте, поддерживает ли штрихкода несколько длины с [IsDecodeLengthSupported](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodesymbologyattributes.isdecodelengthsupported#Windows_Devices_PointOfService_BarcodeSymbologyAttributes_IsDecodeLengthSupported). Если известно, что он поддерживается, можно задать [DecodeLengthKind](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodesymbologyattributes.decodelengthkind#Windows_Devices_PointOfService_BarcodeSymbologyAttributes_DecodeLengthKind), который имеет тип [BarcodeSymbologyDecodeLengthKind](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodesymbologydecodelengthkind). Это свойство может иметь любой из следующих значений:

* **AnyLength**: Декодирование длин любое число.
* **Дискретные**: Декодирование длин либо [DecodeLength1](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodesymbologyattributes.decodelength1) или [DecodeLength2](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodesymbologyattributes.decodelength2) однобайтовых символов.
* **Диапазон**: Декодирование длины между **DecodeLength1** и **DecodeLength2** однобайтовых символов. Порядок **DecodeLength1** и **DecodeLength2** имеет значения (либо может быть выше или ниже, чем другой).

Наконец, можно задать значения **DecodeLength1** и **DecodeLength2** длину данных, требуется управлять.

В следующем фрагменте кода показано задание декодирования длина:

```cs
private async Task<bool> SetDecodeLength(
    ClaimedBarcodeScanner scanner,
    uint symbology, 
    BarcodeSymbologyDecodeLengthKind kind, 
    uint decodeLength1, 
    uint decodeLength2)
{
    bool success = false;
    BarcodeSymbologyAttributes attributes = await scanner.GetSymbologyAttributesAsync(symbology);

    if (attributes.IsDecodeLengthSupported)
    {
        attributes.DecodeLengthKind = kind;
        attributes.DecodeLength1 = decodeLength1;
        attributes.DecodeLength2 = decodeLength2;
        success = await scanner.SetSymbologyAttributesAsync(symbology, attributes);
    }

    return success;
}
```

### <a name="check-digit-transmission"></a>Проверка передачи цифра

Другой атрибут, который можно установить на символика — ли контрольного разряда будет передаваться на узел в ходе необработанных данных. Прежде чем устанавливать это, убедитесь, что поддерживает проверку, символикой передачи цифр с [IsCheckDigitTransmissionSupported](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodesymbologyattributes.ischeckdigittransmissionsupported). Затем задайте отвечает за включение передачи цифра флажок с [IsCheckDigitTransmissionEnabled](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodesymbologyattributes.ischeckdigittransmissionenabled).

В следующем фрагменте кода демонстрируется параметр check цифра передачи:

```cs
private async Task<bool> SetCheckDigitTransmission(ClaimedBarcodeScanner scanner, uint symbology, bool isEnabled)
{
    bool success = false;
    BarcodeSymbologyAttributes attributes = await scanner.GetSymbologyAttributesAsync(symbology);

    if (attributes.IsCheckDigitTransmissionSupported)
    {
        attributes.IsCheckDigitTransmissionEnabled = isEnabled;
        success = await scanner.SetSymbologyAttributesAsync(symbology, attributes);
    }

    return success;
}
```

### <a name="check-digit-validation"></a>Проверьте проверки цифра

Можно также задать ли контрольного разряда штрих-код будет проверяться. Прежде чем устанавливать это, убедитесь, что поддерживает проверку, символикой проверки цифр с [IsCheckDigitValidationSupported](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodesymbologyattributes.ischeckdigitvalidationsupported). Затем задайте ли включена проверка цифра флажок с [IsCheckDigitValidationEnabled](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodesymbologyattributes.ischeckdigitvalidationenabled).

В следующем фрагменте кода демонстрируется проверка цифра флажок параметра:

```cs
private async Task<bool> SetCheckDigitValidation(ClaimedBarcodeScanner scanner, uint symbology, bool isEnabled)
{
    bool success = false;
    BarcodeSymbologyAttributes attributes = await scanner.GetSymbologyAttributesAsync(symbology);

    if (attributes.IsCheckDigitValidationSupported)
    {
        attributes.IsCheckDigitValidationEnabled = isEnabled;
        success = await scanner.SetSymbologyAttributesAsync(symbology, attributes);
    }

    return success;
}
```

[!INCLUDE [feedback](./includes/pos-feedback.md)]

## <a name="see-also"></a>См. также

* [Сканер штрихкодов](pos-barcodescanner.md)
* [Класс BarcodeSymbologies](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodesymbologies)
* [Класс BarcodeScanner](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodescanner)
* [Класс ClaimedBarcodeScanner](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.claimedbarcodescanner)
* [Класс BarcodeSymbologyAttributes](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodesymbologyattributes)
* [Перечисление BarcodeSymbologyDecodeLengthKind](https://docs.microsoft.com/uwp/api/windows.devices.pointofservice.barcodesymbologydecodelengthkind)